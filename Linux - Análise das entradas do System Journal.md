## Encontre eventos no System Journal
O serviço `systemd-journald` armazena dados de registro em um arquivo binário estruturado e indexado, denominado _diário_. Por exemplo, para eventos de syslog, essas informações incluem a prioridade da mensagem original e o _recurso_, que é um valor que o serviço `syslog` atribui para rastrear o processo que originou uma mensagem.

Para recuperar mensagens de log do diário, use o comando `journalctl`. Você pode usar o comando `journalctl` para visualizar todas as mensagens no diário ou para procurar eventos específicos com base em opções e critérios

Se você executar o comando como `root`, terá acesso total ao diário. Embora os usuários regulares também possam usar o comando `journalctl`, o sistema os impede de ver certas mensagens.

```bash-session
[root@host ~]# journalctl
...output omitted...
Mar 15 04:42:16 host.lab.example.com sshd[2110]: pam_unix(sshd:session): session opened for user root(uid=0) by (uid=0)
Mar 15 04:42:17 host.lab.example.com systemd[1]: Starting Hostname Service...
Mar 15 04:42:17 host.lab.example.com systemd[1]: Started Hostname Service.
lines 1951-2000/2000 (END)
```

O comando `journalctl` realça as mensagens de log importantes: as mensagens com prioridade `notice` ou `warning` estão em negrito, enquanto as mensagens com prioridade `error` ou superior estão escritas em vermelho.

A chave para usar o diário para solução de problemas e auditoria é limitar as pesquisas do diário para que mostrem somente os resultados relevantes.

Por padrão, a opção `-n` do comando `journalctl` mostra pelo menos 10 entradas de log. Você pode ajustar o número de entradas de log com um argumento opcional que especifica quantas entradas de log devem ser exibidas.
```bash-session
[root@host ~]# journalctl -n 5
Mar 15 04:42:17 host.lab.example.com systemd[1]: Started Hostname Service.
Mar 15 04:42:47 host.lab.example.com systemd[1]: systemd-hostnamed.service: Deactivated successfully.
Mar 15 04:47:33 host.lab.example.com systemd[2127]: Created slice User Background Tasks Slice.
Mar 15 04:47:33 host.lab.example.com systemd[2127]: Starting Cleanup of User's Temporary Files and Directories...
Mar 15 04:47:33 host.lab.example.com systemd[2127]: Finished Cleanup of User's Temporary Files and Directories.
```

De maneira semelhante ao comando `tail`, a opção `-f` do comando `journalctl` exibe as últimas 10 linhas do diário do sistema e continua a mostrar a saída de novas entradas do diário quando são acrescentadas. Para sair da opção `-f` do comando `journalctl`, use a combinação de teclas **Ctrl**+**C**.

```bash-session
[root@host ~]# journalctl -f
Mar 15 04:47:33 host.lab.example.com systemd[2127]: Finished Cleanup of User's Temporary Files and Directories.
Mar 15 05:01:01 host.lab.example.com CROND[2197]: (root) CMD (run-parts /etc/cron.hourly)
Mar 15 05:01:01 host.lab.example.com run-parts[2200]: (/etc/cron.hourly) starting 0anacron
Mar 15 05:01:01 host.lab.example.com anacron[2208]: Anacron started on 2022-03-15
Mar 15 05:01:01 host.lab.example.com anacron[2208]: Will run job `cron.daily' in 29 min.
Mar 15 05:01:01 host.lab.example.com anacron[2208]: Will run job `cron.weekly' in 49 min.
Mar 15 05:01:01 host.lab.example.com anacron[2208]: Will run job `cron.monthly' in 69 min.
Mar 15 05:01:01 host.lab.example.com anacron[2208]: Jobs will be executed sequentially
Mar 15 05:01:01 host.lab.example.com run-parts[2210]: (/etc/cron.hourly) finished 0anacron
Mar 15 05:01:01 host.lab.example.com CROND[2196]: (root) CMDEND (run-parts /etc/cron.hourly)
​^C

