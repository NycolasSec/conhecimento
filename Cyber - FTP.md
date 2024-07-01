
O FTP é executado na camada de aplicação da pilha de protocolos TCP/IP. Portanto, está na mesma camada que `HTTP`ou `POP`.

Esses protocolos também funcionam com o suporte de navegadores ou clientes de e-mail para a realização de seus serviços.

Em uma conexão FTP, dois canais são abertos. Primeiro, o cliente e o servidor estabelecem um canal de controle por meio de `TCP port 21`. O cliente envia comandos para o servidor, e o servidor retorna códigos de status. Então, ambos os participantes da comunicação podem estabelecer o canal de dados por meio de `TCP port 20`.

Este canal é usado exclusivamente para transmissão de dados, e o protocolo observa erros durante esse processo. Se uma conexão for interrompida durante a transmissão, o transporte pode ser retomado após o contato restabelecido.

Na variante ativa, o cliente estabelece a conexão através da porta TCP 21 e, assim, informa ao servidor através de qual porta do lado do cliente o servidor pode transmitir suas respostas. No entanto, se um firewall proteger o cliente, o servidor não poderá responder porque todas as conexões externas estão bloqueadas.

Para isso `passive mode`foi desenvolvido o. Aqui, o servidor anuncia uma porta através da qual o cliente pode estabelecer o canal de dados. Como o cliente inicia a conexão nesse método, o firewall não bloqueia a transferência.

O FTP conhece diferentes [comandos](https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/) e códigos de status. Nem todos esses comandos são implementados de forma consistente no servidor.O servidor responde em cada caso com um código de status que indica se o comando foi implementado com sucesso. Uma lista de possíveis códigos de status pode ser encontrada [aqui](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes) .

Também precisamos saber que o FTP é um `clear-text`protocolo que às vezes pode ser detectado se as condições da rede forem adequadas. No entanto, também existe a possibilidade de um servidor oferecer arquivos `anonymous FTP`.

O operador do servidor permite então que qualquer usuário carregue ou baixe arquivos via FTP sem usar uma senha.

## TFTP

`Trivial File Transfer Protocol(TFTP)` é mais simples que o FTP e realiza transferências de arquivos entre processos cliente e servidor. Mas não fornece autenticação autenticação e outros recursos valiosos suportados  por FTP. Além disso, enquanto o FTP usa o TCP, o TFTP usa o `UDP`, tornando-o um protocolo não confiável e fazendo com que use recuperação de camada de aplicativo assistida por `UDP`.

Ele não suporta login protegido por senha e estabelece limites de acesso com base apenas nas permissões de leitura e gravação de um arquivo no sistema operacional.

Devido à falta de segurança, o TFTP, ao contrário do FTP, só pode ser utilizado em redes locais e protegidas.

### Alguns comando TFTP

| **Comandos** | **Descrição**                                                                                                                                           |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `connect`    | Define o host remoto e, opcionalmente, a porta para transferências de arquivos.                                                                         |
| `get`        | Transfere um arquivo ou conjunto de arquivos do host remoto para o host local.                                                                          |
| `put`        | Transfere um arquivo ou conjunto de arquivos do host local para o host remoto.                                                                          |
| `quit`       | Sai do tftp.                                                                                                                                            |
| `status`     | Mostra o status atual do tftp, incluindo o modo de transferência atual (ascii ou binário), status da conexão, valor de tempo limite e assim por diante. |
| `verbose`    | Ativa ou desativa o modo detalhado, que exibe informações adicionais durante a transferência de arquivos.                                               |

TFPTP não possui funcionalidade de listagem de diretórios.

## Configuração padrão

A configuração padrão do vsFTPd pode ser encontrada em `/etc/vsftpd.conf`e algumas configurações já estão predefinidas por padrão.

É altamente recomendável instalar o servidor vsFTPd em uma VM e dar uma olhada mais de perto nesta configuração.

### Instalar vsFTPd

```bash
sudo apt install vsftpd
```

### Arquivo de configuração vsFTPd

```bash
cat /etc/vsftpd.conf | grep -v "#"
```

