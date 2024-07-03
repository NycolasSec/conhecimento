
O objetivo principal dos firewalls é fornecer um mecanismo de segurança para controlar e monitorar o tráfego de rede entre diferentes segmentos de rede, como redes internas e externas ou diferentes zonas de rede

No Linux, a funcionalidade de firewall é tipicamente implementada usando o framework Netfilter, que é uma parte integral do kernel. O Netfilter fornece um conjunto de ganchos que podem ser usados ​​para interceptar e modificar o tráfego de rede conforme ele passa pelo sistema. O utilitário iptables é comumente usado para configurar as regras de firewall em sistemas Linux.


## Iptables

O utilitário iptables fornece um conjunto flexível de regras para filtrar tráfego de rede com base em vários critérios, como endereços IP de origem e destino, números de porta, protocolos e muito mais.Também existem outras soluções como nftables, ufw e firewalld.

Ele consiste em vários componentes que trabalham juntos para fornecer uma solução de firewall flexível e poderosa. Os principais componentes do iptables são:

| **Componente** | **Descrição**                                                                                                                                                                                                  |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Tables`       | Tabelas são usadas para organizar e categorizar regras de firewall.                                                                                                                                            |
| `Chains`       | Cadeias são usadas para agrupar um conjunto de regras de firewall aplicadas a um tipo específico de tráfego de rede.                                                                                           |
| `Rules`        | As regras definem os critérios para filtrar o tráfego de rede e as ações a serem tomadas para pacotes que correspondem aos critérios.                                                                          |
| `Matches`      | As correspondências são usadas para corresponder a critérios específicos para filtrar tráfego de rede, como endereços IP de origem ou destino, portas, protocolos e muito mais.                                |
| `Targets`      | Os alvos especificam a ação para pacotes que correspondem a uma regra específica. Por exemplo, os alvos podem ser usados ​​para aceitar, descartar ou rejeitar pacotes ou modificar os pacotes de outra forma. |

### Tables

As tabelas no iptables são usadas para categorizar e organizar regras de firewall com base no tipo de tráfego que elas são projetadas para manipular.

| **Nome da tabela** | **Descrição**                                                                     | **Correntes Integradas**                                       |
| ------------------ | --------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `filter`           | Usado para filtrar tráfego de rede com base em endereços IP, portas e protocolos. | ENTRADA, SAÍDA, FRENTE                                         |
| `nat`              | Usado para modificar os endereços IP de origem ou destino dos pacotes de rede.    | PRÉ-ROTEAMENTO, PÓS-ROTEAMENTO                                 |
| `mangle`           | Usado para modificar os campos de cabeçalho dos pacotes de rede.                  | PRÉ-ROTEAMENTO, SAÍDA, ENTRADA, ENCAMINHAMENTO, PÓS-ROTEAMENTO |

Além das tabelas internas, o iptables fornece uma quarta tabela chamada raw table, que é usada para configurar opções especiais de processamento de pacotes. A raw table contém duas chains internas: PREROUTING e OUTPUT.

### Chains

No iptables, chains organizam regras que definem como o tráfego de rede deve ser filtrado ou modificado. Existem dois tipos de chains no iptables:

- Built-in chains
- User-defined chains

As cadeias internas são predefinidas e criadas automaticamente quando uma tabela é criada. Cada tabela tem um conjunto diferente de cadeias internas. Por exemplo, a tabela de filtros tem três cadeias internas:

- INPUT
- OUTPUT
- FORWARD

Essas cadeias são usadas para filtrar tráfego de rede de entrada e saída, bem como tráfego que está sendo encaminhado entre diferentes interfaces de rede. A tabela nat tem duas cadeias internas:

- PREROUTING
- POSTROUTING

A cadeia PREROUTING é usada para modificar o endereço IP de destino de pacotes de entrada antes que a tabela de roteamento os processe. A cadeia POSTROUTING é usada para modificar o endereço IP de origem de pacotes de saída depois que a tabela de roteamento os processa.

A tabela mangle tem cinco cadeias internas:

- PREROUTING
- OUTPUT
- INPUT
- FORWARD
- POSTROUTING

Essas cadeias são usadas para modificar os campos de cabeçalho de pacotes de entrada e saída e pacotes sendo processados ​​pelas cadeias correspondentes.

`User-defined chains`pode simplificar o gerenciamento de regras agrupando regras de firewall com base em critérios específicos, como endereço IP de origem, porta de destino ou protocolo.

Elas podem ser adicionadas a qualquer uma das três tabelas principais.

Alguns dos alvos comuns usados ​​em regras do iptables incluem o seguinte:

### Rules e Targets

| **Nome do alvo** | **Descrição**                                                                                                                                                     |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ACCEPT`         | Permite que o pacote passe pelo firewall e continue até seu destino                                                                                               |
| `DROP`           | Descarta o pacote, bloqueando efetivamente sua passagem pelo firewall                                                                                             |
| `REJECT`         | Descarta o pacote e envia uma mensagem de erro de volta ao endereço de origem, notificando-o de que o pacote foi bloqueado                                        |
| `LOG`            | Registra as informações do pacote no log do sistema                                                                                                               |
| `SNAT`           | Modifica o endereço IP de origem do pacote, normalmente usado para Network Address Translation (NAT) para traduzir endereços IP privados em endereços IP públicos |
| `DNAT`           | Modifica o endereço IP de destino do pacote, normalmente usado para NAT para encaminhar tráfego de um endereço IP para outro                                      |
| `MASQUERADE`     | Semelhante ao SNAT, mas usado quando o endereço IP de origem não é fixo, como em um cenário de endereço IP dinâmico                                               |
| `REDIRECT`       | Redireciona pacotes para outra porta ou endereço IP                                                                                                               |
| `MARK`           | Adiciona ou modifica o valor da marca Netfilter do pacote, que pode ser usado para roteamento avançado ou outros propósitos                                       |

