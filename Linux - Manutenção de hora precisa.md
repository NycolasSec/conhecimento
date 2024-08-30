## Administrar o fuso horário local e dos relógios locais
A sincronização de hora e data é essencial para a análise de logs. As máquinas usam o Network Time Protocol para fornecer e obter informações corretas de hora pela internet.

Uma máquina pode obter informações precisas de hora por meio de serviços NTP públicos, como o NTP Pool Project. Outra opção é sincronizar com um relógio de hardware de alta qualidade para fornecer a hora precisa para clientes locais.

O comando `timedatectl` mostra uma visão geral das configurações do sistema com relação à hora, incluindo a hora atual, o fuso horário e as configurações de sincronização de NTP do sistema.

```bash-session
[user@host ~]$ timedatectl
               Local time: Wed 2022-03-16 05:53:05 EDT
           Universal time: Wed 2022-03-16 09:53:05 UTC
                 RTC time: Wed 2022-03-16 09:53:05
                Time zone: America/New_York (EDT, -0400)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

Você pode listar um banco de dados de fusos horários com a opção `list-timezones` do comando `timedatectl`:

```bash-session
[user@host ~]$ timedatectl list-timezones
Africa/Abidjan
Africa/Accra
Africa/Addis_Ababa
Africa/Algiers
Africa/Asmara
Africa/Bamako
...output omitted...
```

A IANA fornece um banco de dados de fusos horários, e o comando **timedatectl** usa esses nomes de fuso horário, que geralmente são baseados em continentes ou oceanos e cidades principais. Algumas localidades têm regras de horário de verão diferentes, como o estado do Arizona, que usa o fuso horário **America/Phoenix** e não adota a mudança para o horário de verão.

O comando `tzselect` faz perguntas de modo interativo ao usuário sobre o local do sistema e mostra o nome do fuso horário correto. Ele não altera a configuração de fuso horário do sistema.

O usuário `root` pode alterar a configuração do sistema para atualizar o fuso horário com a opção `set-timezone` do comando `timedatectl`. Por exemplo, o comando `timedatectl` a seguir atualiza o fuso horário atual para `America/Phoenix`.

```bsash-session
[root@host ~]# timedatectl set-timezone America/Phoenix

[root@host ~]# timedatectl
               Local time: Wed 2022-03-16 03:05:55 MST
           Universal time: Wed 2022-03-16 10:05:55 UTC
                 RTC time: Wed 2022-03-16 10:05:55
                Time zone: America/Phoenix (MST, -0700)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

Use a opção `set-time` do comando `timedatectl` para alterar o horário atual do sistema. Você deve especificar a hora no formato _"AAAA-MM-DD hh:mm:ss"_, no qual é possível omitir a data ou a hora. Por exemplo, o comando `timedatectl` a seguir altera o horário para `09:00:00`.

```bash-session
[root@host ~]# timedatectl set-time 9:00:00

[root@host ~]# timedatectl
               Local time: Fri 2019-04-05 09:00:27 MST
           Universal time: Fri 2019-04-05 16:00:27 UTC
                 RTC time: Fri 2019-04-05 16:00:27
                Time zone: America/Phoenix (MST, -0700)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

A opção `set-ntp` do comando `timedatectl` ativa ou desativa a sincronização de NTP para ajuste automático de hora. A opção requer um argumento `true` ou `false` para ser ativada ou desativada. Por exemplo o comando `timedatectl` a seguir desativa a sincronização de NTP.

```bash-session
[root@host ~]# timedatectl set-ntp false
```

## Configurar e monitorar o serviço chronyd
O serviço `chronyd` rastreia o normalmente impreciso _relógio de tempo local (RTC)_ sincronizando-o com os servidores NTP configurados. Se não houver conectividade de rede disponível, o serviço `chronyd` calculará o desvio de tempo do RTC e o registrará no arquivo que o valor `driftfile` especifica no arquivo de configuração `/etc/chrony.conf`.

Por padrão, o serviço `chronyd` usa servidores do NTP Pool Project para a sincronização de horário e não precisa de configurações adicionais. Talvez seja necessário alterar os servidores NTP para uma máquina que é executada em uma rede isolada.

A _camada_ da fonte de tempo do NTP determina o número de saltos que a máquina está em relação a um relógio de referência de alto desempenho. O relógio de referência é uma fonte de tempo `stratum 0`. Um servidor NTP diretamente anexado ao relógio de referência é uma fonte de horário `stratum 1`. Uma máquina que sincroniza o horário do servidor NTP é uma fonte de horário `stratum 2`.

server e peer são as duas categorias de fontes de tempo que podem ser declaradas no arquivo de configuração `/etc/chrony.conf`. A categoria server fica um estrato acima do servidor NTP local, e a peer fica no mesmo nível de camada. Você pode definir vários servidores e pares no arquivo de configuração `chronyd`, um por linha.

O primeiro argumento da linha `server` é o endereço IP ou o nome DNS do servidor NTP. Depois do endereço IP ou nome do servidor, você pode listar uma série de opções para o servidor. A Red Hat recomenda usar a opção `iburst`, porque depois que o serviço `chronyd` é iniciado, quatro medidas são obtidas em um curto período de tempo para uma sincronização inicial de relógio mais precisa. Para obter mais informações sobre as opções do arquivo de configuração `chronyd`, use o comando `man 5 chrony.conf`.


Como exemplo, com a linha `server classroom.example.com iburst` a seguir no arquivo de configuração `/﻿etc/chrony.conf`, o serviço `chronyd` usa o servidor `classroom.example.com` como a fonte de tempo NTP.
```bash-session
# Use public servers from the pool.ntp.org project.
...output omitted...
server classroom.example.com iburst
...output omitted...
```

Reinicie o serviço depois de apontar o serviço `chronyd` para a fonte de tempo local `classroom.example.com`.

```bash-session
[root@host ~]# systemctl restart chronyd
```

O comando `chronyc` atua como um cliente para o serviço `chronyd`. Depois de definir a sincronização do NTP, verifique se o sistema local está usando perfeitamente o servidor NTP para sincronizar o relógio do sistema usando o comando `chronyc sources`. Para obter um resultado mais descritivo e com explicações adicionais sobre a saída, use o comando `chronyc sources -v`.

```bash-session
[root@host ~]# chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* 172.25.254.254                3   6    17    26  +2957ns[+2244ns] +/-   25ms
```











































