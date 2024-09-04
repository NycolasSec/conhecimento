Os aplicativos que utilizam mecanismos de autenticação comparam as credenciais fornecidas com bancos de dados, que podem ser locais ou remotos. No caso dos bancos de dados locais, essas credenciais ficam armazenadas diretamente no sistema. Aplicativos da web, por sua vez, podem ser vulneráveis a injeções de SQL, o que pode permitir que invasores acessem todos os dados de uma organização em texto simples.

Um exemplo notável é a lista de senhas "rockyou.txt", que contém cerca de 14 milhões de senhas únicas. Esta lista surgiu após uma violação de dados na empresa RockYou, onde 32 milhões de contas de usuários foram comprometidas devido ao armazenamento de credenciais em texto simples e a um ataque de injeção de SQL bem-sucedido.

Além disso, todos os sistemas operacionais suportam mecanismos de autenticação e, portanto, armazenam as credenciais localmente. Em seguida, é importante entender como essas credenciais são criadas, armazenadas e gerenciadas em sistemas operacionais como Windows e Linux.

## Linux
Sistemas Linux lidam com tudo na forma de arquivos, portanto as senhas também são armazenadas em arquivo. Este arquivo é o `/etc/shadow`. Além disso, essas senhas são comumente armazenadas na forma de `hashes`. Um exemplo pode ser assim:

**/etc/shadow**
```txt
...SNIP...
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```

O `/etc/shadow`arquivo tem um formato exclusivo no qual as entradas são inseridas e salvas quando novos usuários são criados.

|   |   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|---|
|htb-student:|$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:|18955:|0:|99999:|7:|:|:|:|
|`<username>`:|`<encrypted password>`:|`<day of last change>`:|`<min age>`:|`<max age>`:|`<warning period>`:|`<inactivity period>`:|`<expiration date>`:|`<reserved field>`|

A criptografia da senha neste arquivo é formatada da seguinte forma:

| `$ <id>` | `$ <salt>` | `$ <hashed>`                  |
| -------- | ---------- | ----------------------------- |
| `$ y`    | `$ j9T`    | `$ 3QSBB6CbHEu...SNIP...f8Ms` |

O tipo ( `id`) é o método de hash criptográfico usado para criptografar a senha. Muitos métodos de hash criptográfico diferentes foram usados ​​no passado e ainda são usados ​​por alguns sistemas hoje.

