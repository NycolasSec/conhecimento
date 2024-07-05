Em questão de design de rede(arquitetura de rede), temos dois tipos.

- Two layers (2 tiers)
- Three layers(3 tiers)
- Spine-leaf

Em geral precisamos de 3 camadas lógicas, 

- Camada de Acesso
- Camada de Distribuição
- Camada de Core

## Two layers

quando falamos de `two layers` teremos o que chamamos de camada `colapsed`, pois ela executara a funcionalidade de distribuição e a de core.

![[Pasted image 20240704091110.png]]

![[Pasted image 20240704092641.png]]

## Three layers

no modelo de three layers teremos três camadas, uma para cada funcionalidade.

![[Pasted image 20240704091905.png]]

![[Pasted image 20240704092725.png]]

Aqui os switches podem fazer o roteamento para as vlans, mas não terão conhecimento da tabela full routing, pois ela será gerenciada pelo router.

Assim a camada de distribuição resolve o roteamento interno da rede, enquanto a camada core resolve o roteamento externo.

O gateway está na camada core.

## SPINE-LEAF

Pode rodar em cima de uma topologia layer 3.

os spines são análogos a uma camada collapsed, fazendo o core e a distribuição.

os `leafs` receberão os clientes, e terão uma conectividade `ful matche` com todos os `spines`, assim mesmo se tiver apenas um `spine`, em funcionamento, a rede continua de pé.


![[Pasted image 20240704093301.png]]