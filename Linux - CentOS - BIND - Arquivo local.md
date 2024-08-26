**Arquivo**
```sh
sudo vi /etc/named/named.conf.local
```
O arquivo deve estar vazio. Aqui, especificaremos nossas zonas forward e reverse.

```local
zone "corp.com" {
    type master;
    file "/etc/named/zones/db.corp.com"; # zone file path
};
```

Supondo que nossa sub-rede privada seja _10.128.0.0/16_ , adicione a zona reversa com as seguintes linhas (observe que o nome da nossa zona reversa começa com “128.10”, que é a reversão do octeto de “10.128”):
```local
zone "128.10.in-addr.arpa" {
    type master;
    file "/etc/named/zones/db.10.128";  # 10.128.0.0/16 subnet
    };
```
