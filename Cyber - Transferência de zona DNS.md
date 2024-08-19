# Transferências de Zona DNS

Embora a força bruta possa ser uma abordagem frutífera, há um método menos invasivo e potencialmente mais eficiente para descobrir subdomínios – transferências de zona DNS.

## O que é uma transferência de zona
Uma transferência de zona DNS é essencialmente uma cópia por atacado de todos os registros DNS dentro de uma zona (um domínio e seus subdomínios) de um servidor de nomes para outro.

No entanto, se não for adequadamente protegido, partes não autorizadas podem baixar o arquivo de zona inteiro, revelando uma lista completa de subdomínios, seus endereços IP associados e outros dados DNS confidenciais.

![[Pasted image 20240819082503.png]]

1. `Zone Transfer Request (AXFR)`: O servidor DNS secundário inicia o processo enviando uma solicitação de transferência de zona para o servidor primário. Essa solicitação normalmente usa o tipo AXFR (Full Zone Transfer).
2. `SOA Record Transfer`: Ao receber a solicitação (e potencialmente autenticar o servidor secundário), o servidor primário responde enviando seu registro Start of Authority (SOA). O registro SOA contém informações vitais sobre a zona, incluindo seu número de série, o que ajuda o servidor secundário a determinar se seus dados de zona estão atualizados.
3. `DNS Records Transmission`: O servidor primário então transfere todos os registros DNS na zona para o servidor secundário, um por um. Isso inclui registros como A, AAAA, MX, CNAME, NS e outros que definem os subdomínios do domínio, servidores de e-mail, servidores de nomes e outras configurações.
4. `Zone Transfer Complete`: Depois que todos os registros foram transmitidos, o servidor primário sinaliza o fim da transferência de zona. Esta notificação informa ao servidor secundário que ele recebeu uma cópia completa dos dados da zona.
5. `Acknowledgement (ACK)`: O servidor secundário envia uma mensagem de confirmação ao servidor primário, confirmando o recebimento e o processamento bem-sucedidos dos dados da zona. Isso conclui o processo de transferência de zona.

## Vulnerabilidade de transferência de zona

Embora as transferências de zona sejam essenciais para o gerenciamento legítimo de DNS, um servidor DNS mal configurado pode transformar esse processo em uma vulnerabilidade de segurança significativa.

Nos primórdios da internet, permitir que qualquer cliente solicitasse uma transferência de zona de um servidor DNS era uma prática comum. Essa abordagem aberta simplificou a administração, mas abriu uma enorme brecha de segurança. Isso significava que qualquer um, incluindo agentes maliciosos, poderia pedir a um servidor DNS uma cópia completa de seu arquivo de zona, que contém uma riqueza de informações confidenciais.

As informações coletadas de uma transferência de zona não autorizada podem ser inestimáveis ​​para um invasor. Elas revelam um mapa abrangente da infraestrutura de DNS do alvo, incluindo:

- `Subdomains`: Uma lista completa de subdomínios, muitos dos quais podem não estar vinculados ao site principal ou facilmente descobertos por outros meios. Esses subdomínios ocultos podem hospedar servidores de desenvolvimento, ambientes de preparação, painéis administrativos ou outros recursos sensíveis.
- `IP Addresses`: Os endereços IP associados a cada subdomínio, fornecendo alvos potenciais para reconhecimento ou ataques posteriores.
- `Name Server Records`: Detalhes sobre os servidores de nomes autorizados para o domínio, revelando o provedor de hospedagem e possíveis configurações incorretas.

### Remediação
Felizmente, a conscientização sobre essa vulnerabilidade aumentou, e a maioria dos administradores de servidores DNS mitigou o risco. Servidores DNS modernos são tipicamente configurados para permitir transferências de zona somente para servidores secundários confiáveis, garantindo que dados de zona sensíveis permaneçam confidenciais.

No entanto, configurações incorretas ainda podem ocorrer devido a erro humano ou práticas desatualizadas. É por isso que tentar uma transferência de zona (com autorização adequada) continua sendo uma técnica de reconhecimento valiosa

#### Explorando Transferências de Zona
Você pode usar o `dig`comando para solicitar uma transferência de zona:

```shell-session
NycolasES6@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me
```

Este comando instrui `dig`a solicitar uma transferência de zona completa ( `axfr`) do servidor DNS responsável por `zonetransfer.me`. Se o servidor estiver mal configurado e permitir a transferência, você receberá uma lista completa de registros DNS para o domínio, incluindo todos os subdomínios.

```shell-session
NycolasES6@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.	300	IN	HINFO	"Casio fx-700G" "Windows XP"
zonetransfer.me.	301	IN	TXT	"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.	7200	IN	MX	0 ASPMX.L.GOOGLE.COM.
...
zonetransfer.me.	7200	IN	A	5.196.105.14
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN	TXT	"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN	SRV	0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200	IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN	AFSDB	1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200	IN	A	127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN	AFSDB	1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A	202.14.81.230
...
;; Query time: 10 msec
;; SERVER: 81.4.108.41#53(nsztm1.digi.ninja) (TCP)
;; WHEN: Mon May 27 18:31:35 BST 2024
;; XFR size: 50 records (messages 1, bytes 2085)
```

`zonetransfer.me` é um serviço configurado especificamente para demonstrar os riscos de transferências de zona para que o comando `dig` retorne o registro de zona completo.









