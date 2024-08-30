## Registro de eventos no sistema
Vários programas usam o protocolo syslog para registrar eventos no sistema. Cada mensagem de log é categorizada por um recurso (o subsistema que produz a mensagem) e uma prioridade (a gravidade da mensagem).

A seguinte tabela lista os recursos padrão do syslog:

| Código | Recurso         | Descrição do recurso                     |
| :----- | :-------------- | :--------------------------------------- |
| 0      | kern            | Mensagens do kernel                      |
| 1      | user            | Mensagens no nível do usuário            |
| 2      | mail            | Mensagens do sistema de e-mail           |
| 3      | daemon          | Mensagens de daemons do sistema          |
| 4      | auth            | Mensagens de autenticação e segurança    |
| 5      | syslog          | Mensagens internas do syslog             |
| 6      | lpr             | Mensagens da impressora                  |
| 7      | news            | Mensagens de notícias da rede            |
| 8      | uucp            | Mensagens do protocolo UUCP              |
| 9      | cron            | Mensagens do daemon do relógio           |
| 10     | authpriv        | Mensagens de autorização fora do sistema |
| 11     | ftp             | Mensagens do protocolo FTP               |
| 16-23  | local0 a local7 | Mensagens locais personalizadas          |

A seguinte tabela lista as prioridades padrão do syslog em ordem decrescente:

|Código|Prioridade|Descrição da prioridade|
|:--|:--|:--|
|0|emerg|O sistema está inutilizável|
|1|alerta|É necessário realizar uma ação imediata|
|2|crit|Condição crítica|
|3|err|Condição de erro não crítico|
|4|warning|Condição de aviso|
|5|notice|Evento normal, mas significativo|
|6|info|Evento informativo|
|7|depurar|Mensagem de nível de depuração|

O serviço `rsyslog` usa o recurso e a prioridade das mensagens de log para determinar como lidar com elas. As regras configuram esse recurso e a prioridade no arquivo `/etc/rsyslog.conf` e em qualquer arquivo no diretório `/etc/rsyslog.d` com a extensão `.conf`. Os pacotes de software podem adicionar regras instalando um arquivo apropriado no diretório `/etc/rsyslog.d`.

Cada regra que controla como classificar mensagens de syslog tem uma linha em um dos arquivos de configuração. O lado esquerdo de cada linha indica os recursos e a prioridade das mensagens de syslog que correspondem à regra. O lado direito de cada linha indica o arquivo onde a mensagem de log será salva (ou onde entregar a mensagem). Um asterisco (`*`) é um curinga que corresponde a todos os valores.

Por exemplo, a seguinte linha no arquivo `/etc/rsyslog.conf` registra mensagens enviadas para o recurso `authpriv` em qualquer prioridade no arquivo `/var/log/secure`:

```txt
authpriv.*                  /var/log/secure
```

Às vezes, as mensagens de log correspondem a mais de uma regra no arquivo `rsyslog.conf`. Nesses casos, uma mensagem é armazenada em mais de um arquivo de log. Para limitar o número de mensagens armazenadas, a palavra-chave `none` no campo de prioridade indica que nenhuma mensagem para o recurso indicado está ser armazenada em um determinado arquivo.

Em vez de serem registradas em um arquivo, as mensagens do syslog também podem ser impressas nos terminais de todos os usuários conectados. O arquivo `rsyslog.conf` tem uma configuração para imprimir todas as mensagens do syslog com a prioridade `emerg` nos terminais de todos os usuários conectados.

### Exemplo de regras do serviço rsyslog
```txt
#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log
```

### Rodízio de arquivos de log
O comando **logrotate** realiza o rodízio de arquivos de log no diretório **/var/log** para evitar que ocupem muito espaço. Durante o rodízio, os logs são renomeados com uma data, e um novo log é criado. Após cerca de quatro semanas, os logs mais antigos são descartados para liberar espaço.

Por exemplo, o arquivo `/var/log/messages` anterior é renomeado para o arquivo `/var/log/messages-20220320` quando o rodízio acontece em 2022-03-20.

O logrotate é executado diariamente para verificar a necessidade de rodízio, que geralmente ocorre semanalmente, mas também pode acontecer com base em tamanho específico.

### Análise de uma entrada do syslog
As mensagens de log iniciam com a mensagem mais antiga no início, e as mensagens mais recentes no final do arquivo de log. O serviço `rsyslog` usa um formato padrão ao gravar entradas em arquivos de log. O exemplo a seguir explica a anatomia de uma mensagem de log no arquivo de log `/var/log/secure`.

```txt
Mar 20 20:11:48 localhost sshd[1433]: Failed password for student from 172.25.0.10 port 59344 ssh2
```

- **`Mar 20 20:11:48`** : registra o carimbo de data e hora da entrada de log.
- **`localhost`** : o host que envia a mensagem de log.
- **`sshd[1433]`** : o nome do programa ou do processo e o número PID que enviou a mensagem de log.
- **`Failed password for …​`**: a mensagem que foi enviada.

### Monitoramento de eventos de log
Monitorar arquivos de log em busca de eventos é útil para reproduzir problemas. O comando ``tail -f _`/path/to/file`_`` exibe as últimas dez linhas do arquivo especificado e continua a mostrar as novas linhas gravadas do arquivo.

Por exemplo, para monitorar as tentativas de login com falha, execute o comando `tail` em um terminal e, em outro terminal, execute o comando `ssh` com o usuário `root` enquanto um usuário tenta fazer login no sistema.

No primeiro terminal, execute o comando `tail`:
```bash-sesson
[root@host ~]# tail -f /var/log/secure
```

No segundo terminal, execute o comando `ssh`:
```bash-session
[root@host ~]# **`ssh root@hosta`**
root@hosta's password: **``_`redhat`_``**
_...output omitted..._

[root@hostA ~]#
```

As mensagens de log são visíveis no primeiro terminal.
```bash-session
...output omitted...
Mar 20 09:01:13 host sshd[2712]: Accepted password for root from 172.25.254.254 port 56801 ssh2
Mar 20 09:01:13 host sshd[2712]: pam_unix(sshd:session): session opened for user root by (uid=0)
```

### Envio manual de mensagens do syslog
O comando `logger` envia mensagens para o serviço do `rsyslog`. Por padrão, o comando `logger` envia a mensagem para o tipo de usuário com a prioridade `notice` (`user.notice`), a menos que especificado de outra forma com a opção `-p`. Ele é útil para testar as alterações na configuração do serviço `rsyslog`.

Para enviar uma mensagem para o serviço `rsyslog` que fique gravada no arquivo de log `/var/log/boot.log`, execute o seguinte comando `logger`:

```bash-session
[root@host ~]# logger -p local7.notice "Log entry created on host"
```
































