## Conceitos de arquitetura de firewall
O kernel do Linux usa a estrutura netfilter para gerenciar operações de tráfego de rede, incluindo filtragem de pacotes, conversão de endereços e portas. Essa estrutura possui hooks que permitem aos módulos do kernel interagir com pacotes de rede em trânsito. Os hooks são rotinas que interceptam eventos, como a chegada de pacotes em uma interface, e executam ações relacionadas, como aplicar regras de firewall.

## A estrutura nftables
A estrutura **nftables** no Red Hat Enterprise Linux 9 substitui a antiga estrutura **iptables** para gerenciar regras de firewall. Ela melhora a usabilidade e a eficiência das regras de firewall. Diferente do iptables, que necessitava de regras separadas para diferentes protocolos e ferramentas (iptables, ip6tables, arptables, e ebtables), o nftables utiliza um único utilitário, **nft**, para gerenciar todos os protocolos IPv4, IPv6, ARP e Ethernet a partir de uma única interface.

## O serviço firewalld
O serviço `firewalld` é um gerenciador de firewall dinâmico e é o front-end recomendado para a estrutura `nftables`. A distribuição do Red Hat Enterprise Linux 9 inclui o pacote `firewalld`.

O serviço `firewalld` simplifica o gerenciamento de firewall por meio da classificação do tráfego de rede em _zonas_. A zona atribuída de um pacote de rede depende de critérios como o endereço IP de origem do pacote ou a interface da rede de entrada. Cada zona tem sua própria lista das portas e serviços abertos ou fechados.

O serviço `firewalld` no Linux gerencia o tráfego de rede com base em zonas. Cada pacote de rede é verificado para determinar sua origem e a zona correspondente. Se o pacote tiver um endereço de origem associado a uma zona específica, as regras dessa zona são aplicadas. Caso contrário, o pacote é associado à zona da interface de rede pela qual entrou ou, se a interface não estiver associada a nenhuma zona, à zona padrão.

A zona padrão não é uma zona separada, mas sim uma designação para uma zona existente. Por padrão, a zona `public` é usada como zona padrão, enquanto a interface de loopback (`lo`) é mapeada para a zona `trusted`.

A maioria das zonas define regras que permitem o tráfego baseado em portas e protocolos específicos, como 631/udp, ou serviços predefinidos, como `ssh`. Se o tráfego não corresponder a essas regras, será rejeitado, exceto na zona `trusted`, que permite todos os tráfegos por padrão.

## Zonas predefinidas
O serviço `firewalld` usa zonas predefinidas que você pode personalizar. Por padrão, todas as zonas permitem qualquer tráfego de entrada que seja parte de uma sessão existente iniciada pelo sistema e também permitem todo o tráfego de saída. A tabela a seguir detalha as configurações iniciais de zona.

| Nome da zona | Configuração padrão                                                                                                                                                                                                                                                                        |
| :----------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `trusted`    | Permite todo o tráfego de entrada.                                                                                                                                                                                                                                                         |
| `home`       | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda aos serviços predefinidos `ssh`, `mdns`, `ipp-client`, `samba-client` ou `dhcpv6-client`.                                                                                                    |
| `internal`   | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda aos serviços predefinidos `ssh`, `mdns`, `ipp-client`, `samba-client` ou `dhcpv6-client` (como ocorre com a zona `home`).                                                                    |
| `work`       | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda aos serviços predefinidos `ssh`, `ipp-client` ou `dhcpv6-client`.                                                                                                                            |
| `public`     | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda aos serviços predefinidos `ssh` ou `dhcpv6-client`. _A zona padrão para interfaces de rede adicionadas recentemente._                                                                        |
| `external`   | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda ao serviço predefinido `ssh`. O tráfego IPv4 de saída encaminhado por meio dessa zona é _mascarado_ para dar a impressão de que é originário do endereço IPv4 da interface de rede de saída. |
| `dmz`        | Rejeita o tráfego de entrada, a menos que seja relacionado ao tráfego de saída ou corresponda ao serviço predefinido `ssh`.                                                                                                                                                                |
| `block`      | Rejeita todo o tráfego de entrada, a menos que seja relacionado ao tráfego de saída.                                                                                                                                                                                                       |
| `drop`       | Remove todo o tráfego de entrada, a menos que seja relacionado ao tráfego de saída (nem mesmo responde com erros de ICMP).                                                                                                                                                                 |