[root@host ~]#
```

Para solucionar problemas, você pode filtrar a saída do diário por prioridade das entradas do diário. A opção `-p` do comando `journalctl` mostra as entradas do diário com um nível de prioridade especificado (por nome ou por número) ou superior. O comando `journalctl` processa os níveis de prioridade `debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert` e `emerg` em ordem crescente.

Como um exemplo, execute o seguinte comando `journalctl` para listar entradas do diário com prioridade `err` ou superior:

```bash-session
[root@host ~]# journalctl -p err
Mar 15 04:22:00 host.lab.example.com pipewire-pulse[1640]: pw.conf: execvp error 'pactl': No such file or direct
Mar 15 04:22:17 host.lab.example.com kernel: Detected CPU family 6 model 13 stepping 3
Mar 15 04:22:17 host.lab.example.com kernel: Warning: Intel Processor - this hardware has not undergone testing by Red Hat and might not be certif>
Mar 15 04:22:20 host.lab.example.com smartd[669]: DEVICESCAN failed: glob(3) aborted matching pattern /dev/discs/disc*
Mar 15 04:22:20 host.lab.example.com smartd[669]: In the system's table of devices NO devices found to scan
```

Para isso, use a opção `-u` do comando `journalctl` e o nome da unidade.

```bash-session
[root@host ~]# journalctl -u sshd.service
May 15 04:30:18 host.lab.example.com systemd[1]: Starting OpenSSH server daemon...
May 15 04:30:18 host.lab.example.com sshd[1142]: Server listening on 0.0.0.0 port 22.
May 15 04:30:18 host.lab.example.com sshd[1142]: Server listening on :: port 22.
May 15 04:30:18 host.lab.example.com systemd[1]: Started OpenSSH server daemon.
May 15 04:32:03 host.lab.example.com sshd[1796]: Accepted publickey for user1 from 172.25.250.254 port 43876 ssh2: RSA SHA256:1UGy...>
May 15 04:32:03 host.lab.example.com sshd[1796]: pam_unix(sshd:session): session opened for user user1(uid=1000) by (uid=0)
May 15 04:32:26 host.lab.example.com sshd[1866]: Accepted publickey for user2 from ::1 port 36088 ssh2: RSA SHA256:M8ik...
May 15 04:32:26 host.lab.example.com sshd[1866]: pam_unix(sshd:session): session opened for user user2(uid=1001) by (uid=0)
lines 1-8/8 (END
```

Ao procurar por eventos específicos, você pode limitar os resultados a um determinado período de tempo. Para limitar a saída a um intervalo específico, o comando `journalctl` tem as opções `--since` e `--until`. As duas opções usam um argumento de tempo no formato _"AAAA-MM-DD hh:mm:ss"_ (as aspas duplas são necessárias para manter o espaço na opção).

O comando `journalctl` pressupõe que o dia começa às 00:00:00 quando você omite o argumento de hora. O comando presume o dia atual quando você omite o argumento do dia. As duas opções usam `yesterday`, `today` e `tomorrow` como argumentos válidos, além do campo de data e hora.

Como um exemplo, execute o seguinte comando `journalctl` para listar todas as entradas de diário dos registros de hoje:

```bash-session
[root@host ~]# journalctl --since today
...output omitted...
Mar 15 05:04:20 host.lab.example.com systemd[1]: Started Session 8 of User student.
Mar 15 05:04:20 host.lab.example.com sshd[2255]: pam_unix(sshd:session): session opened for user student(uid=1000) by (uid=0)
Mar 15 05:04:20 host.lab.example.com systemd[1]: Starting Hostname Service...
Mar 15 05:04:20 host.lab.example.com systemd[1]: Started Hostname Service.
Mar 15 05:04:50 host.lab.example.com systemd[1]: systemd-hostnamed.service: Deactivated successfully.
Mar 15 05:06:33 host.lab.example.com systemd[2261]: Starting Mark boot as successful...
Mar 15 05:06:33 host.lab.example.com systemd[2261]: Finished Mark boot as successful.
lines 1996-2043/2043 (END)
```

Execute o seguinte comando `journalctl` para listar todas as entradas de diário de `2022-03-11 20:30:00` a `2022-03-14 10:00:00`:

```bash-session
[root@host ~]# journalctl --since "2022-03-11 20:30" --until "2022-03-14 10:00"
...output omitted...
```

Você também pode especificar todas as entradas desde um tempo relativo até o presente. Por exemplo, para especificar todas as entradas na última hora, você pode usar o seguinte comando:

```bash-session
[root@host ~]# journalctl --since "-1 hour"
...output omitted...
```

Além do conteúdo visível do diário, você pode visualizar entradas de log adicionais se ativar a saída detalhada. Qualquer campo adicional exibido pode ser usado para filtrar o resultado de uma consulta ao diário. Essa saída detalhada é recomendada para reduzir o resultado das pesquisas de determinados eventos complexos no diário.

```bash-session
[root@host ~]# journalctl -o verbose
Tue 2022-03-15 05:10:32.625470 EDT [s=e7623387430b4c14b2c71917db58e0ee;i...]
    _BOOT_ID=beaadd6e5c5448e393ce716cd76229d4
    _MACHINE_ID=4ec03abd2f7b40118b1b357f479b3112
    PRIORITY=6
    SYSLOG_FACILITY=3
    SYSLOG_IDENTIFIER=systemd
    _UID=0
    _GID=0
    _TRANSPORT=journal
    _CAP_EFFECTIVE=1ffffffffff
    TID=1
    CODE_FILE=src/core/job.c
    CODE_LINE=744
    CODE_FUNC=job_emit_done_message
    JOB_RESULT=done
    _PID=1
    _COMM=systemd
    _EXE=/usr/lib/systemd/systemd
    _SYSTEMD_CGROUP=/init.scope
    _SYSTEMD_UNIT=init.scope
    _SYSTEMD_SLICE=-.slice
    JOB_TYPE=stop
    MESSAGE_ID=9d1aaa27d60140bd96365438aad20286
    _HOSTNAME=host.lab.example.com
    _CMDLINE=/usr/lib/systemd/systemd --switched-root --system --deserialize 31
    _SELINUX_CONTEXT=system_u:system_r:init_t:s0
    UNIT=user-1000.slice
    MESSAGE=Removed slice User Slice of UID 1000.
    INVOCATION_ID=0e5efc1b4a6d41198f0cf02116ca8aa8
    JOB_ID=3220
    _SOURCE_REALTIME_TIMESTAMP=1647335432625470
lines 46560-46607/46607 (END) q
```

A seguinte lista mostra alguns campos do diário do sistema que podem ser usados para procurar linhas relevantes para um processo ou evento específico:

- _\_COMM_ é o nome do comando.
- _\_EXE_ é o caminho para o arquivo executável do processo.
- _\_PID_ é a PID do processo.
- _\_UID_ é a UID do usuário que está executando o processo.
- _\_SYSTEMD_UNIT_ é a unidade do `systemd` que iniciou o processo.

Você pode combinar mais de um dos campos de diário do sistema para formar uma consulta de pesquisa granular com o comando `journalctl`. Por exemplo, o comando `journalctl` a seguir mostra todas as entradas de diário relacionadas à unidade `sshd.service` `systemd` de um processo com PID `2110`.

```bash-session
[root@host ~]# journalctl _SYSTEMD_UNIT=sshd.service _PID=2110
Mar 15 04:42:16 host.lab.example.com sshd[2110]: Accepted publickey for root from 172.25.250.254 port 46224 ssh2: RSA SHA256:1UGybTe52L2jzEJa1HLVKn9QUCKrTv3ZzxnMJol1Fro
Mar 15 04:42:16 host.lab.example.com sshd[2110]: pam_unix(sshd:session): session opened for user root(uid=0) by (uid=0)
```






































































