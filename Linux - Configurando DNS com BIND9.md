## Instalação

```bash
sudo dnf install bind bind-utils
# ou
sudo apt install bind bind-utils
```

>[!IMPORTANT] O processo do BIND9 é chamado de `named`. Como tal muitos arquivos se referem a `named` em vez de `BIND`.

---
## Servidor DNS primário
A configuração do BIND consiste em vários arquivos que são incluídos no arquivo de configuração principal `named.conf`.

>[!important] Supondo que temos um domínio `corp.com` e o nosso servidor será o `ns1`. Portanto seu FQDN será ``ns1.corp.com``

### Arquivo de configuração
```bash
vim /etc/named.conf
```

### ACLs
Podemos criar grupos de hosts para podermos dar as devidas configurações.

##### named.conf
```txt
acl "trusted"{
	10.128.10.11; # ns1 - Name Server
	10.128.10.12; # ns2 - Name Server
	10.128.10.13; # ds - DHCP Server
	10.128.10.14; # ws - Web Server
}
```
 Aqui criamos uma lista chamada `"trusted"`. Assim podemos atribuir uma configuração a todo esse grupo.

### Transferência de queries
Em options podemos definir algumas configurações do servidor. Nessas configurações podemos passar hosts ou ACLs.

##### named.conf
```txt
options{
	allow-transfer {10.128.10.12;};
	allow-query {trusted;};
	<SNIP>
}
```
`allow-transfer` - Define quais hosts podem fazer transferência de zona.
`allow-query` - Define quais hosts podem fazer queries ao servidor.

### Arquivo de zonas
Podemos colocar toda configuração em um único arquivo, mas por questões de organização, separamos as configurações em arquivos separados e incluímos no arquivo de configuração principal.

##### named.conf
```txt
include "/etc/named/named.conf.local";
```
Precisamos adicionar essa linha para incluir o arquivo ``named.conf.local`` ás nossas configurações.

>[!IMPORTANT] Caso queira usar outro nome no arquivo de zona como : `named.zones.conf`, basta substituir onde está `named.conf.local`.

---
## Arquivo local
```bash
sudo vi /etc/named/named.conf.local
```
Nesse arquivo apenas mapeamos onde ficará o arquivo de cada zona.

#### Mapeando zona direta
Uma zona direta mapeia diretamente um nome de um host para um IP.
##### named.conf.local
```txt
zone "corp.com"{
	type master;
	file "/etc/named/zones/db.corp.com"; # zone file path
};
```

#### Mapeando zona reversa
Uma zona reversa mapeia um IP para um host. Supondo que nossa sub-rede privada seja _10.128.0.0/16_ , adicione a zona reversa com as seguintes linhas.
##### named.conf.local
```txt
zone "128.10.in-addr.arpa"{
	type master;
	file "/etc/named/zones/db.10.128"; # 10.128.0.0/16
};
```


>[!IMPORTANT] Observe que o nome da nossa zona reversa começa com `128.10`, que é a reversão do octeto de `10.128`.


## Arquivo de zona de encaminhamento
O arquivo de zona de encaminhamento é onde definimos registros DNS para pesquisas de DNS de encaminhamento.

Vamos criar o diretório onde nossos arquivos de zona residirão. De acordo com nossa configuração _named.conf.local_ , esse local deve ser `/etc/named/zones`:

```bash
sudo chmod 755 /etc/named
sudo mkdir /etc/named/zones
```

Agora vamos editar nosso arquivo de zona de encaminhamento:
```bash
sudo vi /etc/named/zones/db.corp.com
```

#### SOA
O registro ``SOA`` mantém algumas configurações gerais da zona como o FQDN do name server, o endereço de email do administrador (onde substituímos o `@` por um `.`), e outras informações.

##### db.corp.com
```txt
@       IN      SOA     ns1.corp.com. admin.nyc3.example.com. (
			3           ; Serial
			604800		; Refresh
			86400		; Retry
			2419200		; Expire
			604800 )	; Negative Cache TTL
```

>[!IMPORTANT] Sempre que modificarmos a zona devemos incrementar o valor do serial.

#### NS
Indica quais são os Name Servers.

##### db.corp.com
```txt
; name servers - NS records
	IN	NS	ns1.corp.com.
	IN	NS	ns2.corp.com.
```

#### A
Os registros A mapeiam um host para um endereço IPv4 enquanto o AAA mapeia para um endereço IPv6.

##### db.corp.com
```txt
;name servers - A records
ns1	IN	A	10.128.10.11; # ns1 - Name Serve
ns2	IN	A	10.128.10.12; # ns2 - Name Server
ds	IN	A	10.128.10.13; # ds - DHCP Server
ws	IN	A	10.128.10.14; # ws - Web Server

; 10.128.0.0/16 -A records

admin	IN	A	10.128.10.101
dev-web	IN	A	10.128.10.102
```

---
## Arquivo de zona de encaminhamento reverso
O arquivo de zona de encaminhamento reverso é onde definimos registros DNS para pesquisas de DNS de encaminhamento reverso.

Com o diretório onde nossos arquivos de zona residirão criado (`/etc/named/zones`), vamos editar o nosso arquivo de zona de encaminhamento reverso.

```bash
sudo vi /etc/named/zones/db.10.128
```

#### SOA
O registro ``SOA`` mantém algumas configurações gerais da zona como o FQDN do name server, o endereço de email do administrador (onde substituímos o `@` por um `.`), e outras informações.

##### db.10.128
```txt
@       IN      SOA     ns1.corp.com. admin.nyc3.example.com. (
			3           ; Serial
			604800		; Refresh
			86400		; Retry
			2419200		; Expire
			604800 )	; Negative Cache TTL
```

>[!IMPORTANT] Sempre que modificarmos a zona devemos incrementar o valor do serial.

#### NS
Indica quais são os Name Servers. Somente 1 é necessário.

##### db.10.128
```txt
; name servers - NS records
	IN	NS	ns1.corp.com.
	IN	NS	ns2.corp.com.
```

#### PTR
É como se fosse o registro A e AAA do arquivo de encaminhamento direto. Temos de indicar a parte que representa o host de fora inversa (caso seja ``10.11`` vamos indicar ``11.10``)e o FQDN do host.

Para que possamos indicar somente a parte do host devemos indicar o parâmetro ``@ORIGIN``
##### db.10.128
```txt
$ORIGIN 10.128.IN-ADDR.ARPA
```

##### db.10.128
```txt
; address server - PTR records
11.10	IN	PTR	ns1.corp.com. ; # ns1 - Name Serve
12.10	IN	PTR	ns2.corp.com. ; # ns2 - Name Server
13.10	IN	PTR	ds.corp.com.  ; # ds - DHCP Server
14.10	IN	PTR	ws.corp.com.  ; # ws - Web Server

; 10.128.0.0/16 -PTR records

101.10	IN	PTR	admin.corp.com.
102.10	IN	PTR	dev-web.corp.com.
```
Se não quisermos que ele complete como o ``$ORIGIN`` devemos colocar um ``.`` no final do endereço reverso.















## Expansão
##### db.corp.com
```txt
$TTL	604800; Default TTL
$ORIGIN	corp.com.; Base domain

@       IN      SOA     ns1.corp.com. admin.nyc3.example.com. (...);
```

Caso tenhamos configurado o `$ORIGIN`, só precisaremos colocar o nome do host, pois ele irá completar automaticamente. Mas se colocarmos o ponto final (`.`) no final do nome do host, estamos indicando que não queremos que ele complete.

O `@` expande para o nome da zona.