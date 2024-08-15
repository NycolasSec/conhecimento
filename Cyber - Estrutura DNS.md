O Domain Name System (DNS) é comparado a uma "lista telefônica da Internet". Ele traduz nomes de domínio (como `www.exemplo.com`) em endereços IP (como `192.0.2.1`), necessários para conectar-se a servidores. O DNS facilita a navegação na web ao permitir que usuários acessem servidores por nomes em vez de números IP. Ele também pode realizar a tradução inversa, convertendo endereços IP em nomes de domínio.

Os componentes de um serviço DNS consistem em:
- `Name servers`
- `Zones`
- `Domain names`
- `IP addresses`

Os servidores de nomes utilizam arquivos de zona, que são listas de entradas para um domínio específico, semelhantes a uma lista telefônica para uma cidade. Esses arquivos contêm endereços IP, domínios e hosts. Em sistemas Linux ou Windows, o arquivo de zona local é chamado de "hosts" e pode ser encontrado em `/etc/hosts` no Pwnbox.

## Estrutura de domínio

Tomemos como exemplo o seguinte nome de domínio totalmente qualificado ( `FQDN` ):

- `www.domain-A.com`

Um domínio é usado para dar nomes reais aos endereços IP do computador e, ao mesmo tempo, dividi-los em uma estrutura hierárquica. Essa estrutura hierárquica se parece com uma árvore de diretórios de um sistema operacional.

```shell-session
.
├── com.
│   ├── domain-A.
│   │   ├── blog.
│   │   ├── ns1.
│   │   └── www.
│   │ 
│   ├── domain-B.
│   │   └── ...
│   └── domain-C.
│       └── ...
│
└── net.
│   ├── domain-D.
│   │   └── ...
│   ├── domain-E.
│   │   └── ...
│   └── domain-F.
│       └── ...
└── ...
│   ├── ...
│   └── ...
```

Para deixar um pouco mais claro, vejamos um exemplo real de como essa estrutura pode se parecer com uma nomenclatura apropriada.

![[tooldev-dns 1.webp]]

Cada domínio consiste em pelo menos duas partes:

1. `Top-Level Domain`( `TLD`)
2. `Domain Name`

Do último exemplo, o nome de domínio seria " `inlanefreight`" e o TLD então " `com`". Se olharmos para o " `inlanefreight`," veremos que ele contém alguns chamados `subdomains`( `dev`, `www`, `mail`). Esses subdomínios podem representar um único host ou hosts virtuais ( `vHosts`). Os servidores DNS são divididos em quatro tipos diferentes:

- `Recursive resolvers`( `DNS Recursor`)
- `Root name server`
- `TLD name server`
- `Authoritative name servers`

## Resolvedor DNS recursivo

O resolvedor recursivo atua como intermediário entre o cliente e o servidor de nomes. Ao receber uma consulta DNS, ele pode responder com dados em cache ou, se necessário, consultar servidores de nomes raiz, TLD e autoritativos.

Após obter a resposta do servidor autoritativo, o resolvedor a envia ao cliente e armazena as informações em cache. Assim, se outro cliente solicitar o mesmo endereço IP recentemente, o resolvedor pode fornecer a resposta diretamente do cache, sem precisar consultar os servidores de nomes novamente.

## Servidor de nomes raiz

Existem 13 servidores de nomes raiz que podem ser acessados por endereços IPv4 e IPv6, mantidos pela organização sem fins lucrativos ICANN. Esses servidores possuem arquivos de zona com nomes de domínio e endereços IP dos TLDs.

Todo resolvedor recursivo conhece esses servidores, que são os primeiros pontos de contato na busca de entradas DNS. Quando um resolvedor recursivo consulta um servidor raiz, ele é direcionado ao servidor de nomes TLD apropriado.

Os servidores de nomes raiz podem ser encontrados no domínio `root-servers.net`, com letras correspondentes como subdomínios.

```shell-session
NycolasES6@htb[/htb]$ dig ns root-servers.net | grep NS | sort -u                                                                          

; EDNS: version: 0, flags:; udp: 4096
;; ANSWER SECTION:
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1
;root-servers.net.		IN	NS
root-servers.net.	6882	IN	NS	a.root-servers.net.
root-servers.net.	6882	IN	NS	b.root-servers.net.
root-servers.net.	6882	IN	NS	c.root-servers.net.
root-servers.net.	6882	IN	NS	d.root-servers.net.
root-servers.net.	6882	IN	NS	e.root-servers.net.
root-servers.net.	6882	IN	NS	f.root-servers.net.
root-servers.net.	6882	IN	NS	g.root-servers.net.
root-servers.net.	6882	IN	NS	h.root-servers.net.
root-servers.net.	6882	IN	NS	i.root-servers.net.
root-servers.net.	6882	IN	NS	j.root-servers.net.
root-servers.net.	6882	IN	NS	k.root-servers.net.
root-servers.net.	6882	IN	NS	l.root-servers.net.
root-servers.net.	6882	IN	NS	m.root-servers.net.
```

Esses 13 servidores de nomes raiz representam os 13 tipos diferentes de servidores de nomes raiz. Isso não significa que eles se espalham apenas por 13 hosts, mas por mais de 600 cópias desses servidores de nomes raiz no mundo todo.

## Servidor de nomes TLD

Os servidores de nomes TLD são responsáveis por gerenciar informações sobre todos os domínios que compartilham o mesmo TLD e são administrados pela Internet Assigned Numbers Authority (IANA). Por exemplo, todos os domínios com o TLD ".com" são gerenciados pelo servidor de nomes TLD correspondente. Após receber uma resposta de um servidor de nomes raiz, o resolvedor recursivo envia uma consulta ao servidor de nomes TLD responsável para encontrar informações sobre um domínio registrado sob esse TLD.

## Servidor de nomes autoritativo

Os servidores de nomes autoritativos armazenam informações de registro DNS para domínios e fornecem respostas com endereços IP e outras entradas DNS para páginas da web. Quando um resolvedor recursivo recebe uma resposta de um servidor TLD, ele encaminha a consulta para o servidor de nomes autoritativo. Este servidor é a etapa final na obtenção do endereço IP de um domínio.






