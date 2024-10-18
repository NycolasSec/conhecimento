Muitas das configurações que são aplicadas no roteador também são aplicadas no switch, pois são configurações gerais de gerência e segurança.

---
---
#### Configurações gerais
---
- hostname
- enable password
- console password 
- banner
- no ip domain-loookup
- logging synchronous

---
---
### SVI (Switched Virtual Interface)
---
Um `switch` layer 2 não suporta endereçamento IP nativamente, para termos acesso a esse recurso precisamos criar uma `SVI`, e então endereçarmos ela. Ela é desabilitada por padrão e inicia no mesmo domínio de broadcast das portas físicas.

Fazemos essa configuração através de uma `VLAN`(Virtual Lan).
```IOS
Switch(config)# interface vlan 1
Switch(config-if)# ip address <address> <subnet mask> 
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway <default-gateway>
```

---
---
### Verification
---
Temos também alguns comandos para verificar o estado do `Switch`.

---
#### Verification
**`Switch# ping 192.168.0.1`**
Envia um ``ECHO ICMP REQUEST`` para o dispositivo de endereço 192.168.0.1.

**`Switch# traceroute 192.168.0.1`**
Envia um ``ECHO ICMP REQUEST`` para o dispositivo de endereço 192.168.0.1. E mostra os dispositivos pelo qual o pacote passou.

---
#### Show
**`Switch(config)# do show ip interface brief`**
Mostra uma descrição de todas as portas do **device**.

**`Switch# show running-configuration`**
Exibe as configurações que estão em vigor no dispositivo.

**`Switch# show version`**

**`Switch# show mac address-table`**
Mostra a tabela de endereços MAC de um device.

>[!info] Os comando `show`, só pode ser executado na `root`(`Device#`) do sistema, mas podemos forçar a execução desse comando usando o `do`.

---
---
### Configuração de exemplo (Switch para host)
---
Aqui está uma configuração de exemplo de um switch.

---
#### SW1
```IOS
Switch> enable
Switch# configure terminal

Switch(config)# hostname SW1
SW1(config)# interface GigabitEthernet1/0/5

SW1(config-if)# description **Connection to Bob Laptop**
SW1(config-if)# switchport mode access
SW1(config-if)# no shutdown
```

Caso necessário, podemos dizer que a interface será full duplex.
```IOS
SW1(config)# interface GigabitEthernet1/0/5
SW1(config-if)# duplex full 
```





















