## Opções de filtragem e sintaxe avançada
Utilizar opções de filtragem mais avançadas como as listadas abaixo nos permitirá reduzir o tráfego impresso na saída ou enviado para o arquivo. É altamente recomendável explorar os filtros mais avançados e encontrar combinações diferentes.

Quando implementados, esses filtros inspecionarão todos os pacotes capturados e procurarão os valores fornecidos no cabeçalho do protocolo para corresponder.

#### Filtros TCPDump úteis

| **Filter**           | **Result**                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------- |
| host                 | `host` will filter visible traffic to show anything involving the designated host. Bi-directional        |
| src / dest           | `src` and `dest` are modifiers. We can use them to designate a source or destination host or port.       |
| net                  | `net` will show us any traffic sourcing from or destined to the network designated. It uses / notation.  |
| proto                | will filter for a specific protocol type. (ether, TCP, UDP, and ICMP as examples)                        |
| port                 | `port` is bi-directional. It will show any traffic with the specified port as the source or destination. |
| portrange            | `portrange` allows us to specify a range of ports. (0-1024)                                              |
| less / greater "< >" | `less` and `greater` can be used to look for a packet or protocol option of a specific size.             |
| and / &&             | `and` `&&` can be used to concatenate two different filters together. for example, src host AND port.    |
| or                   | `or` allows for a match on either of two conditions. It does not have to meet both. It can be tricky.    |
| not                  | `not` is a modifier saying anything but x. For example, not UDP.                                         |

Ao usar o filtro `host`, qualquer IP que inserirmos será verificado no campo IP de origem ou destino. Isso pode ser visto na saída abaixo.

#### Filtro de host
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: host [IP]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 host 172.16.146.2
```
![[Pasted image 20240923075755.png]]
Este filtro é frequentemente usado quando queremos examinar apenas um host ou servidor específico. Com isso, podemos identificar com quem esse host ou servidor se comunica e de que forma.

#### Filtro de Origem/Destino
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: src/dst [host|net|port] [IP|Network Range|Port]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 src host 172.16.146.2
```
![[Pasted image 20240923075929.png]]

Origem e destino nos permitem trabalhar com as direções da comunicação. Por exemplo, na última saída, especificamos que nosso `source`host é `172.16.146.2`, e somente pacotes enviados deste host serão interceptados. Isso pode ser feito para portas e intervalos de rede também. Um exemplo dessa utilização `src port #`seria algo como isto:

#### Utilizando Source With Port como um filtro
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 tcp src port 80
```
![[Pasted image 20240923080230.png]]

O `net` pegará qualquer coisa que corresponda à notação `/` para uma rede. No exemplo, estamos procurando por qualquer coisa destinada à rede `172.16.146.0/24`.

#### Usando o destino em combinação com o filtro de rede
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 dest net 172.16.146.0/24
```
![[Pasted image 20240923080418.png]]

Este filtro pode utilizar o nome do protocolo comum ou o número do protocolo para qualquer protocolo IP, IPv6 ou Ethernet. Exemplos comuns seriam `tcp[6], udp[17], or icmp[1]`. Podemos usar tanto o nome comum top quanto o número do protocolo bottom nas saídas acima. Podemos dar uma olhada neste [recurso](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) para uma lista útil cobrindo números de protocolo.

#### Filtro de Protocolo
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: [tcp/udp/icmp]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 udp
```
![[Pasted image 20240923081816.png]]

Usando o `port`filtro, devemos ter em mente o que estamos procurando e como esse protocolo funciona. Alguns protocolos padrão como HTTP ou HTTPS usam apenas as portas 80 e 443 com o protocolo de transporte TCP. A utilização `tcp port 80`garantirá que veremos apenas o tráfego HTTP.

#### Filtro de porta
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: port [port number]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 tcp port 443
```
![[Pasted image 20240923081954.png]]

Também podemos definir intervalos específicos dessas portas, que são então escutadas pelo TCPdump. Escutar em um intervalo de portas pode ser especialmente útil quando vemos tráfego de rede de portas que não correspondem aos serviços em execução em nossos servidores.

O filtro `portrange`, como visto abaixo, nos permite ver tudo de dentro do intervalo de portas. No exemplo, vemos algum tráfego DNS junto com algumas requisições web HTTP.

#### Filtro de intervalo de portas
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: portrange [portrange 0-65535]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 portrange 0-1024
```
![[Pasted image 20240923082301.png]]
![[Pasted image 20240923082405.png]]

Em seguida, estamos procurando por qualquer pacote com menos de 64 bytes. A partir da saída a seguir, podemos ver que para esta captura, esses pacotes consistiam principalmente em `SYN`, `FIN`, ou `KeepAlive`pacotes.

Por exemplo, digamos que estamos procurando capturar tráfego que inclui uma transferência de arquivo ou conjunto de arquivos. Sabemos que esses arquivos serão maiores do que o tráfego regular. Para demonstrar, podemos utilizar `greater 500`(alternativamente `'>500'`), que só nos mostrará pacotes com um tamanho maior que 500 bytes. Isso removerá todos os pacotes extras da visualização que sabemos que ainda não estamos preocupados.

#### Filtro Menor/Maior
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: less/greater [size in bytes]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 less 64
```
![[Pasted image 20240923082546.png]]

Acima estava um excelente exemplo de uso de `less`. Podemos utilizar o modificador `greater 500`para me mostrar apenas pacotes com 500 bytes ou mais.

