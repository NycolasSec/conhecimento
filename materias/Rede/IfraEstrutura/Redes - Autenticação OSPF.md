#hardening #redes 

## Baseado em senha simples sem autenticação
As chaves são trocadas em texto claro e podem ser interceptadas e decodificadas facilmente.

```ios
R2>enable
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface loopback 0
R2(config-if)#ip address 10.1.1.1 255.255.255.255
R2(config-if)#interface fast 0/0
R2(config-if)#ip address 10.10.1.1 255.255.255.0
R2(config-if)#ip ospf authentication-key senhasec
R2(config-if)#!
R2(config-if)#router ospf 10
R2(config-router)#network 10.10.1.0 0.0.0.255 area 0
R2(config-router)#area 0 authentication
R2(config-router)#end
R2#
```


## Autenticação forte (Com hash ND5)
Utilizam um algoritmo baseado no próprio pacote OSPF, na chave e no ID da chave para gerar um “message digest” ou hash MD5, o qual é inserido no pacote e enviado para a outra ponta.

```ios
interface loopback 0
ip address 10.1.1.1 255.255.255.255
!
interface fast 0/0
ip address 10.10.1.1 255.255.255.0
ip ospf message-digest-key 2 md5 senhasecreta
!
router ospf 10
network 10.10.1.0 0.0.0.255 area 0
area 0 authentication message-digest
```