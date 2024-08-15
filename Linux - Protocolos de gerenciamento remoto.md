No mundo das distribuições Linux, há muitas maneiras de gerenciar os servidores remotamente. Por exemplo, vamos imaginar que estamos em um dos muitos locais e um de nossos funcionários que acabou de ir a um cliente em outra cidade precisa de nossa ajuda por causa de um erro que ele não consegue resolver. A solução de problemas eficiente parecerá difícil por telefone na maioria dos casos, então é benéfico se soubermos como fazer logon no sistema remoto para gerenciá-lo.

## ssh
[O Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) ( `SSH`) permite que dois computadores estabeleçam uma conexão criptografada e direta dentro de uma rede possivelmente insegura na porta padrão `TCP 22`.

Uma vantagem do SSH é que o protocolo é executado em todos os sistemas operacionais comuns. Como é originalmente um aplicativo Unix, ele também é implementado nativamente em todas as distribuições Linux e MacOS.O SSH também pode ser usado no Windows, desde que instalemos um programa apropriado.

O conhecido servidor [OpenBSD SSH](https://www.openssh.com/) ( `OpenSSH`) em distribuições Linux é um fork de código aberto do `SSH`servidor original e comercial da SSH Communication Security. Consequentemente, existem dois protocolos concorrentes: `SSH-1`e `SSH-2`.

`SSH-2`, também conhecido como SSH versão 2, é um protocolo mais avançado que o SSH versão 1 em criptografia, velocidade, estabilidade e segurança. Por exemplo, `SSH-1`é vulnerável a `MITM`ataques, enquanto o SSH-2 não é.

No total, o OpenSSH tem seis métodos de autenticação diferentes:

1. Autenticação de senha
2. Autenticação de chave pública
3. Autenticação baseada em host
4. Autenticação de teclado
5. Autenticação de desafio-resposta
6. Autenticação GSSAPI

#### Autenticação de chave pública
Em uma primeira etapa, o servidor SSH e o cliente se autenticam um ao outro. O servidor envia um certificado ao cliente para verificar se é o servidor correto. Somente quando o contato é estabelecido pela primeira vez é que há o risco de um terceiro se interpor entre os dois participantes e, assim, interceptar a conexão. Como o certificado em si também é criptografado, ele não pode ser imitado. Uma vez que o cliente saiba o certificado correto, ninguém mais pode fingir fazer contato por meio do servidor correspondente.

Após a autenticação do servidor, no entanto, o cliente também deve provar ao servidor que tem autorização de acesso. No entanto, o servidor SSH já está de posse do valor hash criptografado da senha definida para o usuário desejado. Como resultado, os usuários precisam digitar a senha toda vez que fizerem logon em outro servidor durante a mesma sessão. Por esse motivo, uma opção alternativa para autenticação do lado do cliente é o uso de um par de chaves pública e privada.

A chave privada é armazenada exclusivamente em nosso próprio computador e sempre permanece secreta.

As chaves públicas também são armazenadas no servidor. O servidor cria um problema criptográfico com a chave pública do cliente e a envia para o cliente. O cliente, por sua vez, descriptografa o problema com sua própria chave privada, envia de volta a solução e, assim, informa ao servidor que ele pode estabelecer uma conexão legítima.

### Configuração padrão
O arquivo [sshd_config](https://www.ssh.com/academy/ssh/sshd_config) , responsável pelo servidor OpenSSH, tem apenas algumas das configurações definidas por padrão. No entanto, a configuração padrão inclui o encaminhamento X11, que continha uma vulnerabilidade de injeção de comando na versão 7.2p1 do OpenSSH em 2016. No entanto, não precisamos de uma GUI para gerenciar nossos servidores.

#### /etc/ssh/sshd_config
```txt
Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```
A maioria das configurações neste arquivo de configuração são comentadas e exigem configuração manual.

### Configurações perigosas
Apesar do protocolo SSH ser um dos protocolos mais seguros disponíveis hoje, algumas configurações incorretas ainda podem tornar o servidor SSH vulnerável a ataques fáceis de executar. Vamos dar uma olhada nas seguintes configurações:

| **Contexto**                 | **Descrição**                                    |
| ---------------------------- | ------------------------------------------------ |
| `PasswordAuthentication yes` | Permite autenticação baseada em senha.           |
| `PermitEmptyPasswords yes`   | Permite o uso de senhas vazias.                  |
| `PermitRootLogin yes`        | Permite efetuar login como usuário root.         |
| `Protocol 1`                 | Usa uma versão desatualizada de criptografia.    |
| `X11Forwarding yes`          | Permite encaminhamento X11 para aplicativos GUI. |
| `AllowTcpForwarding yes`     | Permite o encaminhamento de portas TCP.          |
| `PermitTunnel`               | Permite tunelamento.                             |
| `DebianBanner yes`           | Exibe um banner específico ao efetuar login.     |

Permitir autenticação de senha nos permite usar força bruta em um nome de usuário conhecido para possíveis senhas. Muitos métodos diferentes podem ser usados ​​para adivinhar as senhas dos usuários. Para esse propósito, `patterns`geralmente são usados ​​métodos específicos para alterar as senhas mais comumente usadas e, assustadoramente, corrigi-las

## Footprinting the Service
Uma das ferramentas que podemos usar para fazer a impressão digital do servidor SSH é [o ssh-audit](https://github.com/jtesta/ssh-audit) . Ele verifica a configuração do lado do cliente e do lado do servidor e mostra algumas informações gerais e quais algoritmos de criptografia ainda são usados ​​pelo cliente e pelo servidor.

### Auditoria SSH
```sh
NycolasES6@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
NycolasES6@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] 
...SNIP...
```

Para possíveis ataques de força bruta, podemos especificar o método de autenticação com a opção de cliente SSH `PreferredAuthentications`.

```sh
NycolasES6@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```

Por padrão, os banners começam com a versão do protocolo que pode ser aplicada e, em seguida, a versão do próprio servidor. Por exemplo, com `SSH-1.99-OpenSSH_3.9p1`, sabemos que podemos usar ambas as versões de protocolo SSH-1 e SSH-2, e estamos lidando com a versão 3.9p1 do servidor OpenSSH. Por outro lado, para um banner com `SSH-2.0-OpenSSH_8.2p1`, estamos lidando com uma versão 8.2p1 do OpenSSH que aceita apenas a versão do protocolo SSH-2.

## Rsync

[O Rsync](https://linux.die.net/man/1/rsync) é uma ferramenta rápida e eficiente para copiar arquivos local e remotamente. Ele é altamente versátil e conhecido por seu algoritmo de transferência delta. Este algoritmo reduz a quantidade de dados transmitidos pela rede quando uma versão do arquivo já existe no host de destino.

Ele é frequentemente usado para backups e espelhamento. Ele encontra arquivos que precisam ser transferidos observando os arquivos que mudaram de tamanho ou o último horário de modificação. Por padrão, ele usa porta `873`e pode ser configurado para usar SSH para transferências seguras de arquivos, pegando carona em cima de uma conexão de servidor SSH estabelecida.

Este [guia](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync) aborda algumas das maneiras pelas quais o Rsync pode ser abusado, principalmente listando o conteúdo de uma pasta compartilhada em um servidor de destino e recuperando arquivos. Às vezes, isso pode ser feito sem autenticação.

#### Scanning for Rsync
```sh
NycolasES6@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)
```

#### Procurando por ações acessíveis
Agora podemos testar um pouco o serviço para ver o que podemos acessar.

```sh
NycolasES6@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

## Enumerando compartilhamento aberto
Aqui podemos ver um compartilhamento chamado `dev` e podemos enumerá-lo melhor.

```sh
NycolasES6@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh
```

Da saída acima, podemos ver alguns arquivos interessantes que podem valer a pena puxar para baixo para investigar mais a fundo. Também podemos ver que um diretório provavelmente contendo chaves SSH está acessível. A partir daqui, poderíamos sincronizar todos os arquivos para nosso host de ataque com o comando `rsync -av rsync://127.0.0.1/dev`.

Se o Rsync estiver configurado para usar SSH para transferir arquivos, poderíamos modificar nossos comandos para incluir o sinalizador `-e ssh`, ou `-e "ssh -p2222"` se uma porta não padrão estiver em uso para SSH. Este [guia](https://phoenixnap.com/kb/how-to-rsync-over-ssh) é útil para entender a sintaxe para usar Rsync sobre SSH.

## R-Services
R-Services são um conjunto de serviços hospedados para habilitar acesso remoto ou emitir comandos entre hosts Unix via TCP/IP.

`R-services`abrangem as portas `512`, `513`, e `514`e são acessíveis somente por meio de um conjunto de programas conhecido como `r-commands`.O conjunto [de comandos R](https://en.wikipedia.org/wiki/Berkeley_r-commands) consiste nos seguintes programas:

- rcp ( `remote copy`)
- executar ( `remote execution`)
- rlogin ( `remote login`)
- rsh ( `remote shell`)
- rstat
- tempo de ruptura
- rquem ( `remote who`)

 A tabela abaixo fornecerá uma rápida visão geral dos comandos mais frequentemente abusados, incluindo o daemon de serviço com o qual eles interagem, sobre qual porta e método de transporte para os quais eles podem ser acessados, e uma breve descrição de cada um.

|**Comando**|**Daemon de serviço**|**Porta**|**Protocolo de Transporte**|**Descrição**|
|---|---|---|---|---|
|`rcp`|`rshd`|514|TCP|Copie um arquivo ou diretório bidirecionalmente do sistema local para o sistema remoto (ou vice-versa) ou de um sistema remoto para outro. Funciona como o `cp`comando no Linux, mas fornece `no warning to the user for overwriting existing files on a system`.|
|`rsh`|`rshd`|514|TCP|Abre um shell em uma máquina remota sem um procedimento de login. Depende das entradas confiáveis ​​nos arquivos `/etc/hosts.equiv`e `.rhosts`para validação.|
|`rexec`|`rexecd`|512|TCP|Habilita um usuário a executar comandos shell em uma máquina remota. Requer autenticação por meio do uso de um `username`e `password`por meio de um soquete de rede não criptografado. A autenticação é substituída pelas entradas confiáveis ​​nos arquivos `/etc/hosts.equiv`e `.rhosts`.|
|`rlogin`|`rlogind`|513|TCP|Permite que um usuário faça login em um host remoto pela rede. Ele funciona de forma semelhante, `telnet`mas só pode se conectar a hosts do tipo Unix. A autenticação é substituída pelas entradas confiáveis ​​nos arquivos `/etc/hosts.equiv`e `.rhosts`.|

O arquivo /etc/hosts.equiv contém uma lista de hosts confiáveis ​​e é usado para conceder acesso a outros sistemas na rede. Quando usuários em um desses hosts tentam acessar o sistema, eles recebem acesso automaticamente sem autenticação adicional.

#### /etc/hosts.equiv
```shell-session
NycolasES6@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

Agora que temos uma compreensão básica do `r-commands`, vamos fazer uma rápida análise de pegadas `Nmap`para determinar se todas as portas necessárias estão abertas.

#### Procurando por R-Services
```shell-session
NycolasES6@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```

#### Controle de acesso e relacionamentos confiáveis
Os serviços R dependem de informações confiáveis ​​enviadas do cliente remoto para a máquina host na qual estão tentando se autenticar. Por padrão, esses serviços utilizam [Módulos de Autenticação Plugáveis ​​(PAM)](https://debathena.mit.edu/trac/wiki/PAM) para autenticação do usuário em um sistema remoto; no entanto, eles também ignoram essa autenticação por meio do uso dos arquivos `/etc/hosts.equiv`e `.rhosts`no sistema.

Os `hosts.equiv`arquivos e `.rhosts`contêm uma lista de hosts ( `IPs`ou `Hostnames`) e usuários que são `trusted`pelo host local quando uma tentativa de conexão é feita usando `r-commands`. As entradas em qualquer arquivo podem aparecer como o seguinte:

>**Observação:** o `hosts.equiv`arquivo é reconhecido como a configuração global referente a todos os usuários em um sistema, enquanto `.rhosts`fornece uma configuração por usuário.

#### Exemplo de arquivo .rhosts
```sh
NycolasES6@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```

Como podemos ver neste exemplo, ambos os arquivos seguem a sintaxe específica de pares `<username> <ip address>`ou `<username> <hostname>`. Além disso, o `+`modificador pode ser usado dentro desses arquivos como um curinga para especificar qualquer coisa.

 Neste exemplo, o `+`modificador permite que qualquer usuário externo acesse r-commands da `htb-student`conta de usuário por meio do host com o endereço IP `10.0.17.10`.

 Agora que entendemos como podemos potencialmente abusar de configurações incorretas nesses arquivos, vamos tentar fazer login em um host de destino usando `rlogin`.

#### Efetuando login usando Rlogin
```sh
NycolasES6@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```

Fizemos login com sucesso na conta `htb-student` no host remoto devido às configurações incorretas no `.rhosts`arquivo. Depois de fazer login com sucesso, também podemos abusar do comando `rwho` para listar todas as sessões interativas na rede local enviando solicitações para a porta UDP 513.

#### Listando usuários autenticados usando Rwho
```sh
NycolasES6@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25    
```

A partir dessas informações, podemos ver que o `htb-student`usuário está atualmente autenticado no `workstn01`host, enquanto o `root`usuário está autenticado no `web01`host.No entanto, o `rwho`daemon transmite periodicamente informações sobre usuários conectados, então pode ser benéfico observar o tráfego de rede.

#### Listando usuários autenticados usando Rusers
Para fornecer informações adicionais em conjunto com `rwho`, podemos emitir o `rusers`comando. Isso nos dará um relato mais detalhado de todos os usuários logados na rede, incluindo informações como nome de usuário, nome do host da máquina acessada, TTY em que o usuário está logado, e etc.

```sh
NycolasES6@htb[/htb]$ rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25
```















































---
## Preferências

https://book.hacktricks.xyz/v/portugues-ht/network-services-pentesting/873-pentesting-rsync

https://phoenixnap.com/kb/how-to-rsync-over-ssh












































