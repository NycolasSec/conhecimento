Em um cenário onde temos 3 redes e precisamos divulga-las a internet, podemos tanto adicionar as 3 a tabela de roteamento ou podemos ``sumarizar`` e divulgar as 3 através de uma única rede que representa as 3.

### Exemplo 1

#### Topologia
![[Pasted image 20240903102950.png]]

#### Octetos iguais
Primeiro devemos identificar até qual octetos eles são iguais. Nesse caso são iguais até o 2° octeto e se diferem a partir do 3° octeto.
10.0.**3**.0/24
10.0.**2**.0/24
10.0.**1**.0/24

#### Bits iguais
Agora abrimos o octeto que difere em  binário para identificarmos em quais bits eles são iguais.

| 10.0.**3**.0/24 | 10.0.**2**.0/24 | 10.0.**1**.0/24 |
| --------------- | --------------- | --------------- |
| 000000**11**    | 000000**10**    | 000000**01**    |
Nesse caso são iguais até o 6° bit, tendo então 6 bits iguais nesses octetos.

#### Novo endereço de rede
Como queremos resumir esses endereços em um que englobe a todos. Definimos todos os bits que são iguais como **rede**.

Já tínhamos com **16 bits** iguais (``10.0``), agora temos mais 6 do octeto que se diferenciava. Obtendo assim **22 bits** para a rede.

Com isso teríamos o novo endereço :
**10.0.0.0/22**

### Exemplo 2

#### Topologia
![[Pasted image 20240903113524.png]]

#### Octetos iguais
Verificamos os octetos que são iguais.
192.168.**10**.0/24
192.168.**20**.0/24
192.168.**30**.0/24

#### Bits iguais
Agora abrimos o octeto em bits.

| 192.168.**10**.0/24 | 192.168.**20**.0/24 | 192.168.**30**.0/24 |
| ------------------- | ------------------- | ------------------- |
| 000**01010**        | 000**10100**        | 000**11110**        |
Nesse caso eles são iguais até o 3° bit.

#### Novo endereço
Somamos todos os bits que são iguais no endereços. Os bits dos primeiros octetos mais os 3 bits do 3° octeto. 16 + 3 = 19.

Com isso teríamos o novo endereço :
**192.168.0.0/19**

### Exemplo 3

#### Topologia
![[Pasted image 20240905082248.png]]

#### Octetos iguais
Verificamos os octetos que são iguais.
10.0.**110**.0/24
10.0.**115**.0/24
10.0.**200**.0/24

#### Bits iguais
Abrimos os octetos a nível de bit, e verificamos quais são iguais.

| 10.0.**110**.0 | 10.0.**115**.0 | 10.0.**200**.0 |
| -------------- | -------------- | -------------- |
| 01101110       | 01110011       | 11001000       |
Nesse caso eles não tem bits iguais nesse octeto. Mas como sabemos que o octeto anterior é igual em todos, usaremos ele.

#### Novo endereço


 













