| Contexto                                                      | Descrição                                                                                                   |
| ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `listen=NO`                                                   | Executar a partir do inetd ou como um daemon independente?                                                  |
| `listen_ipv6=YES`                                             | Ouvir em IPv6?                                                                                              |
| `anonymous_enable=NO`                                         | Ativar acesso anônimo?                                                                                      |
| `local_enable=YES`                                            | Permitir que usuários locais façam login?                                                                   |
| `dirmessage_enable=YES`                                       | Exibir mensagens do diretório ativo quando os usuários acessam determinados diretórios?                     |
| `use_localtime=YES`                                           | Usar a hora local?                                                                                          |
| `xferlog_enable=YES`                                          | Ativar registro de uploads/downloads?                                                                       |
| `connect_from_port_20=YES`                                    | Conectar pela porta 20?                                                                                     |
| `secure_chroot_dir=/var/run/vsftpd/empty`                     | Nome de um diretório vazio                                                                                  |
| `pam_service_name=vsftpd`                                     | Esta string é o nome do serviço PAM que o vsftpd usará.                                                     |
| `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`          | As últimas três opções especificam o local do certificado RSA a ser usado para conexões criptografadas SSL. |
| `rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key` |                                                                                                             |
| `ssl_enable=NO`                                               |                                                                                                             |

Existe um arquivo `/etc/ftpusers` que serve para negar a determinados usuários o acesso ao serviço FTP.

 No exemplo a seguir, os usuários `guest`, `john`e `kevin`não têm permissão para efetuar login no serviço FTP, mesmo que existam no sistema Linux.

### Usuários FTP

```bash
cat /etc/ftpusers

#guest
#john
#kevin
```

### Configurações perigosas

Existem muitas configurações diferentes relacionadas á segurança que podemos fazer no servidor FTP. Eles podem ter vários propósitos, como testar conexões através de firewalls, testar rotas e mecanismos de autenticação.