Para obter uma lista das zonas predefinidas disponíveis e de seus usos pretendidos, consulte a página do man `firewalld.zones`.

## Serviços predefinidos
O serviço `firewalld` inclui configurações predefinidas para serviços comuns para simplificar a definição de regras de firewall. Por exemplo, em vez de pesquisar as portas relevantes para um servidor NFS, use a configuração `nfs` predefinida para criar regras para as portas e os protocolos corretos

|Nome do serviço|Configuração|
|:--|:--|
|`ssh`|Servidor SSH local. Tráfego pela porta 22/tcp.|
|`dhcpv6-client`|Cliente DHCPv6 local. Tráfego pela porta 546/udp em rede fe80::/64 IPv6|
|`ipp-client`|Impressão IPP local. Tráfego pela porta 631/udp.|
|`samba-client`|Cliente local do Windows para compartilhamento de impressão e de arquivos. Tráfego pelas portas 137/udp e 138/udp.|
|`mdns`|Resolução de nomes de link local do Multicast DNS (mDNS). Tráfego pela porta 5353/udp para endereços de multicast 224.0.0.251 (IPv4) ou ff02::fb (IPv6).|
|`cockpit`|Interface baseada na web do Red Hat Enterprise Linux para gerenciar e monitorar seu sistema local e remoto. Tráfego para a porta 9090.|

O pacote `firewalld` inclui muitas configurações de serviço predefinidas. Você pode listar os serviços com o comando `firewall-cmd --get-services`.

```shell-session
[root@host ~]# firewall-cmd --get-services
```
![[Pasted image 20240902133623.png]]

Se as configurações de serviço predefinidas não forem apropriadas para seu caso, você poderá especificar manualmente as portas e os protocolos necessários.

## Configurar o daemon firewalld
A seguinte lista mostra duas maneiras comuns que os administradores de sistema usam para interagir com o serviço `firewalld`:

- A interface gráfica do console da web
- A ferramenta de linha de comando `firewall-cmd`

### Configurar serviços de firewall usando o console da web
Para gerenciar serviços de firewall com o console da web, você deve fazer login e escalonar privilégios. Para escalonar privilégios, clique nos botões Limited access ou Turn on administrative access. Então, digite sua senha quando solicitado.

Clique na opção **Networking** no menu de navegação esquerdo para exibir a seção **Firewall** na página principal da rede. Clique nas zonas do botão **Edit rules and zones** para navegar até a página **Firewall**.
![[Pasted image 20240902134430.png]]

A página Firewall exibe as zonas ativas e os serviços permitidos delas. Clique no botão de seta (>) à esquerda do nome de um serviço para visualizar os detalhes dele. Para adicionar um serviço a uma zona, clique no botão Add services, no canto superior direito da zona aplicável.
![[Pasted image 20240902134444.png]]

A página Add Services exibe os serviços predefinidos disponíveis.
![[Pasted image 20240902134502.png]]

Para selecionar um serviço, percorra a lista ou insira uma seleção na caixa de texto Filter services. No exemplo a seguir, a string `http` filtra as opções para serviços relacionados à web.

Marque a caixa de seleção à esquerda do serviço para conceder permissão através do firewall. Clique no botão Add services para concluir o processo.
![[Pasted image 20240902135137.png]]

A interface retorna à página Firewall, onde você pode revisar a lista atualizada de serviços permitidos.
![[Pasted image 20240902135422.png]]

### Configurar o firewall usando a linha de comando
O comando `firewall-cmd` faz interface com o daemon `firewalld`. Ele é instalado como parte do pacote do `firewalld`.