#### Utilizando Maior
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 greater 500
```
![[Pasted image 20240923083012.png]]

`AND` nos mostrará qualquer coisa que atenda ambos aos requisitos definidos. Por exemplo, `host 10.12.1.122 and tcp port 80`procurará por qualquer coisa do host de origem e contenha tráfego TCP ou UDP da porta 80. 

Aqui utilizamos `host 192.168.0.1 and port 23`como um filtro. Então veremos apenas o tráfego que é desse host em particular que é apenas tráfego da porta 23.

#### Filtro AND
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: and [requirement]
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 host 192.168.0.1 and port 23
```
![[Pasted image 20240923083243.png]]

Os outros modificadores, `OR`e `NOT`nos fornecem uma maneira de especificar múltiplas condições ou negar algo. Vamos brincar um pouco com isso agora.

#### Captura básica sem filtro
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0
```
![[Pasted image 20240923083319.png]]

 Se usássemos o modificador `OR`(alternativamente `||`), poderíamos solicitar tráfego de um host específico ou apenas tráfego ICMP como exemplo. Vamos executá-lo novamente e adicionar um `OR`.

#### Filtro OR 
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: or/|| [requirement]
NycolasRamos@htb[/htb]$ sudo tcpdump -r sus.pcap icmp or host 172.16.146.1
```
![[Pasted image 20240923083455.png]]

Nosso tráfego parece um pouco diferente agora. Isso ocorre porque muitos dos pacotes correspondem à variável ICMP, enquanto alguns correspondem à variável host. Então, nesta saída, podemos ver algum tráfego ARP e tráfego ICMP. O filtro funcionou, pois 172.16.146.2 correspondeu à outra variável e apareceu como um host no campo de origem ou destino. Agora, o que acontece se utilizarmos o modificador `NOT`(alternativamente `!`).

#### Filtro NOT
```shell-session
NycolasRamos@htb[/htb]$ ### Syntax: not/! [requirement]
NycolasRamos@htb[/htb]$ sudo tcpdump -r sus.pcap not icmp
```
![[Pasted image 20240923083652.png]]

Parece muito diferente agora. Só vemos algum tráfego ARP e, em seguida, vemos algum tráfego HTTPS que não tínhamos antes. Isso ocorre porque negamos qualquer tráfego ICMP de ser exibido usando `not icmp`.

## Filtros de pré-captura VS. Processamento de pós-captura
Ao usar filtros, podemos aplicá-los a captura ou ao ler algum arquivo de captura. Caso apliquemos os filtros diretamente a captura, podemos perder informações que talvez nos seriam úteis mais tarde, ao aplicarmos em algum arquivo omitimos apenas na saída e não no arquivo de captura.

## Dicas e truques de interpretação
`-S` exibirá números de sequência absolutos, que são mais longos, eles costumam ser utilizados em outras ferramentas. Podemos mostrar nossa saída com números relativos para diminui-la, mas se formos usar em conjunto com outra ferramenta, utilizamos números absolutos.

As opções `-v`, `-X`, e `-e`podem ajudar a aumentar a quantidade de dados capturados, enquanto as opções `-c`, `-n`, `-s`, `-S`e `-q`podem ajudar a reduzir e modificar a quantidade de dados gravados e visualizados.

``-A`` mostrará apenas o texto ASCII após a linha do pacote, em vez de ASCII e Hex. `-L`dirá ao tcpdump para gerar pacotes em um modo diferente. `-L`fará o buffer de linha em vez de agrupar e enviar em pedaços. Ele nos permite enviar a saída diretamente para outra ferramenta, como `grep`usar um pipe `|`.

#### Dicas e truques
```shell-session
NycolasRamos@htb[/htb]$sudo tcpdump -Ar telnet.pcap
```
![[Pasted image 20240923085719.png]]

Observe como ele tem os valores ASCII mostrados abaixo de cada linha de saída por causa do nosso uso de `-A`. Isso pode ser útil ao procurar rapidamente por algo legível por humanos na saída.

#### Canalizando uma captura para Grep
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -Ar http.cap -l | grep 'mailto:*'
```
![[Pasted image 20240923090002.png]]

Nesse caso, usamos o `-l`para passar a saída para `grep`e procurando por qualquer instância da frase `mailto:*`.

#### Procurando por sinalizadores de protocolo TCP
```shell-session
NycolasRamos@htb[/htb]$ tcpdump -i eth0 'tcp[13] &2 != 0'
```

Isso está contando até o 13º byte na estrutura e olhando para o 2º bit. Se estiver definido como 1 ou ON, o sinalizador SYN está definido.

#### Caçando uma bandeira SYN
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 'tcp[13] &2 != 0'
```
![[Pasted image 20240923090344.png]]

Nossos resultados incluem apenas pacotes com o `SYN`sinalizador TCP definido como vemos acima.

Aqui estão alguns links para aprofundar nossos estudos sobre protocolos padrão e suas estruturas. Exceto pelo link da Wikipedia, cada link deve nos levar diretamente ao RFC que define o padrão em vigor para cada um.

#### Links do protocolo RFC
|**Link**|**Descrição**|
|---|---|
|[Protocolo IP](https://tools.ietf.org/html/rfc791)|`RFC 791`descreve o IP e sua funcionalidade.|
|[Protocolo ICMP](https://tools.ietf.org/html/rfc792)|`RFC 792`descreve o ICMP e sua funcionalidade.|
|[Protocolo TCP](https://tools.ietf.org/html/rfc793)|`RFC 793`descreve o protocolo TCP e como ele funciona.|
|[Protocolo UDP](https://tools.ietf.org/html/rfc768)|`RFC 768`descreve o UDP e como ele opera.|
|[Links rápidos RFC](https://en.wikipedia.org/wiki/List_of_RFCs#Topical_list)|Este artigo da Wikipédia contém uma grande lista de protocolos vinculados ao RFC que explica sua implementação.|