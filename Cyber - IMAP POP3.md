Com a ajuda do `Internet Message Access Protocol`( `IMAP`), o acesso a e-mails de um servidor de e-mail é possível.Ao contrário do `Post Office Protocol`( `POP3`), o IMAP permite o gerenciamento on-line de e-mails diretamente no servidor e suporta estruturas de pastas.

Portanto, protocolos como o IMAP devem ser usados ​​para funcionalidades adicionais, como caixas de correio hierárquicas diretamente no servidor de e-mail, acesso a várias caixas de correio durante uma sessão e pré-seleção de e-mails.

Os clientes acessam essas estruturas on-line e podem criar cópias locais.O IMAP é baseado em texto. Sem uma conexão ativa com o servidor, o gerenciamento de e-mails é impossível.

O cliente estabelece a conexão com o servidor via porta `143`. Para comunicação, ele usa comandos baseados em texto no `ASCII`formato .

O SMTP é geralmente usado para enviar e-mails. Ao copiar e-mails enviados para uma pasta IMAP, todos os clientes têm acesso a todos os e-mails enviados, independentemente do computador de onde foram enviados.

Sem outras medidas, o IMAP funciona sem criptografia e transmite comandos, e-mails ou nomes de usuários e senhas em texto simples.SSL/TLS geralmente é usado para esse propósito. Dependendo do método e da implementação usados, a conexão criptografada usa a porta padrão `143`ou uma porta alternativa, como `993`.

## Configuração padrão

Tanto o IMAP quanto o POP3 possuem um grande número de configurações, o que dificulta um aprofundamento. Recomendamos criar uma VM localmente e instalar os dois pacotes `dovecot-imapd`, e `dovecot-pop3d`usar `apt`e brincar com as configurações e experimentos.

Na documentação do Dovecot, podemos encontrar as [configurações de núcleo](https://doc.dovecot.org/settings/core/) individuais e opções [de configuração de serviço](https://doc.dovecot.org/configuration_manual/service_configuration/) que podem ser utilizadas para nossos experimentos.

Mas vamos dar uma olhada na lista de comandos e ver como podemos interagir e nos comunicar diretamente com IMAP e POP3 usando a linha de comando.

### Comados IMAP

|                                 |                                                                                                                 |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `1 LOGIN username password`     | Login do usuário.                                                                                               |
| `1 LIST "" *`                   | Lista todos os diretórios.                                                                                      |
| `1 CREATE "INBOX"`              | Cria uma caixa de correio com um nome especificado.                                                             |
| `1 DELETE "INBOX"`              | Exclui uma caixa de correio.                                                                                    |
| `1 RENAME "ToRead" "Important"` | Renomeia uma caixa de correio.                                                                                  |
| `1 LSUB "" *`                   | Retorna um subconjunto de nomes do conjunto de nomes que o Usuário declarou como sendo `active`ou `subscribed`. |
| `1 SELECT INBOX`                | Seleciona uma caixa de correio para que as mensagens nela contidas possam ser acessadas.                        |
| `1 UNSELECT INBOX`              | Sai da caixa de correio selecionada.                                                                            |
| `1 FETCH <ID> all`              | Recupera dados associados a uma mensagem na caixa de correio.                                                   |
| `1 CLOSE`                       | Remove todas as mensagens com o `Deleted`sinalizador definido.                                                  |
| `1 LOGOUT`                      | Fecha a conexão com o servidor IMAP.                                                                            |

### Comandos POP3

| **Comando**     | **Descrição**                                                  |
| --------------- | -------------------------------------------------------------- |
| `USER username` | Identifica o usuário.                                          |
| `PASS password` | Autenticação do usuário através de sua senha.                  |
| `STAT`          | Solicita o número de e-mails salvos do servidor.               |
| `LIST`          | Solicita ao servidor o número e o tamanho de todos os e-mails. |
| `RETR id`       | Solicita ao servidor que entregue o e-mail solicitado por ID.  |
| `DELE id`       | Solicita ao servidor que exclua o e-mail solicitado por ID.    |
| `CAPA`          | Solicita ao servidor para exibir os recursos do servidor.      |
| `RSET`          | Solicita ao servidor que redefina as informações transmitidas. |
| `QUIT`          | Fecha a conexão com o servidor POP3.                           |

### Configurações perigosas

Algumas empresas ainda usam seus próprios servidores de e-mail por muitos motivos diferentes. Um desses motivos é manter a privacidade que desejam manter em suas próprias mãos. Muitos erros de configuração podem ser cometidos por administradores, o que, nos piores casos, nos permitirá ler todos os e-mails enviados e recebidos, que podem até conter informações confidenciais ou sensíveis.

| **Contexto**              | **Descrição**                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------------ |
| `auth_debug`              | Habilita todo o registro de depuração de autenticação.                                           |
| `auth_debug_passwords`    | Esta configuração ajusta o detalhamento do log, as senhas enviadas e o esquema que é registrado. |
| `auth_verbose`            | Registra tentativas de autenticação malsucedidas e seus motivos.                                 |
| `auth_verbose_passwords`  | As senhas usadas para autenticação são registradas e também podem ser truncadas.                 |
| `auth_anonymous_username` | Isso especifica o nome de usuário a ser usado ao efetuar login com o mecanismo ANONYMOUS SASL.   |

## Footprinting the service

Por padrão, as portas `110`, `143`, `993`, e `995`são usadas para IMAP e POP3. As duas portas mais altas são usadas `TLS/SSL`para criptografar a comunicação entre cliente e servidor.

O escaneamento retornará as informações correspondentes, como as que vemos abaixo, se o servidor usar um certificado SSL incorporado.

### nmap

```bash
sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC
```

```txt
Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00026s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
143/tcp open  imap     Dovecot imapd
|_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
```


Por exemplo, a partir da saída, podemos ver que o nome comum é `mail1.inlanefreight.htb`, e o servidor de e-mail pertence à organização `Inlanefreight`, que está localizada na Califórnia. Os recursos exibidos nos mostram os comandos disponíveis no servidor e para o serviço na porta correspondente.


Se descobrirmos com sucesso as credenciais de acesso de um dos funcionários, um invasor poderá fazer login no servidor de e-mail e ler ou até mesmo enviar as mensagens individuais.

### curl

```bash
curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd
```

```txt
* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```

Com a opção `-v` vemos como a conexão é feita. Assim podemos ver a versão do TLS usada para criptografia, mais detalhes do certificado SSL e até mesmo o banner, que normalmente contém a versão do servidor de email.

```bash
curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v
```
![[Pasted image 20240708104855.png]]

Para interagir com o servidor IMAP ou POP3 sobre SSL, podemos usar `openssl`, assim como `ncat`. Os comandos para isso ficariam assim:

### OpenSSL - Interação criptografada TLS POP3

```bash
openssl s_client -connect 10.129.14.128:pop3s
```

### OpenSSL - TLS Criptografado Interação IMAP

```bash
openssl s_client -connect 10.129.14.128:imaps
```

Depois que tivermos iniciado com sucesso uma conexão e efetuado login no servidor de e-mail de destino, podemos usar os comandos acima para trabalhar e navegar no servidor.



























































































































































































































