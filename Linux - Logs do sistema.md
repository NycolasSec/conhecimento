# Linux - Logs do sistema

Os logs do sistema no Linux são um conjunto de arquivos que contêm informações sobre o sistema e as atividades que ocorrem nele.

Ao analisar os logs em nossos sistemas de destino, podemos obter insights sobre o comportamento do sistema, atividade de rede e atividade do usuário e podemos usar essas informações para identificar qualquer atividade anormal, como logins não autorizados, tentativas de ataques, credenciais de texto não criptografado ou acesso incomum a arquivos, o que pode indicar uma potencial violação de segurança.

Ao revisar os logs após executar o teste de segurança, podemos determinar se nossas atividades acionaram quaisquer eventos de segurança, como alertas de detecção de intrusão ou avisos do sistema.

Para garantir a segurança de um sistema Linux, é importante configurar os logs do sistema corretamente. Isso inclui definir os níveis de log apropriados, configurar a rotação de log para evitar que os arquivos de log fiquem muito grandes e garantir que os logs sejam armazenados com segurança e protegidos contra acesso não autorizado.

Existem vários tipos diferentes de logs do sistema no Linux, incluindo:

- Registros do Kernel
- Logs do sistema
- Logs de autenticação
- Registros de aplicativos
- Registros de segurança

### Registros do kernel

Esses logs contêm informações sobre o kernel do sistema, incluindo drivers de hardware, chamadas do sistema e eventos do kernel

.Eles são armazenados no `/var/log/kern.log`arquivo. Por exemplo, os logs do kernel podem revelar a presença de drivers vulneráveis ​​ou desatualizados que podem ser alvos de invasores para obter acesso ao sistema.

Eles também podem fornecer insights sobre falhas do sistema, limitações de recursos e outros eventos que podem levar a uma negação de serviço ou outros problemas de segurança.

### Logs do sistema

Esses logs contêm informações sobre eventos de nível de sistema, como inícios e paradas de serviço, tentativas de login e reinicializações do sistema. Eles são armazenados no `/var/log/syslog`arquivo.

Ao analisar tentativas de login, inícios e paradas de serviço e outros eventos de nível de sistema, podemos detectar qualquer acesso ou atividade possível no sistema.

Além disso, podemos usar o `syslog`para identificar problemas potenciais que podem impactar a disponibilidade ou o desempenho do sistema, como falhas no início do serviço ou reinicializações do sistema. 

Aqui está um exemplo de como esse `syslog`arquivo pode se parecer:

```txt
Feb 28 2023 15:00:01 server CRON[2715]: (root) CMD (/usr/local/bin/backup.sh)
Feb 28 2023 15:04:22 server sshd[3010]: Failed password for htb-student from 10.14.15.2 port 50223 ssh2
Feb 28 2023 15:05:02 server kernel: [  138.303596] ata3.00: exception Emask 0x0 SAct 0x0 SErr 0x0 action 0x6 frozen
Feb 28 2023 15:06:43 server apache2[2904]: 127.0.0.1 - - [28/Feb/2023:15:06:43 +0000] "GET /index.html HTTP/1.1" 200 13484 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.149 Safari/537.36"
Feb 28 2023 15:07:19 server sshd[3010]: Accepted password for htb-student from 10.14.15.2 port 50223 ssh2
Feb 28 2023 15:09:54 server kernel: [  367.543975] EXT4-fs (sda1): re-mounted. Opts: errors=remount-ro
Feb 28 2023 15:12:07 server systemd[1]: Started Clean PHP session files.
```

### Registro de autenticação


Esses logs contêm informações sobre tentativas de autenticação do usuário, incluindo tentativas bem-sucedidas e malsucedidas. Eles são armazenados no `/var/log/auth.log`arquivo. É importante observar que, embora o `/var/log/syslog`arquivo possa conter informações de login semelhantes, o `/var/log/auth.log`arquivo foca especificamente em tentativas de autenticação do usuário, tornando-o um recurso mais valioso para identificar potenciais ameaças à segurança.

