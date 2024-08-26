A configuração do BIND consiste em vários arquivos, que são incluídos no arquivo de configuração principal, `named.conf`. Esses nomes de arquivo começam com “named” porque esse é o nome do processo que o BIND executa. Começaremos configurando o arquivo de opções.

Supondo que temos um domínio `corp.com` e o nosso servidor será o ns1. Portanto seu FQDN será ``ns1.corp.com``

**Arquivo de configuração**
```
sudo vi /etc/named.conf
```

**Hosts confiáveis**
```conf"
acl "trusted" {
        10.128.10.11;    # ns1 - can be set to localhost
        10.128.20.12;    # ns2
        10.128.100.101;  # host1
        10.128.200.102;  # host2
};
```
Aqui definimos os clientes que poderão fazer consultas recursivas.

**Porta e IP**
```conf
options {
        listen-on port 53 { 127.0.0.1; 10.128.10.11; };
#        listen-on-v6 port 53 { ::1; };
<SNIP>
}
```
Aqui definimos a porta e o endereço IP privado; Podemos configurar o IPv4 e IPv6. Se não quisermos usar basta comentarmos s linha.

**Transferências e queries**
```conf
options {
		allow-transfer { 10.128.20.12; };
		allow-query { trusted; };
<SNIP>
}
```
Definimos qual host pode fazer a transferência de zona, e quais hosts podem fazer ``queries``; nesse caso os que estiverem no ``acl trusted``.

**Arquivo de zonas**
```conf
include "/etc/named/named.conf.local";
```
Precisamos adicionar essa linha para incluir o arquivo ``named.conf.local`` ás nossas configurações.