Vamos ilustrar uma regra e considerar que queremos adicionar uma nova entrada à cadeia INPUT que permite que o tráfego TCP de entrada na porta 22 (SSH) seja aceito.

O comando para isso seria parecido com o seguinte:

```sh
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

### Matches

`Matches` são usados ​​para especificar os critérios que determinam se uma regra de firewall deve ser aplicada a um pacote ou conexão em particular.

As correspondências são usadas para corresponder a características específicas do tráfego de rede, como o endereço IP de origem ou destino, protocolo, número de porta e muito mais.

| **Nome da partida**   | **Descrição**                                                                       |
| --------------------- | ----------------------------------------------------------------------------------- |
| `-p`ou`--protocol`    | Especifica o protocolo a ser correspondido (por exemplo, tcp, udp, icmp)            |
| `--dport`             | Especifica a porta de destino para corresponder                                     |
| `--sport`             | Especifica a porta de origem para corresponder                                      |
| `-s`ou`--source`      | Especifica o endereço IP de origem para corresponder                                |
| `-d`ou`--destination` | Especifica o endereço IP de destino a ser correspondido                             |
| `-m state`            | Corresponde ao estado de uma conexão (por exemplo, NOVO, ESTABELECIDO, RELACIONADO) |
| `-m multiport`        | Corresponde a várias portas ou intervalos de portas                                 |
| `-m tcp`              | Corresponde a pacotes TCP e inclui opções adicionais específicas de TCP             |
| `-m udp`              | Corresponde a pacotes UDP e inclui opções adicionais específicas de UDP             |
| `-m string`           | Corresponde a pacotes que contêm uma sequência específica                           |
| `-m limit`            | Corresponde pacotes em um limite de taxa especificado                               |
| `-m conntrack`        | Corresponde pacotes com base em suas informações de rastreamento de conexão         |
| `-m mark`             | Corresponde aos pacotes com base no valor da marca Netfilter                        |
| `-m mac`              | Corresponde pacotes com base em seus endereços MAC                                  |
| `-m iprange`          | Corresponde pacotes com base em um intervalo de endereços IP                        |

Em geral, as correspondências são especificadas usando a opção '-m' no iptables. Por exemplo, o comando a seguir adiciona uma regra à cadeia 'INPUT' na tabela 'filter' que corresponde ao tráfego TCP de entrada na porta 80:

```sh
sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
```

Esta regra de exemplo corresponde ao tráfego TCP de entrada ( `-p tcp`) na porta 80 ( `--dport 80`) e salta para o destino de aceitação ( `-j ACCEPT`) se a correspondência for bem-sucedida.






























































































































































































