Registros DNS são instruções que estão localizadas em servidores DNS autoritativos e contêm informações sobre um domínio.

Essas entradas são escritas na sintaxe DNS que fornece aos servidores DNS as instruções apropriadas. Aqui estão os registros DNS mais comuns que encontraremos durante nossos Testes de Penetração:

| **Registro** | **Descrição**                                    |
| ------------ | ------------------------------------------------ |
| `A`          | Registros de endereço IP versão 4                |
| `AAAA`       | Registros de endereço IP versão 6                |
| `CNAME`      | Registros de nomes canônicos                     |
| `HINFO`      | Registros de informações do host                 |
| `ISDN`       | Registros da Rede Digital de Serviços Integrados |
| `MX`         | Registro de Mail exchanger                       |
| `NS`         | Registros do servidor de nomes                   |
| `PTR`        | Registros de ponteiro de pesquisa reversa        |
| `SOA`        | Início dos registros de autoridade               |
| `TXT`        | Registros de texto                               |

Existem muitas ferramentas e recursos com os quais podemos trabalhar para enviar consultas aos servidores DNS. Por exemplo, podemos usar ferramentas como:

- `dig`
- `nslookup`

## Servidores NS

Queremos automatizar o processo de enumeração para potenciais transferências de zona em servidores DNS mal configurados para verificar essa vulnerabilidade rapidamente com a ferramenta que estamos criando.

Vamos começar enumerando alguns servidores NS e verificando se uma transferência de zona DNS é possível e, se for, obter as informações sobre os subdomínios da transferência de zona.

## Registros NS

Registros `NS` representam `name server`. Esses registros especificam qual servidor DNS é o servidor autoritativo para o domínio correspondente que contém os registros DNS reais.

Frequentemente, um domínio tem vários registros NS que podem especificar servidores de nomes primários e secundários para esse domínio. Agora que sabemos nosso domínio de destino, ainda precisamos de servidores DNS com os quais interagiremos. Para isso, temos que descobrir quais servidores DNS são responsáveis ​​pelo domínio e, para isso, podemos usar a ferramenta chamada `dig`. Neste exemplo, usamos o domínio chamado:`inlanefreight.com`

#### DIG - Consultas NS
```shell-session
NycolasES6@htb[/htb]$ dig NS inlanefreight.com

<SNIP>
;; ANSWER SECTION:
inlanefreight.com.	60	IN	NS	ns2.inlanefreight.com.
inlanefreight.com.	60	IN	NS	ns1.inlanefreight.com.

<SNIP>
```

#### DIG - Consultas SOA
```shell-session
NycolasES6@htb[/htb]$ dig SOA inlanefreight.com

<SNIP>
;; ANSWER SECTION:
inlanefreight.com.	879	IN	SOA	ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

<SNIP>
```

#### NSLOOKUP - FPS
```shell-session
NycolasES6@htb[/htb]$ nslookup -type=SPF inlanefreight.com

Non-authoritative answer:
inlanefreight.com	rdata_99 = "v=spf1 include:_spf.google.com include:mail1.inlanefreight.com include:google.com ~all"
```

#### NSLOOKUP - DMARC
```shell-session
NycolasES6@htb[/htb]$ nslookup -type=txt _dmarc.inlanefreight.com

Non-authoritative answer:
_dmarc.inlanefreight.com	text = "v=DMARC1; p=reject; rua=mailto:master@inlanefreight.com; ruf=mailto:master@inlanefreight.com; fo=1;"
```



























