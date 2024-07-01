# Linux - Configuração de rede

Uma das principais tarefas de configuração de rede é configurar interfaces de rede. Isso inclui a atribuição de endereços IP, a configuração de dispositivos de rede, como roteadores e switches, e a configuração de protocolos de rede. 

Além disso, devemos estar familiarizados com diferentes interfaces de rede, incluindo conexões com e sem fio, e ser capazes de solucionar problemas de conectividade.

O controle de acesso à rede é outro componente crítico da configuração da rede. Como testadores de penetração, devemos estar familiarizados com a importância do NAC para a segurança da rede e com as diferentes tecnologias NAC disponíveis. Esses incluem:

- Controle de acesso discricionário (DAC)
- Controle de acesso obrigatório (MAC)
- Controle de acesso baseado em função (RBAC)

Devemos também compreender os diferentes mecanismos de aplicação do NAC e saber como configurar dispositivos de rede Linux para NAC. Isso inclui configurar políticas SELinux, configurar perfis AppArmor e usar wrappers TCP para controlar o acesso.

Ferramentas como`` syslog``, ``rsyslog``, ``ss``, ``lsof`` e ``pilha ELK`` podem ser usadas para monitorar o tráfego de rede e identificar problemas de segurança.

Além disso, um bom conhecimento das ferramentas de solução de problemas de rede é crucial para identificar vulnerabilidades e interagir com outras redes e hosts.

 Além das ferramentas que mencionamos, podemos usar ping, nslookup e nmap para diagnosticar e enumerar redes.

## Configurar interfaces de rede

Ao trabalhar com o Ubuntu, você pode configurar interfaces de rede locais usando o comando `ifconfig` ou `ip`. Esses comandos poderosos nos permitem visualizar e configurar as interfaces de rede do nosso sistema.

O comando `ifconfig` nos mostra as interfaces de rede disponíveis e seus atributos de forma clara, este comando foi descontinuado e substituído pelo comando ``ip`` que oferece recursos mais avançados.

### Configurações de rede

```bash
ip addr
```

```txt
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 8a:d9:fa:cf:79:7a brd ff:ff:ff:ff:ff:ff
    altname enp0s3
    altname ens3
    inet 178.62.32.126/18 brd 178.62.63.255 scope global dynamic eth0
       valid_lft 85274sec preferred_lft 85274sec
    inet6 fe80::88d9:faff:fecf:797a/64 scope link 
       valid_lft forever preferred_lft forever
```

Quando se trata de ativar as interfaces de rede, os comando **ip** e **ifconfig** são duas ferramentas comumente usadas. Esses comandos permitem a ativação e modificação de uma interface específica.

### Ativar interface de rede

```bash
sudo ifconfig eth0 up
sudo ip link set eth0 up
```

O comando `ifconfig` pode ser usado para alocar um endereço IP para uma interface de rede. Devemos indicar o nome da interface e o endereço IP como argumentos. 

### Atribuir endereço IP a uma interface

```bash
sudo ifconfig eth0 192.168.0.4
```

Para definir a máscara de rede devemos passar o nome da interface e a máscara de rede.

### Atribuir uma máscara de rede a uma interface

```bash
sudo ifconfig eth0 netmask 255.255.255.0
```

Quando queremos definir o gateway padrão para uma interface, podemos usar o comando `route` com a opção `add`. 

### Atribuir a rota a uma interface

```bash
sudo route add default gw 192.168.1.1 eth0
```

A configuração do servidor DNS pode ser feita modificando o arquivo `/etc/resolv.conf` com as informações apropriadas  do servidor DNS. Este arquivo é um arquivo de texto simples que contém as informações de DNS do sistema.

### Editando configurações de DNS

```bash
sudo vim /etc/resolv.conf
```

