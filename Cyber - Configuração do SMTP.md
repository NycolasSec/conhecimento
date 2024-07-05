Despois de saber para que seve o SMTP ([[Cyber - Descrição SMTP]])podemos ir para aas configurações

## Configuração padrão

Cada servidor SMTP pode ser configurado de muitas maneiras, assim como todos os outros serviços. No entanto, há diferenças porque o servidor SMTP é responsável apenas por enviar e encaminhar e-mails.

#### Configuração padrão

```bash
cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"
```

```txt
smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

O envio e a comunicação também são feitos por comandos especiais que fazem com que o servidor SMTP faça o que o usuário solicita.

| **Comando**  | **Descrição**                                                                                      |
| ------------ | -------------------------------------------------------------------------------------------------- |
| `AUTH PLAIN` | AUTH é uma extensão de serviço usada para autenticar o cliente.                                    |
| `HELO`       | O cliente efetua login com seu nome de computador e assim inicia a sessão.                         |
| `MAIL FROM`  | O cliente nomeia o remetente do e-mail.                                                            |
| `RCPT TO`    | O cliente nomeia o destinatário do e-mail.                                                         |
| `DATA`       | O cliente inicia a transmissão do e-mail.                                                          |
| `RSET`       | O cliente aborta a transmissão iniciada, mas mantém a conexão entre cliente e servidor.            |
| `VRFY`       | O cliente verifica se uma caixa de correio está disponível para transferência de mensagens.        |
| `EXPN`       | O cliente também verifica se uma caixa de correio está disponível para mensagens com este comando. |
| `NOOP`       | O cliente solicita uma resposta do servidor para evitar a desconexão devido ao tempo limite.       |
| `QUIT`       | O cliente encerra a sessão.                                                                        |

Para interagir com o servidor SMTP, podemos usar a `telnet`ferramenta para inicializar uma conexão TCP com o servidor SMTP. A inicialização real da sessão é feita com o comando mencionado acima, `HELO`ou `EHLO`.

#### Telnet - HELO/EHLO

```bash
telnet 10.129.14.128 25
```

```txt
Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

### Telnet - VRFY

O comando `VRFY`pode ser usado para enumerar usuários existentes no sistema. No entanto, isso nem sempre funciona. Dependendo de como o servidor SMTP está configurado, o servidor SMTP pode emitir `code 252`e confirmar a existência de um usuário que não existe no sistema. Uma lista de todos os códigos de resposta SMTP pode ser encontrada [aqui](https://serversmtp.com/smtp-error/) .

```bash
telnet 10.129.14.128 25
```

```txt
Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Portanto, nunca se deve confiar inteiramente nos resultados de ferramentas automáticas. Afinal, elas executam comandos pré-configurados, mas nenhuma das funções declara explicitamente como o administrador configura o servidor testado.

> Às vezes, podemos ter que trabalhar por meio de um proxy da web. Também podemos fazer com que esse proxy da web se conecte ao servidor SMTP. O comando que enviaríamos seria algo como isto:`CONNECT 10.129.14.128:25 HTTP/1.0`

Todos os comandos que inserimos na linha de comando para enviar um e-mail que conhecemos de todos os programas clientes de e-mail, como Thunderbird, Gmail, Outlook e muitos outros. Especificamos o `subject`, para quem o e-mail deve ir, CC, BCC e as informações que queremos compartilhar com os outros. Claro, o mesmo funciona na linha de comando.

### Enviar um e-mail

```bash
telnet 10.129.14.128 25
```

```txt

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server


EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING


MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok


RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok


DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB


QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```

O cabeçalho do e-mail tem muitas informações importantes. Entre outras coisas, ele fornece informações sobre o remetente e o destinatário, o horário de envio e chegada, as estações pelas quais o e-mail passou em seu caminho, o conteúdo e o formato da mensagem, e o remetente e o destinatário.

Algumas são obrigatórias e outras são opcionais.

No entanto, o cabeçalho do e-mail não contém nenhuma informação necessária para entrega técnica.

Tanto o remetente quanto o destinatário podem acessar o cabeçalho de um e-mail, embora ele não seja visível à primeira vista. A estrutura de um cabeçalho de e-mail é definida pelo [RFC5322](https://datatracker.ietf.org/doc/html/rfc5322) .

## Configurações perigosas

Para evitar que os e-mails enviados sejam filtrados como spam e não cheguem ao destinatário, o remetente pode usar um servidor de retransmissão em que o destinatário confia. É um servidor SMTP que é conhecido e verificado por todos os outros. Como regra, o remetente deve se autenticar no servidor de retransmissão antes de usá-lo.

Normalmente administradores não sabem quais intervalos de IP devem permitir. Isso resulta em uma configuração incorreta no servidor SMTP.

No entanto eles permitem que todos os endereços IP não causem erros no tráfego de e-mail e, assim, não perturbem ou interrompam a comunicação de clientes.

### Open Relay Configuration

```shell
mynetworks = 0.0.0.0/0
```

Com essa configuração, esse servidor SMTP pode enviar e-mails falsos e, assim, inicializar a comunicação entre várias partes. Outra possibilidade de ataque seria falsificar o e-mail e lê-lo.

### Footprinting the service

Os scripts Nmap padrão incluem `smtp-commands`, que usa o `EHLO`comando para listar todos os comandos possíveis que podem ser executados no servidor SMTP de destino.

#### Nmap

```shell
sudo nmap 10.129.14.128 -sC -sV -p25
```

```txt
PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)
```

No entanto, também podemos usar o script NSE [smtp-open-relay](https://nmap.org/nsedoc/scripts/smtp-open-relay.html) para identificar o servidor SMTP de destino como um open relay usando 16 testes diferentes.

#### Nmap - Open Relay

```shell
sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v
```



















































