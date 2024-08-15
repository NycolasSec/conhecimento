## Sub rede baseada no número de redes

As vezes teremos de dividir uma rede para locais diferentes e não para quantidades diferentes, nesse caso devemos fazer nossa divisão de rede baseada no número de redes que precisamos.

### Exemplo 1
Recebemos o endereço de rede **10.0.0.0/16** para dividirmos em 3 localidades.

##### Quantos bits usaremos
Nesse caso precisamos de 3 sub nets. Usamos os números múltiplos de base 2, e usamos o que mais se achega ao que precisamos.

Nesse caso **2^2=4**. Então usaremos mais 2 bits para a rede. Para representar as sub nets.

Então agora em vez de ser **10.0.0.0/16** será **10.0.0.0/18**, pois adicionamos mais 2 bits. Sendo esses 2 bits que adicionamos, dedicados a sub redes.

##### Salto das sub nets
O salta determina onde começa e onde termina uma rede, ou sub rede. Podemos determinar pelo número de endereços de cada sub rede.

Para calcularmos o número do salto, usamos o número de endereços disponíveis em cada sub rede.

Nesse caso como é **/18** indica que no 3 octeto dois bits são de rede e 6 bits são de hosts. Então fazemos **2^6=64**. Então o nosso salto é de 64.

##### Redes
Então tendo 64 como o número do salto, temos as redes:

``1° rede`` - 10.0.0.0/18
``2° rede`` - 10.0.64.0/18
``3° rede`` - 10.0.128.0/18
``4° rede`` - 10.0.196.0/18

---
### Exemplo 2
Recebemos o endereço de rede **192.168.0.0/24** para dividirmos entre 5 localidades.

##### Quantos bits usaremos
Aqui precisamos descobrir qual número múltiplo de base 2 será usado. Aqui seria **2^3=8**. Então usaremos 3 bits para nossas sub nets.

Como adicionamos mais 3 bits a máscara CIDR mudará para **/27**

| 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 7   | 6   | 5   | 4   | 3   | 2   | 1   | 0   |
| 1   | 1   | 1   | 0   | 0   | 0   | 0   | 0   |

#### Salto das sub nets
Ainda temos 5 bits para os hosts, então faremos **2^5 = 32**. Então 32 é o tamanho do salto.

Também podemos fazer a soma dos bits da rede e subtrair de **256**.

**256** - (**128** + **64** + **32**) = **256** - **224** = **32**

##### Definindo as redes
Sendo o número do salto 32, teremos as redes :

``1° rede`` - 192.168.0.0
``2° rede`` - 192.168.0.32
``3° rede`` - 192.168.0.64
``4° rede`` - 192.168.0.96
``5° rede`` - 192.168.0.128
``6° rede`` - 192.168.0.160
``7° rede`` - 192.168.0.192
``8° rede`` - 192.168.0.224

