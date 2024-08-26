O arquivo de zona de encaminhamento é onde definimos registros DNS para pesquisas de DNS de encaminhamento. Ou seja, quando o DNS recebe uma consulta de nome, “ host1.nyc3.example.com ”, por exemplo, ele procurará no arquivo de zona de encaminhamento para resolver o endereço IP privado correspondente do host1 .

Vamos criar o diretório onde nossos arquivos de zona residirão. De acordo com nossa configuração _named.conf.local_ , esse local deve ser `/etc/named/zones`:

```sh
sudo chmod 755 /etc/named
sudo mkdir /etc/named/zones
```

Agora vamos editar nosso arquivo de zona de avanço:

```sh
sudo vi /etc/named/zones/db.nyc3.example.com
```

Primeiro, você vai querer adicionar o registro SOA. Substitua o FQDN ns1 destacado pelo seu próprio FQDN, então substitua o segundo “ [nyc3.example.com](http://nyc3.example.com/) ” pelo seu próprio domínio. Toda vez que você editar um arquivo de zona, você deve incrementar o valor _serial_ antes de reiniciar o `named`processo – nós o incrementaremos para “3”. Ele deve ficar parecido com isto:

```conf
@       IN      SOA     ns1.nyc3.example.com. admin.nyc3.example.com. (
                              3         ; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
```

Depois disso, adicione seus registros de nameserver com as seguintes linhas (substitua os nomes pelos seus). Note que a segunda coluna especifica que esses são registros “NS”:

```conf
; name servers - NS records
    IN      NS      ns1.nyc3.example.com.
    IN      NS      ns2.nyc3.example.com.
```

Em seguida, adicione os registros A para seus hosts que pertencem a esta zona. Isso inclui qualquer servidor cujo nome queremos terminar com “.nyc3.example.com” (substitua os nomes e endereços IP privados). Usando nossos nomes de exemplo e endereços IP privados, adicionaremos registros A para ns1 , ns2 , host1 e host2 assim:

```conf
; name servers - A records
ns1.nyc3.example.com.          IN      A       10.128.10.11
ns2.nyc3.example.com.          IN      A       10.128.20.12

; 10.128.0.0/16 - A records
host1.nyc3.example.com.        IN      A      10.128.100.101
host2.nyc3.example.com.        IN      A      10.128.200.102
```

Salve e saia do arquivo `db.nyc3.example.com`.

Nosso arquivo de zona de encaminhamento de exemplo final se parece com o seguinte:

```txt
$TTL    604800
@       IN      SOA     ns1.nyc3.example.com. admin.nyc3.example.com. (
			      3		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
; name servers - NS records
     IN      NS      ns1.nyc3.example.com.
     IN      NS      ns2.nyc3.example.com.

; name servers - A records
ns1.nyc3.example.com.          IN      A       10.128.10.11
ns2.nyc3.example.com.          IN      A       10.128.20.12

; 10.128.0.0/16 - A records
host1.nyc3.example.com.        IN      A      10.128.100.101
host2.nyc3.example.com.        IN      A      10.128.200.102
```


















