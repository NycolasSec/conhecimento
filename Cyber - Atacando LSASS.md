O LSASS é um serviço crítico que desempenha um papel central no gerenciamento de credenciais e nos processos de autenticação em todos os sistemas operacionais Windows.

![[Pasted image 20240906092303.png]]

No login inicial, o LSASS irá:
- Credenciais de cache localmente na memória
- Criar [tokens de acesso](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- Aplicar políticas de segurança
- Escrever no [log de segurança do Windows](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

Vamos abordar algumas das técnicas e ferramentas que podemos usar para despejar memória LSASS e extrair credenciais de um destino executando o Windows.

## Despejando memória do processo LSASS
Para atacar o processo LSASS de forma semelhante ao banco de dados SAM, é recomendável criar um dump de memória do LSASS. Isso permite extrair credenciais offline, oferecendo mais flexibilidade e eficiência no ataque, além de reduzir o tempo gasto no sistema alvo. Existem vários métodos para gerar esse dump de memória.

#### Método do Gerenciador de Tarefas
Com acesso a uma sessão gráfica interativa com o alvo, podemos usar o gerenciador de tarefas para criar um dump de memória. Isso requer que:

![[Pasted image 20240906092750.png]]
`Open Task Manager`> `Select the Processes tab`> `Find & right click the Local Security Authority Process`>`Select Create dump file`

Um arquivo chamado `lsass.DMP`é criado e salvo em:
```cmd-session
C:\Users\loggedonusersdirectory\AppData\Local\Temp
```
Este é o arquivo que transferiremos para nosso host de ataque.

#### Método Rundll32.exe e Comsvcs.dll
Essa maneira é mais rápida do que o método do Gerenciador de Tarefas e mais flexível porque podemos obter uma sessão de shell em um host Windows com acesso apenas à linha de comando. É importante observar que as ferramentas antivírus modernas reconhecem esse método como atividade maliciosa.

Precisamos determinar qual ID de processo ( `PID`) é atribuído a `lsass.exe`. Isso pode ser feito a partir do ``cmd`` ou do ``PowerShell``:

#### Encontrando LSASS PID em cmd
No cmd, podemos emitir o comando `tasklist /svc`e encontrar lsass.exe e seu ID de processo no campo PID.

```cmd-session
C:\Windows\system32> tasklist /svc
```
![[Pasted image 20240906093200.png]]

#### Encontrando LSASS PID no PowerShell
No PowerShell, podemos emitir o comando `Get-Process lsass`e ver o ID do processo no `Id`campo.

```powershell-session
PS C:\Windows\system32> Get-Process lsass
```
![[Pasted image 20240906093242.png]]

Depois de atribuir o PID ao processo LSASS, podemos criar o arquivo de despejo.

#### Criando lsass.dmp usando PowerShell
Com uma sessão elevada do PowerShell, podemos emitir o seguinte comando para criar o arquivo de despejo:
```powershell-session
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

Com este comando, estamos executando `rundll32.exe`para chamar uma função exportada `comsvcs.dll`que também chama a função MiniDumpWriteDump (`MiniDump`) para despejar a memória do processo LSASS em um diretório especificado ( `C:\lsass.dmp`).

Se conseguirmos executar esse comando e gerar o `lsass.dmp`arquivo, podemos prosseguir com a transferência do arquivo para nossa caixa de ataque para tentar extrair quaisquer credenciais que possam ter sido armazenadas na memória do processo LSASS.

## Usando Pypykatz para extrair credenciais
Depois de obter o arquivo de despejo da memória do LSASS no nosso host de ataque, podemos usar a ferramenta `pypykatz` para extrair credenciais do arquivo `.dmp`.

O `pypykatz` é uma implementação do Mimikatz em Python, permitindo sua execução em sistemas Linux, ao contrário do Mimikatz original, que só funciona em Windows. Isso é vantajoso porque evita a necessidade de executar o Mimikatz diretamente no alvo ou em um host Windows. O `pypykatz` permite trabalhar offline com o arquivo de despejo, que contém um "instantâneo" das credenciais ativas no momento do despejo, facilitando a extração de informações de logon.

#### Executando Pypykatz
O comando `pypykatz lsass` é utilizado para analisar os segredos no dump de memória do processo LSASS. O `lsass` refere-se ao Local Security Authority Subsystem Service, e o comando especifica que o arquivo de dump é um ``minidump``, fornecendo o caminho para o arquivo de dump (por exemplo, `/home/peter/Documents/lsass.dmp`). O `pypykatz` processa o arquivo de dump e revela as credenciais e outros segredos armazenados na memória do LSASS.

```shell-session
NycolasRamos@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp 
```
![[Pasted image 20240906094006.png]]

Vamos dar uma olhada mais detalhada em algumas das informações úteis na saída.

#### MSV
```shell-session
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
```

[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) é um pacote de autenticação no Windows que o LSA chama para validar tentativas de logon no banco de dados SAM. Pypykatz extraiu os hashes de senha `SID`, `Username`, `Domain`e até mesmo `NT`& `SHA1`associados à sessão de logon da conta de usuário bob.

#### Wdigest
```shell-session
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
```

`WDIGEST`é um protocolo de autenticação mais antigo habilitado por padrão em `Windows XP`- `Windows 8`e `Windows Server 2003`- `Windows Server 2012`. O LSASS armazena em cache credenciais usadas pelo WDIGEST em texto não criptografado.

A Microsoft lançou uma atualização de segurança para sistemas afetados por esse problema com o WDIGEST. Podemos estudar os detalhes dessa atualização de segurança [aqui](https://msrc-blog.microsoft.com/2014/06/05/an-overview-of-kb2871997/) .

#### Kerberos
```shell-session
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```
**Kerberos** é um protocolo de autenticação do **Active Directory** que emite tickets para acesso a recursos sem a necessidade de repetida autenticação. O **LSASS** armazena em cache senhas, chaves e **tickets** associados ao Kerberos, que podem ser extraídos da memória do processo LSASS para acesso a outros sistemas no domínio.

#### APIDP
```shell-session
== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```
A ``DPAPI`` (Data Protection Application Programming Interface) é uma API do Windows que permite a criptografia e descriptografia de dados por usuário, protegendo informações para o sistema operacional e aplicativos de terceiros.

Aqui estão apenas alguns exemplos de aplicativos que usam DPAPI e para que eles o usam:

| Aplicações                  | Uso do DPAPI                                                                                            |
| --------------------------- | ------------------------------------------------------------------------------------------------------- |
| `Internet Explorer`         | Dados de preenchimento automático do formulário de senha (nome de usuário e senha para sites salvos).   |
| `Google Chrome`             | Dados de preenchimento automático do formulário de senha (nome de usuário e senha para sites salvos).   |
| `Outlook`                   | Senhas para contas de e-mail.                                                                           |
| `Remote Desktop Connection` | Credenciais salvas para conexões com máquinas remotas.                                                  |
| `Credential Manager`        | Credenciais salvas para acessar recursos compartilhados, ingressar em redes sem fio, VPNs e muito mais. |
``Mimikatz`` e ``Pypykatz`` podem extrair o DPAPI `masterkey`para o usuário logado cujos dados estão presentes na memória do processo LSASS.

#### Quebrando o NT Hash com Hashcat
Agora podemos usar o Hashcat para quebrar o NT Hash. Após definir o modo no comando, podemos colar o hash, especificar uma lista de palavras e, então, quebrar o hash.

```shell-session
NycolasRamos@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```
![[Pasted image 20240906095242.png]]





