#### /etc/resolv.conf
```txt
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Mas para garantir que as modificações da interface de rede persistam quando o sistema for desligado, devemos editar o arquivo `/etc/network/interfaces`, que define interfaces de rede para sistemas operacionais Linux.

### Editando interfaces

```bash
sudo vim /etc/network/interfaces
```

Isso abrirá o arquivo de interfaces no editor ``vim``. Podemos adicionar as configurações de rede ao arquivo assim:

#### /etc/rede/interfaces
```txt
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

### Reiniciar o serviço der rede

```bash
sudo systemctl restart networking
```

## Controle de acesso à rede

O NAC é um sistema de segurança que garante que apenas dispositivos autorizados e compatíveis tenham acesso à rede, evitando acesso não autorizado, violações de dados e outras ameaças à segurança

A seguir estão as diferentes tecnologias NAC que podem ser usadas para aprimorar as medidas de segurança:

- Controle de acesso discricionário (DAC)
- Controle de acesso obrigatório (MAC)
- Controle de acesso baseado em função (RBAC)

Essas tecnologias são projetadas para fornecer diferentes níveis de controle de acesso e segurança. Cada tecnologia tem características únicas e é adequada para diferentes casos de uso.

### Controle de acesso discricionário

Permite que os usuários gerenciem o acesso aos seus recursos, concedendo aos proprietários dos recursos a responsabilidade de controlar as permissões de acesso aos seus recursos.
Isso significa que os usuários e grupos que possuem um recurso específico podem decidir quem tem acesso aos seus recursos e quais ações eles estão autorizados a executar.

### Controle de acesso obrigatório

Cada recurso recebe um rótulo de segurança que identifica seu nível de segurança, e cada usuário ou processo recebe uma autorização de segurança que identifica seu nível de segurança.
O acesso a um recurso só é concedido se o nível de segurança do usuário ou processo for igual ou superior ao nível de segurança do recurso.

 O MAC é frequentemente usado em sistemas operacionais e aplicativos que exigem um alto nível de segurança, como sistemas militares ou governamentais, sistemas financeiros e sistemas de saúde.

### Controle de acesso baseado em função

O RBAC atribui permissões aos usuários com base em suas funções dentro de uma organização.

Comparado aos sistemas de Controle de Acesso Discricionário (DAC), o RBAC fornece uma abordagem mais flexível e escalável para gerenciar o acesso a recursos. Em um sistema RBAC, cada usuário recebe uma ou mais funções, e cada função recebe um conjunto de permissões que definem as ações do usuário.

### Monitoramento

O monitoramento de rede envolve capturar, analisar e interpretar o tráfego de rede para identificar ameaças à segurança, problemas de desempenho e comportamento suspeito.

Resumindo, ao analisar o tráfego da rede, podemos obter insights sobre o comportamento da rede e identificar padrões que podem indicar ameaças à segurança.

## Troubleshooting

A solução de problemas de rede é um processo essencial que envolve o diagnóstico e a resolução de problemas de rede que podem afetar adversamente o desempenho e a confiabilidade da rede.

Várias ferramentas podem nos ajudar a identificar e resolver problemas relacionados à solução de problemas de rede em sistemas Linux. Algumas das ferramentas mais comumente usadas incluem:

- Ping
- Traceroute
- Netstat
- Tcpdump
- Wreshark
- Nmap

### ping

Ela envia pacotes para um host remoto e mede o tempo para retorná-los

```bash
ping <remote_host>
```

Por exemplo, executar ping no servidor DNS do Google enviará pacotes ICMP para o servidor DNS do Google e exibirá os tempos de resposta.

### Traceroute

Ele rastreia a rota que os pacotes tomam para chegar a um host remoto. Ele envia pacotes com valores crescentes de Time-to-Live (TTL) para um host remoto e exibe os endereços IP dos dispositivos pelos quais os pacotes passam.

```bash
traceroute www.inlanefreight.com

#traceroute to www.inlanefreight.com (134.209.24.248), 30 hops max, 60 byte packets
# 1  * * *
# 2  10.80.71.5 (10.80.71.5)  2.716 ms  2.700 ms  2.730 ms
# 3  * * *
# 4  10.80.68.175 (10.80.68.175)  7.147 ms  7.132 ms 10.80.68.161 (10.80.68.161)  7.393 ms
```

