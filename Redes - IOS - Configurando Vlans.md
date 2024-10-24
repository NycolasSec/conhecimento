VLAN é uma forma de criarmos vários domínios de broadcast em um equipamento. Assim podemos segmentar em grupos e aplicar configurações nesses grupos.

![[Pasted image 20241022110044.png]]

---
---
#### dot1q
---
O protocolo dot1q ==é um padrão que insere uma tag de 4 bytes no quadro Ethernet original==, criando um novo quadro ``dot1q``. A tag contém a ID da VLAN, que identifica a VLAN à qual o quadro pertence. 

O ==dot1q opera na camada 2 do modelo OSI== e modifica o quadro original alterando a sequência de verificação de quadros (``FCS``) campo.  O ==dot1q é um padrão IEEE==, enquanto o Inter-Switch Link Protocol (ISL) é um protocolo proprietário da Cisco. 

O protocolo VLAN é usado para organizar vários dispositivos dentro de uma rede física única sem precisar fazer mudanças físicas na infraestrutura.  O Identificador de ==VLAN é um campo de 12 bits que identifica exclusivamente a VLAN== à qual o quadro pertence. O campo pode ter um valor entre 0 e 4095.

``802.1q`` é o protocolo que define como a VLAN é injetada no campo.

---
---
#### Port mode
---
Precisamos definir qual porta tem acesso a qual VLAN, para que o switch saiba para onde mandar o tráfego que ele recebe. Caso uma interface tenha apenas uma VLAN atribuída, ela será definida como `access port` (porta de acesso) e depois dizemos a qual VLAN ela será atribuída. Caso uma interface for usada para transportar as nossas VLANs, ela será definida como `trunk`.

---
---
#### Configuração VLAN
---
Primeiro definimos o ID da nossa VLAN, e depois definimos o seu nome.

```IOS
Switch(config)# vlan 10
Switch(config-vlan)# name Suporte 
```

---
---
#### Configuração Access Port
---
Uma interface em modo ``access`` é configurada para um domínio de broadcast (VLAN).

```IOS
Switch(config)# interface range Ethernet 0/0-3
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport mode access vlan 10
```

Assim configuramos um range de interfaces para ter acesso a VLAN 10.

---
---
#### Vendo as informações
---
Para sabermos os nomes e as interfaces de cada VLAN, podemos emitir os comandos:

```IOS
Switch# show vlan brief
```
![[Pasted image 20241022132031.png]]
Traz as informações de forma resumida.

```IOS
Switch# show vlan
```
![[Pasted image 20241022132114.png]]
Traz as informações de forma completa.

```IOS
Switch# show interface ethernet 0/0 switchport
```
![[Pasted image 20241022133033.png]]
Traz informações da interface relativas ao seu modo.






























































