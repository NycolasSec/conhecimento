Plano de endereçamento

![[Pasted image 20240711103522.png]]

### Resumo de endereçamento IPv4

**O intervalo 10.0.0.0/16 será usado:**

| Intervalo de endereços                           | Informação detalhada                                      |
| ------------------------------------------------ | --------------------------------------------------------- |
| 10.0.0.0/24 para Loopbacks de roteador           | /32 por Loopback: 10.0.0.R/32, R = número do roteador     |
| 10.1.0.0/24 para links ponto a ponto do roteador | /30 por link                                              |
|                                                  | Primeiro IP do host em /30 para roteador com número menor |
|                                                  | Segundo host IP em /30 para roteador com número maior     |
| 10.2.0.0/16 para LANs                            | /24 por rede: 10.2.R.0/24, R = número do roteador         |
|                                                  | .1 para roteador                                          |
|                                                  | .2 para Host                                              |

### Resumo de endereçamento IPv6

**O intervalo 2001:DB8::/32 será usado:**

| Intervalo de endereços                                      | Informação detalhada                                                                                             |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| 2001:DB8::/64 para Loopbacks de roteador, /128 por roteador | /128 por Loopback: 2001:DB8::R/128                                                                               |
|                                                             | R = número do roteador                                                                                           |
| 2001:DB8:1::/48 para links ponto a ponto do roteador        | Reserve /64 para cada link, sub-rede para /127 (recomendação de [RFC6164](https://tools.ietf.org/html/rfc6164) ) |
|                                                             | ::0 para roteador com número menor                                                                               |
|                                                             | ::1 para roteador com número maior                                                                               |
| 2001:DB8:2::/48 para LANs                                   | /64 por rede: 2001:DB8:2:R::/64, R = número do roteador                                                          |
|                                                             | ::1 para roteador                                                                                                |
|                                                             | ::2 para o anfitrião                                                                                             |

### Endereços IPv4/IPv6 de host final pré-configurados

#### H1
```
Interface ens32 (Connecting to R1 GigabitEthernet7)
 - IPv4 Address: 10.2.1.2/24
 - IPv4 Gateway: 10.2.1.1
 - IPv6 Address: 2001:DB8:2:1::2/64
 - IPv6 Gateway: 2001:DB8:2:1::1
```

#### H2
```
Interface ens32 (Connecting to R2 GigabitEthernet7)
 - IPv4 Address: 10.2.2.2/24
 - IPv4 Gateway: 10.2.2.1
 - IPv6 Address: 2001:DB8:2:2::2/64
 - IPv6 Gateway: 2001:DB8:2:2::1
```

#### H5
```
Interface ens32 (Connecting to R5 GigabitEthernet7)
 - IPv4 Address: 10.2.5.2/24
 - IPv4 Gateway: 10.2.5.1
 - IPv6 Address: 2001:DB8:2:5::2/64
 - IPv6 Gateway: 2001:DB8:2:5::1
```

#### H6

```
Interface ens32 (Connecting to R6 GigabitEthernet7)
 - IPv4 Address: 10.2.6.2/24
 - IPv4 Gateway: 10.2.6.1
 - IPv6 Address: 2001:DB8:2:6::2/64
 - IPv6 Gateway: 2001:DB8:2:6::1
```

---

## Configurando endereços IPv4/IPv6

Por padrão o ipv6 está desabilitado no Cisco IOS. Temos que habilitá-lo.

```ios
R1(config)# ipv6 unicast-routing
```
Ativa o endereçamento IPv6.

```ios
R1(config)# interface Loopback0
```
Cria uma Interface de Loopback

```ios
R1(config-if)# description *** Loopback for Router ID ***
```
Adiciona uma descrição a interface

```ios
R1(config-if)# ip address 10.0.0.1 255.255.255.255
```
Atribuição de endereço IPv4.

```ios
R1(config-if)# IPv6 address 2001:DB8::1/128
```
Atribuição de endereço IPv6

#### Configurar GigabitEthernet1 de R1
```ios
R1(config)# interface GigabitEthernet1
R1(config-if)# no shutdown
R1(config-if)# description *** To R2 GigabitEthernet1 ***

R1(config-if)# ip address 10.1.0.1 255.255.255.252
R1(config-if)# ipv6 address 2001:DB8:1::/127

! Desable IPv6 Router Advertisement
R1(config-if)# ipv6 nd ra suppress all
R1(config-if)# exit
```

#### Configurar GigabitEthernet2 de R1
```ios
R1(config)# interface GigabitEthernet2
R1(config-if)# no shutdown
R1(config-if)# description *** To R3 GigabitEthernet1 ***

R1(config-if)# ip address 10.1.0.5 255.255.255.252 
R1(config-if)# ipv6 address 2001:DB8:1:1::/127

R1(config-if)# ipv6 nd ra suppress all
R1(config-if)# exit
```

#### Configurar GigabitEthernet3 de R1
```ios
R1(config)# interface GigabitEthernet3
R1(config-if)# no shutdown
R1(config-if)# description *** To R4 GigabitEthernet1 ***

R1(config-if)# ip address 10.1.0.9 255.255.255.252 
R1(config-if)# ipv6 address 2001:DB8:1:2::/127

R1(config-if)# ipv6 nd ra suppress all
R1(config-if)# exit
```

#### Configurar GigabitEthernet7 de R1
```ios
R1(config)# interface GigabitEthernet7
R1(config-if)# no shutdown
R1(config-if)# description *** LAN for H1 ***

R1(config-if)# ip address 10.2.1.1 255.255.255.0
R1(config-if)# ipv6 address 2001:DB8:2:1::1/64

R1(config-if)# ipv6 nd ra suppress all
R1(config-if)# exit
```


























































