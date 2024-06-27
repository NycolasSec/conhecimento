# Cyber - Informação de domínio

As informações de domínio são um componente central de qualquer teste de penetração e não se trata apenas dos subdomínios, mas de toda a presença na Internet.

Este tipo de informação é coletada passivamente, sem verificações diretas e ativas.
Ou seja, permanecemos ocultos e navegamos como “clientes” ou “visitantes” para evitar conexões diretas com a empresa que possam nos expor.

Prestamos atenção ao que `we see`e `we do not see`. Vemos os serviços, mas não sua funcionalidade. No entanto, os serviços estão vinculados a certos aspectos técnicos necessários à prestação de um serviço.

## Presença online

Depois de termos um conhecimento básico da empresa e dos seus serviços, podemos ter uma primeira impressão da sua presença na Internet.

O primeiro ponto de presença na Internet pode ser o `SSL certificate`site principal da empresa que podemos examinar.
Muitas vezes, esse certificado inclui mais do que apenas um subdomínio, e isso significa que o certificado é usado para vários domínios, e estes provavelmente ainda estão ativos.

![[Pasted image 20240625093954.png]]

Outra fonte para encontrar mais subdomínios é [crt.sh](https://crt.sh/) . Esta fonte são logs [de transparência de certificados](https://en.wikipedia.org/wiki/Certificate_Transparency) . A Transparência de Certificados é um processo que visa permitir a verificação de certificados digitais emitidos para conexões criptografadas à Internet.

![[DomInfo-2.webp]]

## Transparência do certificado

```bash
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .

[
  {
    "issuer_ca_id": 23451835427,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 50815783237226155,
    "entry_timestamp": "2021-08-21T06:00:17.173",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe9017d6de5eda90"
  },
  {
    "issuer_ca_id": 6864563267,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 5081529377,
    "entry_timestamp": "2021-08-21T06:00:16.932",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe90104e271c98a90"
  },
  {
    "issuer_ca_id": 113123452,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "smartfactory.inlanefreight.com",
    "name_value": "smartfactory.inlanefreight.com",
    "id": 4941235512141012357,
    "entry_timestamp": "2021-07-27T00:32:48.071",
    "not_before": "2021-07-26T23:32:47",
    "not_after": "2021-10-24T23:32:45",
    "serial_number": "044bac5fcc4d59329ecbbe9043dd9d5d0878"
  },
  { ... SNIP ...
]
```

Se necessário, também podemos filtrá-los por subdomínios exclusivos.

```bash
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

account.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
data.ttn.inlanefreight.com
*.inlanefreight.com
inlanefreight.com
integrations.ttn.inlanefreight.com
iot.inlanefreight.com
mails.inlanefreight.com
marina.inlanefreight.com
marina-live.inlanefreight.com
matomo.inlanefreight.com
next.inlanefreight.com
noc.ttn.inlanefreight.com
preview.inlanefreight.com
shop.inlanefreight.com
smartfactory.inlanefreight.com
ttn.inlanefreight.com
vx.inlanefreight.com
www.inlanefreight.com
```

A seguir, podemos identificar os hosts diretamente acessíveis pela Internet e não hospedados por provedores terceirizados. Isso ocorre porque não temos permissão para testar os hosts sem a permissão de fornecedores terceirizados.

### Servidores hospedados pela empresa

```bash
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```

Depois de vermos quais hosts podem ser investigados mais detalhadamente, podemos gerar uma lista de endereços IP com um pequeno ajuste no `cut`comando e executá-los `Shodan`.

O `Shodan` pesquisa na Internet portas TCP/IP abertas e filtra os sistemas de acordo com termos e critérios específicos. Por exemplo, portas HTTP ou HTTPS abertas e outras portas de servidor para `FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`ou `SIP`são pesquisadas.

Como resultado, podemos encontrar dispositivos e sistemas, como `surveillance cameras`,,,,, e , e vários componentes de rede `servers`.`smart home systems``industrial controllers``traffic lights``traffic controllers`

### Shodan - Lista de IPs

```bash
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
```

```bash
for i in $(cat ip-addresses.txt);do shodan host $i;done
```

Lembramos o IP `10.129.127.22`( `matomo.inlanefreight.com`) para futuras investigações ativas que desejamos realizar. Agora podemos exibir todos os registros DNS disponíveis onde podemos encontrar mais hosts.

## Registros DNS

```bash
dig any inlainefreight.com

#; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.com
#;; global options: +cmd
#;; Got answer:
#;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52058
#;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

#;; OPT PSEUDOSECTION:
#; EDNS: version: 0, flags:; udp: 65494
#;; QUESTION SECTION:
#;inlanefreight.com.             IN      ANY

#;; ANSWER SECTION:
#inlanefreight.com.      300     IN      A       10.129.27.33
#inlanefreight.com.      300     IN      A       10.129.95.250
#inlanefreight.com.      3600    IN      MX      1 aspmx.l.google.com.
#inlanefreight.com.      3600    IN      MX      10 aspmx2.googlemail.com.
#inlanefreight.com.      3600    IN      MX      10 aspmx3.googlemail.com.
#inlanefreight.com.      3600    IN      MX      5 alt1.aspmx.l.google.com.
#inlanefreight.com.      3600    IN      MX      5 alt2.aspmx.l.google.com.
#inlanefreight.com.      21600   IN      NS      ns.inwx.net.
#inlanefreight.com.      21600   IN      NS      ns2.inwx.net.
#inlanefreight.com.      21600   IN      NS      ns3.inwx.eu.
#inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"
#inlanefreight.com.      21600   IN      TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
#inlanefreight.com.      3600    IN      TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
#inlanefreight.com.      300     IN      TXT     "google-site-verification=bow47-er9LdgoUeah"
#inlanefreight.com.      3600    IN      TXT     "google-site-verification=gZsCG-BINLopf4hr2"
#inlanefreight.com.      3600    IN      TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
#inlanefreight.com.      300     IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
#inlanefreight.com.      21600   IN      SOA     ns.inwx.net. hostmaster.inwx.net. 2021072600 10800 3600 604800 3600

#;; Query time: 332 msec
#;; SERVER: 127.0.0.53#53(127.0.0.53)
#;; WHEN: Mi Sep 01 18:27:22 CEST 2021
#;; MSG SIZE  rcvd: 940
```

Vejamos o que aprendemos aqui, vemos um registro IP, alguns servidores de e-mail, alguns servidores DNS, registros TXT e registro SOA.

- Registros `A`: Reconhecemos os endereços IP que apontam para um subdomínio específico através do registro `A`. Aqui vemos apenas um que já conhecemos.

- Registros `MX`: Os registros do servidor de e-mail nos mostram qual servidor de e-mail é responsável pelo gerenciamento dos e-mails da empresa. Como é feito pelo Google em nosso caso, devemos observar isso e ignorá-lo por enquanto.

- Registros `NS`: Esses tipos de registros mostram quais servidores de nomes são usados para resolver o FQDN para endereços IP. A maioria dos provedores de hospedagem usa seus próprios servidores de nomes, facilitando a identificação do provedor de hospedagem.

- Registros `TXT`: Esse tipo de registro geralmente contém chaves de verificação para diferentes provedores terceirizados e outros aspectos de segurança do DNS, como SPF, DMARC e DKIN, que são responsáveis por verificar e confirmar a origem dos e-mails enviados

Aqui já podemos ver algumas informações valiosas se olharmos mais de perto os resultados.

```txt
...SNIP... TXT     "MS=ms92346782372"
...SNIP... TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
...SNIP... TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
...SNIP... TXT     "google-site-verification=bow47-er9LdgoUeah"
...SNIP... TXT     "google-site-verification=gZsCG-BINLopf4hr2"
...SNIP... TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
...SNIP... TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
```

O que pudemos ver até agora foram entradas no servidor DNS, que à primeira vista não pareciam muito interessantes.

No entanto, não conseguimos ver os fornecedores terceirizados por trás das entradas mostradas à primeira vista. As principais informações que podemos ver agora são:

|                                          |                                           |                                                                               |
| ---------------------------------------- | ----------------------------------------- | ----------------------------------------------------------------------------- |
| [Atlassiano](https://www.atlassian.com/) | [Gmail](https://www.google.com/gmail/)    | [LogMeIn](https://www.logmein.com/)                                           |
| [Arma postal](https://www.mailgun.com/)  | [Panorama](https://outlook.live.com/owa/) | [](https://www.inwx.com/en)ID/nome de usuário [INWX](https://www.inwx.com/en) |
| 10.129.24.8                              | 10.129.27.2                               | 10.72.82.106                                                                  |

[Google Gmail](https://www.google.com/gmail/) indica que o Google é usado para gerenciamento de e-mail. Portanto, também pode sugerir que possamos acessar pastas ou arquivos abertos do GDrive com um link.

[O LogMeIn](https://www.logmein.com/) é um local central que regula e gerencia o acesso remoto em muitos níveis diferentes. No entanto, a centralização de tais operações é uma faca de dois gumes.Se o acesso como administrador a esta plataforma for obtido, também se tem acesso completo a todos os sistemas e informações.

[Mailgun](https://www.mailgun.com/) oferece várias APIs de e-mail, retransmissões SMTP e webhooks com os quais os e-mails podem ser gerenciados. Isso nos diz para mantermos os olhos abertos para interfaces API que podemos então testar para várias vulnerabilidades, como IDOR, SSRF, POST, solicitações PUT e muitos outros ataques.

[O Outlook](https://outlook.live.com/owa/) é outro indicador para gerenciamento de documentos. As empresas costumam usar o Office 365 com OneDrive e recursos de nuvem, como blob do Azure e armazenamento de arquivos. O armazenamento de arquivos do Azure pode ser muito interessante porque funciona com o protocolo SMB.

A última coisa que vemos é [INWX](https://www.inwx.com/en) . Esta empresa parece ser um provedor de hospedagem onde domínios podem ser adquiridos e registrados. O registro TXT com o valor “MS” é frequentemente usado para confirmar o domínio. Na maioria dos casos, é semelhante ao nome de usuário ou ID usado para fazer login na plataforma de gerenciamento.

[Anterior](https://academy.hackthebox.com/module/112/section/1185)




































