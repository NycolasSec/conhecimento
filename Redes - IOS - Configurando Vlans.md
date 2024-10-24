VLAN é uma forma de dividirmos um domínio de broadcast em vários.  Assim podemos dividir um domínio de broadcast em grupos e aplicar configurações nesses grupos.

![[Pasted image 20241022110044.png]]

Antes mesmo de configurarmos uma VLAN no switch, ele já vem com uma VLAN padrão ( ou VLAN nativa) a qual todas as interfaces fazem parte de início.

---
---
#### VLAN nativa
---
Em cada switch podemos mudar qual será a VLAN nativa, que por padrão é a ``VLAN 1``. Desse modo, sempre que algum pacote chegar sem uma ``VLAN`` especificada o switch insere a ``VLAN nativa``. E sempre que o switch enviar algum pacote da ``VLAN nativa``, ele manda sem uma VLAN especificada. Assim o switch que receber o pacote irá assumir que é da ``VLAN nativa``.

![[Pasted image 20241024111030.png]]

---
---
#### VLAN de gerência
---
No caso da VLAN de gerência, ela é uma ==interface virtual com IP== anexada á uma interface física. Como precisamos acessar o device remotamente, precisamos ter um IP configurado, e como o switch é um device layer 2, criamos uma interface virtual, o que nos permite colocar um IP, e então anexar a uma interface física.

```IOS
Switch(config)# interface vlan 15
Switch(config-if)# ip address 10.0.0.15 255.255.255.0
Switch(config-if)# exit
```
Primeiro criamos uma VLAN e atribuímos um IP a ela.

```IOS
Switch(config)# interface ethernet 0/5
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 15

Switch(config-if)# exit
Switch(config)# interface vlan 15
Switch(config-if)# no shutdown
```
Depois colocamos uma interface nessa VLAN, e ligamos a VLAN.

>[!important] Nunca esqueça de ligar a interface virtual (SVI)

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
Precisamos definir qual porta tem acesso a qual VLAN, para que o switch saiba para onde mandar o tráfego que ele recebe. Caso uma interface tenha apenas uma VLAN atribuída, ela será definida como `access port` (porta de acesso) e depois dizemos a qual VLAN ela será atribuída. Caso uma interface for usada para transportar todas as VLANs, ela será definida como `trunk`.

Normalmente definimos uma porta como ``trunk`` quando queremos ==compartilhar nossas **VLANs** com outro equipamento==.

![[Pasted image 20241024085148.png]]

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
Switch(config-if-range)# switchport access vlan 10
```

Assim configuramos um range de interfaces para ter acesso a VLAN 10.

---
---
#### Configuração Trunk Port
---
Há casos em que queremos ter a mesma ``VLAN`` em dois dispositivos, neste caso em vez darmos acesso á apenas uma ``VLAN``, podemos colocar a porta no modo ``trunk``, assim passamos todas as nossas ``VLANs`` por ela.

```IOS
Switch(config)# interface Ethernet 1/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
```
Primeiro definimos o encapsulamento(`802.1q` ou `ISL`) e depois colocamos como `trunk`.

Por padrão uma porta no modo trunk da acesso a todas as VLANs, mas podemos definir quais VLANs queremos bloquear ou permitir.

```IOS
Switch(config)# interface Ethernet 1/1
Switch(config-if)# switchport trunk allowed vlan 10,30
Switch(config-if)# switchport trunk allowed vlan except 1000
```
Primeiro permitimos o acesso as ``VLANs`` 10 e 30 e depois negamos o acesso a ``VLAN`` 1000.

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


```IOS
Switch# show interface trunk
```
![[Pasted image 20241024091500.png]]
Mostra informações das portas que estão em modo trunk.

```IOS
Switch# show interface ethernet 2/0 switchport
```
![[Pasted image 20241024091644.png]]
Mostra informações do ``switchport`` de uma interface.

---
---
#### Header VLAN
---
Pensando no cabeçalho Ethernet, ao adicionarmos a tag de VLAN, ele insere no cabeçalho Ethernet as informações definidas no protocolo `802.1q`.

![[Pasted image 20241024085723.png]]

**`VID`**: ID da VLAN.
**`Priority`**: Define a prioridade do pacote.

Se analisarmos como Wireshark ela aparecerá no `802.1q`.
![[Pasted image 20241024085939.png]]

---
----
#### OBS:
---
Quem define o VID do pacote é o switch e não os dispositivos finais.
Os pacotes pacotes enviados da VLAN nativa recebem o VID da VLAN nativa do switch que irá receber o pacote.

É importante que as portas trunk de cada device tenham a mesma VLAN nativa, por questão de compatibilidade.


















































































































