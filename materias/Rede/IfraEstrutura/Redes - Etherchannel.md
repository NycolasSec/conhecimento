Em algumas situações precisamos de mais largura de banda do que apenas um link pode nos proporcionar, sendo assim vários linksk podem ser conectados, no entanto o STP bloquearia links redundantes para não permitir loops de comutação.

A ideia do **Etherchannel** seria fazer um **bundle** dos links/somar os links físicos em um único link lógico.

Isso não apenas nos permite lidar com grandes volumes de tráfego, mas também proporciona redundância e balanceamento de carga, essenciais para manter nossa rede operacional mesmo em situações de falha.

## Configuração

As interfaces usadas para fazer o bundle, dever ter as mesmas configurações. Sendo assim podemos configurar mais deum aporta por vez.

Também é recomendado que seja configurado em um número par de portas, apesar de poder ser configurado em um número ímpar de portas, o balanceamento não fica tão bom.

![[Pasted image 20240628104334.png]]

Podemos notar que uma das porta do ``SW2``, foi bloqueada pelo ``STP``.

### Desligando as portas

É ideal que se faça a configuração com as interfaces desligadas.

```SW
(config)# interface range gigabitEthernet 0/1-2

(config-if-range)# shutdow
```

![[Pasted image 20240628104919.png]]

---

Os dois Switches tem de estar com as portas como **trunk**

```SW
(config-if-range)# switchport mode trunk
```

---

```SW
(config-if-range)# channel-group 1 mode active
```

Aqui estamos definindo um grupo para rodar o **Etherchannel**, é aceito do número 1 ao 6.

O modo define qual protocolo ir emos usar.

| Modo      | Protocolo |
| --------- | --------- |
| active    | LACP      |
| passive   | LACP      |
| auto      | PAgP      |
| desirable | PAgP      |
| on        |           |

No LACP se um switch estiver no modo **passive**, ele só subirá o **etherchannel** se o outro **switch** estiver no modo **active**. Mas os dois podem estar no modo **active**.

No PAgP se um switch estiver no modo **desirable**, ele só subirá o **etherchannel** se o outro **switch** estiver no modo **auto**.

normalmente se usa o padrão aberto **LACP**.

---

```SW
(config-if-range)# no shutdown
```
Aqui ligamos as interfaces denovo.

---

### Vendo informações

```SW
# show spanning-tree
```
![[Pasted image 20240628111342.png]]

```SW
# show etherchannel summary
```
![[Pasted image 20240628111522.png]]

Como as duas portas estão com **P**, elas estão `ìm port-channel`.

Caso alguma interface seja desligada, aparecerá como **D** de `down`


```SW
# show ip interface brief
```
![[Pasted image 20240628111858.png]]
![[Pasted image 20240628111958.png]]

Esse comando mostra todas as interfaces , mas será destacado a parte do **etherchannel**

### Load balance

```SW
# show etherchannel load-balance
```
![[Pasted image 20240628113250.png]]
Aqui o load balance está associado ao **source mac address**.

O mais ideal é que seja baseado no endereço IP de destino, assim quando ele alternará qual link é usado com base no endereço IP ao qual a informação se destina.

```SW
(config)# port-channel load-balance ?
```
![[Pasted image 20240628113841.png]]

```SW
(config)# port-channel load-balance src-dst-ip
```
Aqui mudamos o modo do nosso load balance

```SW
# show etherchannel load-balance
```
Mostrará qual o modo do nosso **load balance**

---
## Adicionando mais switches

![[Pasted image 20240628114931.png]]

As interfaces do `SW1` que se conectam ao `SW3`, para obedecermos ao "padrão", tem de estar em modo `trunk`, mesmo não sendo obrigatório.

```SW1
(config)# interface range fastEthernet 0/1-2

(config-if-range)# switchport mode trunk

(config-if-range)# shut down

(config-if-range)# channel-group 2 mode on
```

Agora fazemos as mesmas configurações para o `SW3`

```SW3
(config)# interface range fastEthernet 0/1-2

(config-if-range)# switchport mode trunk

(config-if-range)# shut down

(config-if-range)# channel-group 2 mode on
```

https://www.youtube.com/watch?v=qfuGIrlmKnM