A tabela a seguir mostra comandos `firewall-cmd` usados com frequência, seguidos de uma explicação. A maioria dos comandos funciona na configuração de _tempo de execução_, a não ser que a opção `--﻿permanent` seja especificada. Se a opção `--permanent` for especificada, você deverá ativar a configuração executando também o comando `firewall-cmd --reload`, que lê a configuração permanente atual e a aplica como a nova configuração de tempo de execução.

Muitos dos comandos listados usam a opção `--zone=ZONE` para encontrar quais zonas eles afetam. Quando uma máscara de rede for necessária, use a notação CIDR, como 192.168.1/24.

| Comandos firewall-cmd                           | Explicação                                                                                                                                                       |
| :---------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--get-default-zone`                            | Consulta a zona padrão atual.                                                                                                                                    |
| `--set-default-zone=ZONE`                       | Define a zona padrão. Essa zona padrão altera tanto a configuração de tempo de execução quanto a permanente.                                                     |
| `--get-zones`                                   | Lista todas as zonas disponíveis.                                                                                                                                |
| `--get-active-zones`                            | Lista todas as zonas atualmente em uso (com uma interface ou fonte vinculada a cada uma delas), juntamente com suas informações de interface e de fonte.         |
| `--add-source=CIDR [--zone=ZONE]`               | Encaminha todo o tráfego do endereço IP ou da rede/máscara de rede para a zona especificada. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada. |
| ``--remove-source=CIDR [--zone=ZONE]``          | Remove a regra que encaminha todo o tráfego da zona recebido do endereço IP ou da rede. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.      |
| ``--add-interface=INTERFACE [--﻿zone=ZONE]``    | Encaminha todo o tráfego de ``_`INTERFACE`_`` para a zona especificada. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                      |
| ``--change-interface=INTERFACE [--﻿zone=ZONE]`` | Associa a interface com a ZONE em vez de sua zona atual. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                                     |
| ``--list-all [--zone=ZONE]``                    | Lista todas as interfaces, fontes, serviços e portas configuradas para ``_`ZONE`_``. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.         |
| `--list-all-zones`                              | Recupera todas as informações de todas as zonas (interfaces, fontes, portas e serviços).                                                                         |
| ``--add-service=SERVI`_ [--﻿zone=_`ZONE`_]``    | Permite tráfego para ``_`SERVICE`_``. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                                                        |
| ``--add-port=PORT/PROTOCOL [--﻿zone=ZONE]``     | Permite tráfego para as portas ``_`PORT/PROTOCOL`_``. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                                        |
| ``--remove-service=SERVICE [--﻿zone=ZONE]``     | Remove ``_`SERVICE`_`` da lista de permissões da zona. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                                       |
| ``--remove-port=PORT/PROTOCOL [--﻿zone=ZONE]``  | Remove as portas ``_`PORT/PROTOCOL`_`` da lista de permissões da zona. Se nenhuma opção `--zone=` for fornecida, a zona padrão será usada.                       |
| `--reload`                                      | Remove a configuração de tempo de execução e aplica a persistente.                                                                                               |

O exemplo a seguir define a zona padrão como `dmz`, atribui todo o tráfego vindo da rede `192.168.0.0/24` à zona `internal` e abre as portas da rede para o serviço `mysql` na zona `internal`.
```shell-session
[root@host ~]# firewall-cmd --set-default-zone=dmz

[root@host ~]# firewall-cmd --permanent --zone=internal \
--add-source=192.168.0.0/24

[root@host ~]# firewall-cmd --permanent --zone=internal --add-service=mysql

[root@host ~]# firewall-cmd --reload
```
---

Como outro exemplo, para adicionar todo o tráfego de entrada do `172.25.25.11` único endereço IPv4 à zona `public`, use os seguintes comandos:
```shell-session
[root@host ~]# firewall-cmd --permanent --zone=public \
--add-source=172.25.25.11/32

[root@host ~]# firewall-cmd --reload
```
---













