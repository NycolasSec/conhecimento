## Topologia
---
![[Pasted image 20240820085606.png]]

---
---
## Descrição
---
#### VTP Server
O SW0 será o VTP Server que possuirá as VLANs 10, 20, 30 e 40.

 SW1, SW2, SW3 possuirão a VLAN 40 para guest, e uma VLAN para uso próprio, conforme a ttopologia.

#### SSH
Os computadores da VLAN 40 deverão ter acesso aos Switches SW0, SW1, SW2, SW3 e SW4 aos roteadores R0 e R1 e aos servidores FTP, DHCP DNS e WEB.

#### DNS
Os servidores devem ser acessíveis pelos nomes.
``FTP`` - ftp.corp.com
``DHCP`` - dhcp.corp.com
``DNS`` - dns.corp.com
``WEB`` - corp.com

#### FTP
O SW0 E R1 deverão fazer backup no servidor FTP.

#### Eherchannel
SWI e SW2 deverão estar em Etherchannel.

---

## Configurações

### VTP


### Etherchannel

