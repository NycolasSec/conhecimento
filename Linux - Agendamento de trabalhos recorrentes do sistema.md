Administradores do sistema frequentemente precisam executar trabalhos recorrentes. É recomendável agendar esses trabalhos usando contas do sistema em vez de contas de usuário individuais. Para isso, use arquivos **crontab** de todo o sistema em vez do comando `crontab`.

As entradas nos arquivos **crontab** de todo o sistema são semelhantes às dos arquivos **crontab** dos usuários, mas incluem um campo adicional antes do comando para especificar o usuário que executa o comando.

O arquivo `/etc/crontab` tem um diagrama de sintaxe nos comentários.

<pre style="overflow-x:scroll; padding:10px;">
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
</pre>

O arquivo `/etc/crontab` e outros arquivos no diretório `/etc/cron.d/` são usados para definir trabalhos recorrentes do sistema. É recomendável criar arquivos crontab personalizados no diretório `/etc/cron.d/` para agendar trabalhos recorrentes, evitando assim que uma atualização de pacote substitua o arquivo `/etc/crontab`. Pacotes que precisam de trabalhos recorrentes geralmente colocam seus arquivos crontab no diretório `/etc/cron.d/`, onde também podem ser agrupados trabalhos relacionados.

O sistema crontab também inclui repositórios para scripts serem executados a cada hora, dia, semana e mês. Esses repositórios são colocados nos diretórios `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/` e `/etc/cron.monthly/`. Esses diretórios contêm scripts shell executáveis, não arquivos crontab.

### Execução de comandos periódicos com o Anacron
O comando `anacron` usa o script `run-parts` para executar os trabalhos diários, semanais, e mensais a partir de um arquivo de configuração `/etc/anacrontab`.

O comando `anacron` usa o script `run-parts` para executar trabalhos diários, semanais e mensais definidos no arquivo de configuração `/etc/anacrontab`. Este arquivo garante que os trabalhos agendados sejam executados mesmo se o sistema estiver desligado ou hibernado, garantindo que os trabalhos pendentes sejam concluídos quando o sistema estiver ativo novamente.

O arquivo `/etc/anacrontab` pode incluir um atraso antes da execução do trabalho, conforme especificado no parâmetro "Delay in minutes". Arquivos no diretório `/var/spool/anacron/` são usados para determinar e registrar a execução dos trabalhos. A sintaxe do arquivo `/etc/anacrontab` é diferente dos arquivos crontab regulares e contém quatro campos por linha.

`Period in days`
Define o intervalo em dias para o trabalho ser executado em um agendamento recorrente. Este campo aceita um inteiro ou um valor macro. Por exemplo, a macro `@daily` é equivalente ao inteiro `1`, o que significa que o trabalho é executado diariamente. Da mesma forma, a macro `@weekly` é equivalente ao inteiro `7`, o que significa que o trabalho é executado semanalmente.

`Delay in minutes`
Define o tempo que o daemon `crond` deve aguardar antes de iniciar o trabalho.

`Job identifier`
Identifica o nome exclusivo do trabalho nas mensagens de log.

`Command`
O comando a ser executado.

O arquivo `/etc/anacrontab` também contém declarações da variável de ambiente com a sintaxe `NAME=value`. A variável `START_HOURS_RANGE` especifica o intervalo de tempo em que os trabalhos devem ser executados.

### Temporizador do systemd
A unidade de temporizador do `systemd` ativa outra unidade (como um serviço) cujo nome corresponde ao nome da unidade do temporizador. Isso permite que a ativação de outras unidades seja baseada em eventos temporizados. Para ajudar na depuração, o temporizador do `systemd` registra eventos relacionados no diário do sistema.

#### Unidade de temporizador de amostra
O pacote `sysstat` fornece a unidade de temporizador do `systemd`, chamada de serviço `sysstat-collect.timer`, para coletar estatísticas do sistema a cada 10 minutos. A saída a seguir mostra o conteúdo do arquivo de configuração `/usr/lib/systemd/system/sysstat-collect.timer`.
```txt
...output omitted...
[Unit]
Description=Run system activity accounting tool every 10 minutes

[Timer]
OnCalendar=*:00/10

[Install]
WantedBy=sysstat.service
```

A opção `OnCalendar=*:00/10` significa que esta unidade de temporizador ativa a unidade correspondente `sysstat-collect.service` a cada 10 minutos. Você pode especificar intervalos de tempo mais complexos.

Por exemplo, um valor de `2022-04-* 12:35,37,39:16` em relação à opção `OnCalendar` faz com que a unidade de temporizador ative a unidade de serviço correspondente às `12:35:16`, `12:37:16` e `12:39:16` todos os dias em abril de 2022. Você também pode especificar temporizadores relativos com a opção `OnUnitActiveSec`. Por exemplo, com a opção `OnUnitActiveSec=15min`, a unidade de temporizador aciona a unidade correspondente para iniciar 15 minutos após a última vez que a unidade de temporizador ativou sua unidade correspondente.

Depois de alterar o arquivo de configuração da unidade de temporizador, use o comando `systemctl daemon-reload` para garantir que a unidade do temporizador do `systemd` carregue as mudanças.

```shell-session
[root@host ~]# systemctl daemon-reload
```

Depois de recarregar a configuração do daemon `systemd`, use o comando `systemctl` para ativar a unidade de temporizador.

```shell-session
[root@host ~]# systemctl enable --now <unitname>.timer
```

























































