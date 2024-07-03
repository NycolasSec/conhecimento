Existem vários tipos de servidores DNS que são usados ​​em todo o mundo:

- Servidor raiz DNS
- Servidor de nomes autoritativo
- Servidor de nomes não autoritativo
- Servidor de cache
- Servidor de encaminhamento
- Resolver

|   |   |
|---|---|
|`DNS Root Server`|Os servidores raiz do DNS são responsáveis ​​pelos domínios de nível superior ( `TLD`). Como última instância, eles são solicitados somente se o servidor de nomes não responder. Assim, um servidor raiz é uma interface central entre usuários e conteúdo na Internet, pois vincula domínio e endereço IP. A [Internet Corporation for Assigned Names and Numbers](https://www.icann.org/) ( `ICANN`) coordena o trabalho dos servidores de nomes raiz. Existem `13`tais servidores raiz ao redor do globo.|
|`Authoritative Nameserver`|Servidores de nomes autoritativos detêm autoridade para uma zona específica. Eles respondem apenas a consultas de sua área de responsabilidade, e suas informações são vinculativas. Se um servidor de nomes autoritativo não puder responder à consulta de um cliente, o servidor de nomes raiz assume naquele ponto.|
|`Non-authoritative Nameserver`|Servidores de nomes não autoritativos não são responsáveis ​​por uma zona DNS específica. Em vez disso, eles coletam informações sobre zonas DNS específicas, o que é feito usando consulta DNS recursiva ou iterativa.|
|`Caching DNS Server`|Servidores DNS de cache armazenam em cache informações de outros servidores de nomes por um período especificado. O servidor de nomes autoritativo determina a duração desse armazenamento.|
|`Forwarding Server`|Os servidores de encaminhamento desempenham apenas uma função: eles encaminham consultas DNS para outro servidor DNS.|
|`Resolver`|Os resolvedores não são servidores DNS autoritativos, mas executam a resolução de nomes localmente no computador ou roteador.|

O DNS é principalmente não criptografado. Dispositivos na WLAN local e provedores de Internet podem, portanto, hackear e espionar consultas DNS.

Por padrão, os profissionais de segurança de TI aplicam `DNS over TLS`( `DoT`) ou `DNS over HTTPS`( `DoH`) aqui.

Além disso, o protocolo de rede `DNSCrypt`também criptografa o tráfego entre o computador e o servidor de nomes.

O DNS armazena e emite informações adicionais sobre os serviços associados a um domínio. Uma consulta DNS pode revelar qual computador serve como servidor de e-mail para o domínio em questão ou como os servidores de nomes do domínio são chamados.

![[tooldev-dns.webp]]

Diferentes `DNS records`são usados ​​para as consultas DNS, que têm todas várias tarefas.

|         |                                                                                                                                                                                                                                                                         |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `A`     | Retorna um endereço IPv4 do domínio solicitado como resultado.O `SOA`registro está localizado no arquivo de zona de um domínio e especifica quem é responsável pela operação do domínio e como as informações de DNS do domínio são gerenciadas.                        |
| `AAAA`  | Retorna um endereço IPv6 do domínio solicitado.                                                                                                                                                                                                                         |
| `MX`    | Retorna os servidores de e-mail responsáveis ​​como resultado.                                                                                                                                                                                                          |
| `NS`    | Retorna os servidores DNS (nameservers) do domínio.                                                                                                                                                                                                                     |
| `TXT`   | Este registro pode conter várias informações. O all-rounder pode ser usado, por exemplo, para validar o Google Search Console ou validar certificados SSL. Além disso, as entradas SPF e DMARC são definidas para validar o tráfego de e-mail e protegê-lo contra spam. |
| `CNAME` | Este registro serve como um alias para outro nome de domínio. Se você quiser que o domínio www.hackthebox.eu aponte para o mesmo IP que hackthebox.eu, você criaria um registro A para hackthebox.eu e um registro CNAME para www.hackthebox.eu.                        |
| `PTR`   | O registro PTR funciona ao contrário (pesquisa reversa). Ele converte endereços IP em nomes de domínio válidos.                                                                                                                                                         |
| `SOA`   | Fornece informações sobre a zona DNS correspondente e o endereço de e-mail do contato administrativo.                                                                                                                                                                   |
|         |                                                                                                                                                                                                                                                                         |

O registro `SOA` está localizado no arquivo de zona de um domínio e especifica quem é responsável pela operação do domínio e como as informações de DNS do domínio são gerenciadas.

```bash
dig soa ww.ilanefreight.com
```

```txt
; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.inlanefreight.com.         IN      SOA

;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Jan 05 12:56:10 GMT 2023
;; MSG SIZE  rcvd: 128
```

O ponto (.) é substituído por um sinal de arroba (@) no endereço de e-mail. Neste exemplo, o endereço de e-mail do administrador é `awsdns-hostmaster@amazon.com`.

## Configuração padrão

Existem muitos tipos de configuração padrão para DNS. Mas esses são os mais importantes do ponto de vista administrativo.

Todos os servidores DNS trabalham com três tipos diferentes de arquivos de configuração:

1. arquivos de configuração DNS local
2. arquivos de zona
3. arquivos de resolução de nomes reversos

Por exemplo o servidor de DNS Bind9, seu arquivo de configuração local (**named.conf**) é dividido em duas seções, primeiramente a seção de opções para configurações gerais e, em segundo lugar, as entradas de zona para os domínios individuais. Os arquivos de configuração local são geralmente:

- `named.conf.local`
- `named.conf.options`
- `named.conf.log`

Ele contém o RFC associado onde podemos personalizar o servidor para nossas necessidades e nossa estrutura de domínio com as zonas individuais para diferentes domínios. O arquivo de configuração `named.conf`é dividido em várias opções que controlam o comportamento do servidor de nomes. Uma distinção é feita entre `global options`e `zone options`.

As configurações globais são gerais e afetam todas as zonas. Uma opção de zona afeta somente a zona a qual foi atribuída. As opções não listadas em **named.conf** tem o valor padrão

Se uma opção for global e específica de zona, então a opção de zona tem precedência.

### Configuração de DNS local

```bash
cat /etc/bind/named.conf.local
```

```txt
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

Aqui podemos definir diferentes zonas. Essas zonas são divididas em arquivos individuais, que na maioria dos casos são destinados principalmente a um único domínio.

Exceções são ISP e servidores DNS públicos.

A `zone file` é um arquivo de texto que descreve uma zona DNS com o formato de arquivo BIND. O formato de arquivo BIND é o formato de arquivo de zona preferido pela indústria e agora está bem estabelecido no software de servidor DNS.

Deve haver precisamente um registro `SOA` e pelo menos um registro `NS`.

O principal objetivo dessas regras globais é melhorar a legibilidade dos arquivos de zona.

Resumindo, aqui, todos `forward records`são inseridos de acordo com o formato BIND. Isso permite que o servidor DNS identifique a qual domínio, nome de host e função os endereços IP pertencem.

### Arquivos de zona

```bash
cat /etc/bind/db.domain.com
```

```txt
;
; BIND reverse data file for local loopback interface
;
$ORIGIN domain.com
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

      IN     MX     10     mx.domain.com.
      IN     MX     20     mx2.domain.com.

             IN     A       10.129.14.5

server1      IN     A       10.129.14.5
server2      IN     A       10.129.14.7
ns1          IN     A       10.129.14.2
ns2          IN     A       10.129.14.3

ftp          IN     CNAME   server1
mx           IN     CNAME   server1
mx2          IN     CNAME   server2
www          IN     CNAME   server2
```

Para que o endereço IP seja resolvido a partir do `Fully Qualified Domain Name`( `FQDN`), o servidor DNS deve ter um arquivo de pesquisa reversa.

 Neste arquivo, o nome do computador (FQDN) é atribuído ao último octeto de um endereço IP, que corresponde ao respectivo host, usando um registro `PTR`.
 
### Arquivos de zona de resolução de nome reverso

```bash
cat /etc/bind/db.10.129.14
```

```txt
;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
...SNIP...
```

### Configurações perigosas

Porque o DNS pode ficar muito complicado e é muito fácil que erros se infiltrem neste serviço, forçando um administrador a contornar o problema até encontrar uma solução exata.

| **Opção**         | **Descrição**                                                                         |
| ----------------- | ------------------------------------------------------------------------------------- |
| `allow-query`     | Define quais hosts têm permissão para enviar solicitações ao servidor DNS.            |
| `allow-recursion` | Define quais hosts têm permissão para enviar solicitações recursivas ao servidor DNS. |
| `allow-transfer`  | Define quais hosts têm permissão para receber transferências de zona do servidor DNS. |
| `zone-statistics` | Coleta dados estatísticos de zonas.                                                   |

## Footprint the service

O footprinting em servidores DNS é feito como resultado das solicitações que enviamos.

Então, primeiro de tudo, o servidor DNS pode ser consultado sobre quais outros servidores de nomes são conhecidos. Fazemos isso usando o registro NS e a especificação do servidor DNS que queremos consultar usando o `@`caractere.

### DIG - Consultas NS

```sh
dig ns inlanefreight.htb @10.129.14.128
```

```txt
;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
...SNIP...
```

Às vezes também é possível consultar a versão de um servidor DNS usando uma consulta de classe CHAOS e tipo TXT. No entanto, essa entrada deve existir no servidor DNS. Para isso, poderíamos usar o seguinte comando:

### DIG - Consulta de versão

```bash
dig CH TXT version.bind 10.129.120.85
```

```txt
; <<>> DiG 9.10.6 <<>> CH TXT version.bind
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47786
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1"

;; ADDITIONAL SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1-Debian"

;; Query time: 2 msec
;; SERVER: 10.129.120.85#53(10.129.120.85)
;; WHEN: Wed Jan 05 20:23:14 UTC 2023
;; MSG SIZE  rcvd: 101
```

Podemos usar a opção `ANY`para visualizar todos os registros disponíveis. É importante notar que nem todas as entradas das zonas serão mostradas.

### DIG - ANY Query

```bash
dig any inlainefreight.htb @q0..129.14.128
```

```txt
; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7649
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 064b7e1f091b95120100000061476865a6026d01f87d10ca (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:42:13 CEST 2021
;; MSG SIZE  rcvd: 437
```

`Zone transfer`refere-se à transferência de zonas para outro servidor no DNS, que geralmente acontece pela porta TCP 53.

Este procedimento é abreviado `Asynchronous Full Transfer Zone`( `AXFR`).

 Quando alterações são feitas, deve-se garantir que todos os servidores tenham os mesmos dados. A sincronização entre os servidores envolvidos é realizada pela transferência de zona.

Usando uma chave secreta `rndc-key`, que vimos inicialmente na configuração padrão, os servidores garantem que se comuniquem com seu próprio mestre ou escravo

A transferência de zona envolve a mera transferência de arquivos ou registros e a detecção de discrepâncias nos conjuntos de dados dos servidores envolvidos.

Os dados originais de uma zona estão localizados em um servidor DNS, chamado de servidor ``primário`` de nomes para esta zona.

Para aumentar a confiabilidade, realizar distribuição de carga  ou proteger o primário, são criados servidores secundários 

Para alguns `Top-Level Domains`( `TLDs`), tornar os arquivos de zona para o `Second Level Domains`acessíveis em pelo menos dois servidores é obrigatório.

O escravo busca o registro `SOA` da zona relevante do mestre em certos intervalos, o chamado tempo de atualização, geralmente uma hora, e compara os números de série.

Se o número de série do registro `SOA` do mestre for maior que a do escravo, os dados não correspondem mais.

### DIG - Transferência de Zona AXFR

```sh
dig axfr inlanefreight.htb @10.129.14.128
```

Se o administrador usasse uma sub-rede para a opção `allow-transfer` para fins de teste ou como uma solução alternativa ou a definisse como `any`, todos consultariam o arquivo de zona inteiro no servidor DNS.

Além disso, outras zonas podem ser consultadas, o que pode até mostrar endereços IP internos e nomes de host.

### DIG - Transferência de Zona AXFR - Interna

```bash
dig axfr internal.inlanefreight.htb @10.129.14.128
```

Os registros `A` individuais com os nomes de host também podem ser descobertos com a ajuda de um ataque de força bruta. Para fazer isso, precisamos de uma lista de possíveis nomes de hos

Uma opção seria executar um comando `for-loop`no Bash que lista essas entradas e envia a consulta correspondente ao servidor DNS desejado.

### Força bruta de subdomínios

```sh
for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

#ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
#mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
#app.inlanefreight.htb.  604800  IN      A       10.129.18.15
```

Uma das ferramentas que podem ser usadas é, por exemplo, [DNSenum](https://github.com/fwaeytens/dnsenum) .

```sh
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```

```txt
dnsenum VERSION:1.2.6

-----   inlanefreight.htb   -----


Host's addresses:
__________________



Name Servers:
______________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136


Mail (MX) Servers:
___________________



Trying Zone Transfers and getting Bind Versions:
_________________________________________________

unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 900 thread 1.

Trying Zone Transfer for inlanefreight.htb on ns.inlanefreight.htb ...
AXFR record query failed: no nameservers


Brute forcing with /home/cry0l1t3/Pentesting/SecLists/Discovery/DNS/subdomains-top1million-110000.txt:
_______________________________________________________________________________________________________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136
mail1.inlanefreight.htb.                 604800   IN    A        10.129.18.201
app.inlanefreight.htb.                   604800   IN    A        10.129.18.15
ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136

...SNIP...
done.
```
















