Isso exibirá os endereços IP dos dispositivos pelos quais os pacotes passam para chegar ao servidor DNS do Google

### Netstat

`Netstat` é usado para exibir conexões de rede ativas e suas portas associadas.

```bash
netstat -a
```

```txt
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 localhost:5901          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
...SNIP...
```

Isso inclui o protocolo usado, o número de bytes recebidos e enviados, endereços IP, números de porta de dispositivos locais e remotos e o estado atual da conexão.

Essas conexões incluem o software de desktop remoto VNC, o serviço Sun Remote Procedure Call, o protocolo HTTP para tráfego da web e o protocolo SSH para acesso remoto seguro ao shell.

Os problemas de rede mais comuns que encontraremos durante nossos testes de penetração incluem o seguinte:

- Problemas de conectividade de rede
- Problemas de resolução de DNS (é sempre DNS)
- Perda de pacotes
- Problemas de desempenho de rede

## Hardening

Vários mecanismos são altamente eficazes na proteção de sistemas Linux. Três desses mecanismos são:

- SELinux
- AppArmor
- TCP wrappers

Essas ferramentas são projetadas para proteger sistemas Linux contra várias ameaças de segurança, desde acesso não autorizado a ataques maliciosos, especialmente durante a realização de um teste de penetração

SELinux é um sistema MAC integrado ao kernel Linux. Ele foi projetado para fornecer controle de acesso refinado sobre recursos e aplicativos do sistema.

AppArmor também é um sistema MAC que fornece um nível semelhante de controle sobre recursos e aplicativos do sistema, mas funciona de maneira um pouco diferente.O AppArmor é implementado como um Módulo de Segurança Linux (LSM) e usa perfis de aplicativos para definir os recursos que um aplicativo pode acessar

TCP wrappers são um mecanismo de controle de acesso de rede baseado em host que pode ser usado para restringir o acesso a serviços de rede com base no endereço IP do sistema cliente.Ele funciona interceptando solicitações de rede de entrada e comparando o endereço IP do sistema cliente com as regras de controle de acesso.

## Configurando

Quando se trata de implementar medidas de segurança cibernética, não há uma abordagem única para todos. É importante considerar as informações específicas que você deseja proteger e as ferramentas que usará para isso.

### SELinux

|     |                                                                                                                                  |
| --- | -------------------------------------------------------------------------------------------------------------------------------- |
| 1.  | Instale o SELinux na sua VM.                                                                                                     |
| 2.  | Configure o SELinux para impedir que um usuário acesse um arquivo específico.                                                    |
| 3.  | Configure o SELinux para permitir que um único usuário acesse um serviço de rede específico, mas negue acesso a todos os outros. |
| 4.  | Configure o SELinux para negar acesso a um usuário ou grupo específico para um serviço de rede específico.                       |

### AppArmor

|     |                                                                                                                                   |
| --- | --------------------------------------------------------------------------------------------------------------------------------- |
| 5.  | Configure o AppArmor para impedir que um usuário acesse um arquivo específico.                                                    |
| 6.  | Configure o AppArmor para permitir que um único usuário acesse um serviço de rede específico, mas negue acesso a todos os outros. |
| 7.  | Configure o AppArmor para negar acesso a um usuário ou grupo específico para um serviço de rede específico.                       |

### Wrappers

|     |                                                                                                                       |
| --- | --------------------------------------------------------------------------------------------------------------------- |
| 8.  | Configure wrappers TCP para permitir acesso a um serviço de rede específico a partir de um endereço IP específico.    |
| 9.  | Configure wrappers TCP para negar acesso a um serviço de rede específico a partir de um endereço IP específico.       |
| 10. | Configure wrappers TCP para permitir acesso a um serviço de rede específico a partir de um intervalo de endereços IP. |

































































































































































































