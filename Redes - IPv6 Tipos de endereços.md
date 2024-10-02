There are more than 5 types of IPv6, but here we will talk about 5 types.

## Link Local
Pode ser usado colo gateway pelos protocolos de roteamento para fazer o roteamento de rotas. Só tem roteamento localmente. Pode ser usado em mais de uma interface, pois será usado no link local de cada interface.

Sempre começa com `FE80::/10`.

## Global Unicast
É o endereço  que será divulgado para a internet, como se fosse o endereço público do IPv4.

Sempre começa com `2000::/3`

## Unique local 
É o endereço de uso exclusivo de uma rede privada. Como `192.168.0.0.` ou `10.0.0.0`.

Sempre começa com `FC00::/7`

## Multicast
O conceito de multicast permaneceu na transição do IPv6. Com o conceito de grupos de dispositivos. Para podermos dividir o tráfego da rede. Um roteador escuta vários grupos.

Sempre começa com `FF00::/8`.

>[!DANGER] IPv6 não tem broadcast.

## Anycast
A ideia de anycast é disponibilizar o mesmo IP em qualquer lugar, e ele agir de acordo com as suas configurações.






