| **EU IA** | **Algoritmo de Hash Criptográfico**                                   |
| --------- | --------------------------------------------------------------------- |
| `$1$`     | [MD5](https://en.wikipedia.org/wiki/MD5)                              |
| `$2a$`    | [BlowFish](https://en.wikipedia.org/wiki/Blowfish_(cipher))           |
| `$5$`     | [SHA-256](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$6$`     | [SHA-512](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$sha1$`  | [SHA1crypt](https://en.wikipedia.org/wiki/SHA-1)                      |
| `$y$`     | [Yescriptografar](https://github.com/openwall/yescrypt)               |
| `$gy$`    | [Gost-yescript](https://www.openwall.com/lists/yescrypt/2019/06/30/1) |
| `$7$`     | [Yscript](https://en.wikipedia.org/wiki/Scrypt)                       |

No passado, a senha criptografada era armazenada junto com o nome de usuário no `/etc/passwd`arquivo, mas isso foi cada vez mais reconhecido como um problema de segurança porque o arquivo pode ser visualizado por todos os usuários no sistema. O `/etc/shadow`arquivo só pode ser lido pelo usuário `root`.

#### Arquivo de senha

```shell-session
NycolasRamos@htb[/htb]$ cat /etc/passwd

...SNIP...
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash
```


|                |               |          |          |              |                      |                                   |
| -------------- | ------------- | -------- | -------- | ------------ | -------------------- | --------------------------------- |
| `htb-student:` | `x:`          | `1000:`  | `1000:`  | `,,,:`       | `/home/htb-student:` | `/bin/bash`                       |
| `<username>:`  | `<password>:` | `<uid>:` | `<gid>:` | `<comment>:` | `<home directory>:`  | `<cmd executed after logging in>` |

Um `x` na senha indica que ela está armazenada no `/etc/shadow`. Portanto, um campo vazio significa que podemos efetuar login com o nome de usuário sem inserir uma senha.

## Processo de autenticação do Windows

O processo de autenticação no Windows é mais complexo do que em sistemas Linux, envolvendo vários módulos que gerenciam logon, recuperação e verificação de credenciais. Um exemplo de complexidade é a autenticação Kerberos. A Autoridade de Segurança Local (LSA) é um subsistema protegido que autentica usuários e gerencia a segurança local do computador, incluindo a manutenção de informações de segurança e a tradução entre nomes e IDs de segurança (SIDs).

O subsistema de segurança rastreia políticas e contas em um sistema. Em Controladores de Domínio, essas políticas e contas se aplicam ao domínio e são armazenadas no Active Directory. Além disso, a LSA verifica o acesso a objetos, permissões de usuário e gera mensagens de monitoramento, assegurando a integridade das operações de segurança.

#### Diagrama do processo de autenticação do Windows
![[Pasted image 20240902111000.png]]

O logon interativo local é realizado pela interação entre o processo de logon ( [WinLogon](https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8) ), o processo de interface de usuário de logon ( `LogonUI`), o `credential providers`, `LSASS`, um ou mais `authentication packages`, e `SAM`ou `Active Directory`. Pacotes de autenticação, neste caso, são as Dynamic-Link Libraries ( `DLLs`) que realizam verificações de autenticação. Por exemplo, para logins interativos e não associados a domínios, o pacote de autenticação `Msv1_0.dll`é usado.

`Winlogon`é um processo confiável responsável por gerenciar interações de usuários relacionadas à segurança. Elas incluem:

- Iniciando o LogonUI para inserir senhas no login
- Alterando senhas
- Bloqueio e desbloqueio da estação de trabalho

Ele depende de provedores de credenciais instalados no sistema para obter o nome de conta ou senha de um usuário. Provedores de credenciais são `COM`objetos localizados em DLLs.

O Winlogon é o único processo que intercepta solicitações de login do teclado enviadas por uma mensagem RPC do Win32k.sys. O Winlogon inicia imediatamente o aplicativo LogonUI no logon para exibir a interface do usuário para logon. Depois que o Winlogon obtém um nome de usuário e senha dos provedores de credenciais, ele chama o LSASS para autenticar o usuário que tenta fazer login.

#### LSASS
[O Local Security Authority Subsystem Service](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) ( `LSASS`) é uma coleção de muitos módulos e tem acesso a todos os processos de autenticação que podem ser encontrados em `%SystemRoot%\System32\Lsass.exe`. Este serviço é responsável pela política de segurança do sistema local, autenticação do usuário e envio de logs de auditoria de segurança para o `Event log`.

|**Pacotes de autenticação**|**Descrição**|
|---|---|
|`Lsasrv.dll`|O serviço LSA Server aplica políticas de segurança e atua como o gerenciador de pacotes de segurança para o LSA. O LSA contém a função Negotiate, que seleciona o protocolo NTLM ou Kerberos após determinar qual protocolo deve ser bem-sucedido.|
|`Msv1_0.dll`|Pacote de autenticação para logons de máquinas locais que não exigem autenticação personalizada.|
|`Samsrv.dll`|O Security Accounts Manager (SAM) armazena contas de segurança locais, aplica políticas armazenadas localmente e oferece suporte a APIs.|
|`Kerberos.dll`|Pacote de segurança carregado pelo LSA para autenticação baseada em Kerberos em uma máquina.|
|`Netlogon.dll`|Serviço de logon baseado em rede.|
|`Ntdsa.dll`|Esta biblioteca é usada para criar novos registros e pastas no registro do Windows.|

Cada sessão de logon interativa cria uma instância separada do serviço Winlogon. A arquitetura [Graphical Identification and Authentication](https://docs.microsoft.com/en-us/windows/win32/secauthn/gina) ( `GINA`) é carregada na área de processo usada pelo Winlogon, recebe e processa as credenciais e invoca as interfaces de autenticação por meio da função [LSALogonUser](https://docs.microsoft.com/en-us/windows/win32/api/ntsecapi/nf-ntsecapi-lsalogonuser) .

#### Banco de dados SAM
O **Security Account Manager (SAM)** é um banco de dados nos sistemas operacionais Windows que armazena senhas de usuários em formato hash, como LMhash ou NTLMhash. Esse arquivo é utilizado para autenticar usuários locais e remotos e está localizado em `%SystemRoot%/system32/config/SAM`, sendo montado no registro em `HKLM/SAM`. Para acessá-lo, são necessárias permissões de nível **SYSTEM**.

Nos sistemas Windows, os computadores podem ser configurados para pertencer a um grupo de trabalho ou a um domínio. Se o computador for parte de um grupo de trabalho, ele gerencia o banco de dados SAM localmente, armazenando os dados dos usuários no próprio sistema. No entanto, se o computador estiver em um domínio, as credenciais são validadas pelo **Controlador de Domínio (DC)**, que utiliza o banco de dados do **Active Directory (ntds.dit)**, localizado em `%SystemRoot%\ntds.dit`.

Para aumentar a segurança do banco de dados SAM, a Microsoft introduziu no Windows NT 4.0 o recurso **SYSKEY (syskey.exe)**, que criptografa parcialmente o arquivo SAM no disco rígido, protegendo os hashes de senha armazenados contra ataques de cracking offline.

#### Gerenciador de Credenciais
![[Pasted image 20240902111829.png]]
O Credential Manager é um recurso integrado a todos os sistemas operacionais Windows que permite que os usuários salvem as credenciais que usam para acessar vários recursos de rede e sites. As credenciais salvas são armazenadas com base nos perfis de usuário em cada usuário `Credential Locker`. As credenciais são criptografadas e armazenadas no seguinte local:

```powershell-session
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\
```

## DST
É muito comum encontrar ambientes de rede onde os sistemas Windows são unidos a um domínio Windows.

Nesses casos, os sistemas Windows enviarão todas as solicitações de logon para os Controladores de Domínio que pertencem à mesma floresta do Active Directory. Cada Controlador de Domínio hospeda um arquivo chamado `NTDS.dit`que é mantido sincronizado em todos os Controladores de Domínio, com exceção dos [Controladores de Domínio Somente Leitura](https://docs.microsoft.com/en-us/windows/win32/ad/rodc-and-active-directory-schema) .

NTDS.dit é um arquivo de banco de dados que armazena os dados no Active Directory, incluindo, mas não se limitando a:

- Contas de usuário (hash de nome de usuário e senha)
- Contas de grupo
- Contas de computador
- Objetos de política de grupo





































