Um deles é o login com o usuário anônimo, que normalmente é usado para permitir que todos na rede interna compartilhem arquivos e dados sem acessar os computadores uns dos outros. Com vsFTPd, as [configurações opcionais](http://vsftpd.beasts.org/vsftpd_conf.html) que podem ser adicionadas ao arquivo de configuração para o login anônimo são assim:

| **Contexto**                   | **Descrição**                                                                   |
| ------------------------------ | ------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Permitir login anônimo?                                                         |
| `anon_upload_enable=YES`       | Permitindo upload de arquivos anônimos?                                         |
| `anon_mkdir_write_enable=YES`  | Permitindo que anônimos criem novos diretórios?                                 |
| `no_anon_password=YES`         | Não peça senha anônima?                                                         |
| `anon_root=/home/username/ftp` | Diretório para anônimo.                                                         |
| `write_enable=YES`             | Permitir o uso dos comandos FTP: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE e SITE? |

Assim que nos conectamos ao servidor vsFTPd, `response code 220`é exibido com o banner do servidor FTP. Freqüentemente, esse banner contém a descrição `service`e até mesmo a descrição `version`dele.

### Login anônimo

```bash
ftp 10.129.14.136

#Connected to 10.129.14.136.
#220 "Welcome to the HTB Academy vsFTP service."
#Name (10.129.14.136:cry0l1t3): anonymous

#230 Login successful.
#Remote system type is UNIX.
#Using binary mode to transfer files.


ftp> ls

#200 PORT command successful. Consider using PASV.
#150 Here comes the directory listing.
#-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
#drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
#drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
#drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
#-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
#226 Directory send OK.

```

Porém, para obter um primeira visão geral das configurações do servidor, podemos usar o seguinte.:

### Status vsFTPd

```bash
ftp> status

#Connected to 10.129.14.136.
#No proxy connection.
#Connecting using address family: any.
#Mode: stream; Type: binary; Form: non-print; Structure: file
#Verbose: on; Bell: off; Prompting: on; Globbing: on
#Store unique: off; Receive unique: off
#Case: off; CR stripping: on
#Quote control characters: on
#Ntrans: off
#Nmap: off
#Hash mark printing: off; Use of PORT cmds: on
#Tick counter printing: off
```

Alguns comandos nos mostram informações que podemos utilizar para nossos propósitos, como `debug` e `trace`.

### Saída detalhada vsFTPd

```bash
ftp> debug

#Debugging on (debug=1).

ftp> trace

#Packet tracing on.

ftp> ls

#---> PORT 10,10,14,4,188,195
#200 PORT command successful. Consider using PASV.
#---> LIST
#150 Here comes the directory listing.
#-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
#drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
#drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
#drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
#-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
#226 Directory send OK.
```

| **Contexto**              | **Descrição**                                                                     |
| ------------------------- | --------------------------------------------------------------------------------- |
| `dirmessage_enable=YES`   | Mostrar uma mensagem quando eles entrarem pela primeira vez em um novo diretório? |
| `chown_uploads=YES`       | Alterar a propriedade de arquivos enviados anonimamente?                          |
| `chown_username=username` | Usuário que recebe a propriedade dos arquivos enviados anonimamente.              |
| `local_enable=YES`        | Permitir que usuários locais façam login?                                         |
| `chroot_local_user=YES`   | Colocar usuários locais em seu diretório inicial?                                 |
| `chroot_list_enable=YES`  | Usar uma lista de usuários locais que serão colocados em seu diretório inicial?   |

| **Contexto**            | **Descrição**                                                                                   |
| ----------------------- | ----------------------------------------------------------------------------------------------- |
| `hide_ids=YES`          | Todas as informações de usuários e grupos nas listagens de diretório serão exibidas como "ftp". |
| `ls_recurse_enable=YES` | Permite o uso de listagens recursivas.                                                          |

No exemplo a seguir, podemos ver que se a `hide_ids=YES`configuração estiver presente, a representação UID e GUID do serviço será sobrescrita, dificultando a identificação com quais direitos esses arquivos são gravados e carregados.

### Ocultando IDs - SIM

```bash
ftp> ls

#---> TYPE A
#200 Switching to ASCII mode.
#ftp: setsockopt (ignored): Permission denied
#---> PORT 10,10,14,4,223,101
#200 PORT command successful. Consider using PASV.
#---> LIST
#150 Here comes the directory listing.
#-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
#drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
#drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
#drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
#-rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt
#-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
#226 Directory send OK.
```

Esta configuração é um recurso de segurança para evitar que nomes de usuários locais sejam revelados. Com os nomes de usuário, poderíamos atacar serviços como FTP e SSH e muitos outros com um ataque de força bruta, em teoria, no entanto as soluções `fail2ban` são uma implementação padrão de qualquer infra-estrutura que registra o endereço IP e bloqueia todo o aceso após um certo número de tentativas de login falhas.

Outra configuração útil que podemos usar para nossos propósitos é a extensão `ls_recurse_enable=YES`.
 Pois nos permite ver todo o conteúdo visível de uma só vez.

### Listagem Recursiva

```bash
ftp> ls -R

---> PORT 10,10,14,4,222,149
#200 PORT command successful. Consider using PASV.

---> LIST -R
#150 Here comes the directory listing.
#.:
#-rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx
#drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients
#drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents
#drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees
#-rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt
#-rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt

#./Clients:
#drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox
#drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight

#./Clients/HackTheBox:
#-rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx
#-rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx
#-rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf
#-rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt

#./Clients/Inlanefreight:
#-rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx
#-rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx
#-rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt
#-rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx

#./Documents:
#-rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx
#-rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx
#-rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf

#./Employees:
#226 Directory send OK.

```

Baixar arquivos desse servidor FTP é um dos principais recursos, assim como subir arquivos criados por nós. Isso nos permite, por exemplo, usar vulnerabilidades LFI para fazer o host executar comandos do sistema. Ataques também são possíveis com logs de FTP, levando a `Remote Command Execution`( `RCE`).

### Baixar um arquivo

```bash
ftp> ls

#200 PORT command successful. Consider using PASV.
#150 Here comes the directory listing.
#-rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx
#drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients
#drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents
#drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees
#-rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt
#226 Directory send OK.

ftp> get Important\ Notes.txt

#local: Important Notes.txt remote: Important Notes.txt
#200 PORT command successful. Consider using PASV.
#150 Opening BINARY mode data connection for Important Notes.txt (41 bytes).
#226 Transfer complete.
#41 bytes received in 0.00 secs (606.6525 kB/s)

ftp> exit

#221 Goodbye.
```

```bash
ls | grep Notes.txt

'Important Notes.txt'
```

Também podemos baixar todos os arquivos e pastas aos quais temos acesso de uma só vez, mas isto pode causar alarmes, pois ninguém da empresa deseja baixar todos os arquivos de uma vez.

### Baixe todos os arquivos disponíveis

```bash
NycolasES6@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136

```

Depois de baixar todos os arquivos, o `wget` criará um diretório com o nome do endereço IP do nosso alvo.

A atitude de que os componentes internos da rede não podem ser acessados ​​externamente significa que o fortalecimento dos sistemas internos é frequentemente negligenciado e leva a configurações incorretas.

A capacidade de fazer upload de arquivos para o servidor FTP conectado a um servidor web aumenta a probabilidade de obter acesso direto ao servidor web e até mesmo a um shell reverso que nos permite executar comandos internos do sistema e talvez até aumentar nossos privilégios.

### Enviar um arquivo

```bash
touch testupload.txt
```

Com o comando ``put``, podemos enviar arquivos da pasta atual para o servidor FTP.

```bash
ftp> put testupload.txt

#local: testupload.txt remote: testupload.txt
#---> PORT 10,10,14,4,184,33
#200 PORT command successful. Consider using PASV.
#---> STOR testupload.txt
#150 Ok to send data.
#226 Transfer complete.
```

## Footprinting do serviço

O Nmap também traz o [Nmap Scripting Engine](https://nmap.org/book/nse.html) ( `NSE`), um conjunto de diversos scripts escritos para serviços específicos

Podemos atualizar este banco de dados de scripts NSE com o comando mostrado.

## Script FTP do Nmap

```bash
sudo nmap --script-updatedb
```

Em nossos sistemas podemos encontrar os scripts usando um comando simples em nosso sistema.

```bash
find / -type f -name ftp* 2>/dev/null | grep scripts

#/usr/share/nmap/scripts/ftp-syst.nse
#/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
#/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
#/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
#/usr/share/nmap/scripts/ftp-bounce.nse
#/usr/share/nmap/scripts/ftp-libopie.nse
#/usr/share/nmap/scripts/ftp-anon.nse
#/usr/share/nmap/scripts/ftp-brute.nse
```

Como já sabemos, o servidor FTP geralmente roda na porta TCP 21 padrão, que podemos verificar usando o Nmap. Também usamos a verificação de versão ( `-sV`), a verificação agressiva ( `-A`) e a verificação de script padrão ( `-sC`) em nosso alvo `10.129.14.136`.

### Nmap

```bash
sudo nmap -sV -p21 -sC -A 10.0129.14.136
```

Depois que o Nmap detecta o serviço, ele executa os scripts marcados um após o outro, fornecendo informações diversas. Por exemplo, o script [ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html) NSE verifica se o servidor FTP permite acesso anônimo. Nesse caso, o conteúdo do diretório raiz do FTP será renderizado para o usuário anônimo.

O `ftp-syst`, por exemplo, executa o comando comando, que exibe informações sobre o status do servidor FTP.

O Nmap também oferece a capacidade de rastrear o progresso dos scripts NSE no nível da rede se usarmos a `--script-trace`opção em nossas varreduras.Isso nos permite ver quais comandos o Nmap envia, quais portas são usadas e quais respostas recebemos do servidor verificado.

### Rastreamento de script Nmap

```bash
sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace
```

Saída:
```bash
Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST                                                                                                                                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]             
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #1 [10.129.14.136:21] (timeout: 7000ms) EID 42
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #3 [10.129.14.136:21] (timeout: 7000ms) EID 58
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #4 [10.129.14.136:21] (timeout: 11000ms) EID 66
NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.
```

### interação de serviço

```bash
nc -nv 10.129.14.136 21
```

```bash
telnet 10.129.14.136 21
```

Parece um pouco diferente se o servidor FTP for executado com criptografia TLS/SSL. Pois precisaremos de um cliente que possa lidar com TLS/SSL.

Para isso, podemos usar o cliente `openssl` e nos comunicar com o servidor FTP. O bom de usar `openssl` é que podemos ver o certificado SSL.

```bash
openssl s_client -connect 10.129.14.136:21 -starttls ftp

#...SNIP...
#-----BEGIN CERTIFICATE-----

#MIIENTCCAx2gAwIBAgIUD+SlFZAWzX5yLs2q3ZcfdsRQqMYwDQYJKoZIhvcNAQEL
#...SNIP...
```


Isto porque o certificado SSL permite-nos reconhecer o `hostname`, por exemplo, e na maioria dos casos também um `email address`para a organização ou empresa

Além disso, caso a empresa possua diversas localidades no mundo, também podem ser criados certificados para localidades específicas, que também podem ser identificadas através do certificado SSL.


































