```txt
Feb 28 2023 18:15:01 sshd[5678]: Accepted publickey for admin from 10.14.15.2 port 43210 ssh2: RSA SHA256:+KjEzN2cVhIW/5uJpVX9n5OB5zVJ92FtCZxVzzcKjw
Feb 28 2023 18:15:03 sudo:   admin : TTY=pts/1 ; PWD=/home/admin ; USER=root ; COMMAND=/bin/bash
Feb 28 2023 18:15:05 sudo:   admin : TTY=pts/1 ; PWD=/home/admin ; USER=root ; COMMAND=/usr/bin/apt-get install netcat-traditional
Feb 28 2023 18:15:08 sshd[5678]: Disconnected from 10.14.15.2 port 43210 [preauth]
Feb 28 2023 18:15:12 kernel: [  778.941871] firewall: unexpected traffic allowed on port 22
Feb 28 2023 18:15:15 auditd[9876]: Audit daemon started successfully
Feb 28 2023 18:15:18 systemd-logind[1234]: New session 4321 of user admin.
Feb 28 2023 18:15:21 CRON[2345]: pam_unix(cron:session): session opened for user root by (uid=0)
Feb 28 2023 18:15:24 CRON[2345]: pam_unix(cron:session): session closed for user root
```

### Logs de aplicação

Esses logs contêm informações sobre as atividades de aplicativos específicos em execução no sistema. Eles geralmente são armazenados em seus próprios arquivos, como `/var/log/apache2/error.log`para o servidor web Apache ou `/var/log/mysql/error.log`para o servidor de banco de dados MySQL.

Esses logs são particularmente importantes quando estamos mirando em aplicativos específicos, como servidores web ou bancos de dados, pois podem fornecer insights sobre como esses aplicativos estão processando e manipulando dados.

Esses logs podem ser usados ​​para identificar tentativas de acesso não autorizado, exfiltração de dados ou outras atividades suspeitas.

Além disso, logs de acesso e auditoria são logs críticos que registram informações sobre as ações de usuários e processos no sistema. Eles são cruciais para fins de segurança e conformidade, e podemos usá-los para identificar potenciais problemas de segurança e vetores de ataque.

or exemplo, `access logs`mantenha um registro da atividade do usuário e do processo no sistema, incluindo tentativas de login, acessos a arquivos e conexões de rede.

`Audit logs`Registre informações sobre eventos relevantes para a segurança no sistema, como modificações em arquivos de configuração do sistema ou tentativas de modificar arquivos ou configurações do sistema.

#### Entrada de log de acesso

```txt
2023-03-07T10:15:23+00:00 servername privileged.sh: htb-student accessed /root/hidden/api-keys.txt
```

Nesta entrada de log, podemos ver que o usuário `htb-student`usou o `privileged.sh`script para acessar o `api-keys.txt`arquivo no `/root/hidden/`diretório. Em sistemas Linux, a maioria dos serviços comuns tem locais padrão para logs de acesso:

| **Serviço**  | **Descrição**                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------ |
| `Apache`     | Os logs de acesso são armazenados no arquivo /var/log/apache2/access.log (ou similar, dependendo da distribuição). |
| `Nginx`      | Os logs de acesso são armazenados no arquivo /var/log/nginx/access.log (ou similar).                               |
| `OpenSSH`    | Os logs de acesso são armazenados no arquivo /var/log/auth.log no Ubuntu e em /var/log/secure no CentOS/RHEL.      |
| `MySQL`      | Os logs de acesso são armazenados no arquivo /var/log/mysql/mysql.log.                                             |
| `PostgreSQL` | Os logs de acesso são armazenados no arquivo /var/log/postgresql/postgresql-version-main.log.                      |
| `Systemd`    | Os logs de acesso são armazenados no diretório /var/log/journal/.                                                  |

### Registros de segurança

Esses logs de segurança e seus eventos são frequentemente registrados em uma variedade de arquivos de log, dependendo do aplicativo ou ferramenta de segurança específica em uso

Por exemplo, o aplicativo Fail2ban registra tentativas de login com falha no `/var/log/fail2ban.log`arquivo, enquanto o firewall UFW registra a atividade no `/var/log/ufw.log`arquivo.

outros eventos relacionados à segurança, como alterações em arquivos ou configurações do sistema, podem ser registrados em logs de sistema mais gerais, como `/var/log/syslog`ou `/var/log/auth.log`.

odos esses logs podem ser acessados ​​e analisados ​​usando uma variedade de ferramentas, incluindo os visualizadores de arquivos de log integrados na maioria dos ambientes de desktop Linux, bem como ferramentas de linha de comando, como os comandos `tail`, `grep`e `sed`.








































