Quando temos uma rede e a dividimos em redes menores, por razões de segurança, gerenciamento, necessidade e etc..

### Exemplo 1
- Temos a máscara de subrede **255.255.255.240**.

Sempre olhamos para o octeto misto(com zeros e uns), nesse caso é o último octeto.

Agora convertemos o último octeto em binário.

| 128   | 64    | 32    | 16    | 8     | 4     | 2     | 1     |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| 2^7   | 2^6   | 2^5   | 2^4   | 2^3   | 2^2   | 2^1   | 2^0   |
| **1** | **1** | **1** | **1** | **0** | **0** | **0** | **0** |
Os bits que ficaram como 0 representam os bits de host. Nesse caso são 4 bits para o host.

##### Máscara CIDR
A máscara CIDR indica o número de bits dedicado a rede.
No total um endereço IPv4 tem 32 bits, como aqui dedicamos 4 bits para host faremos uma subtração.
**32-4 = 28** - Então a máscara CIDR é a /28

##### Salto
Embora seja muito explícito, é bom sabermos alguns métodos de sabermos o salto logo de cara.

O número do salto é o **256** subtraído do número do octeto misto. Nesse caso. **256 - 240 = 16**

Também podemos pegar o número de endereços possíveis. que seria 2 elevado ao número de bits dedicado aos hosts. Nesse caso **2^4 = 16**

##### Número de endereços
Agora fazemos 2 elevado ao número de bits de host.
**2^4 = 16** - Este é o número de endereços possíveis em cada subrede.

##### Número de endereços para hosts
Como temos 1 endereço para broadcast e 1 endereço para rede, remos que subtrair 2.
**2^4 - 2 = 14** - Este é o número de hosts em cada subrede

##### Rede e Broadcast
Sendo o endereço de rede o primeiro endereço de uma rede e o endereço de bradcast o último endereço.

Como temos 16 endereços, somaremos de 16 em 16 para definir o início e o término de cada rede.

Também podemos usar o salto. Que em tese dá no mesmo

| **Num. sub rede** | 1   | 2   | 3   | 4   | 5   | ... |
| ----------------- | --- | --- | --- | --- | --- | --- |
| **End. rede**     | 0   | 16  | 32  | 48  | 64  | ... |
| **End. broad.**   | 15  | 31  | 47  | 63  | 79  | ... |




---

### Exemplo 2
##### Escopo
Temos uma empresa com quatro departamentos, no qual em cada departamento são necessários 14 IPs para os funcionários. Nos foi fornecido o endereço IP **192.168.0.0/24** para usarmos.
Como podemos dividir essa rede em sub-redes para que haja o menor número possível de desperdício de endereços.

##### Número de endereços
Precisamos de 14 endereços para os hosts. Como também precisamos do endereço de rede e de broadcast. Resultando em 16 endereços.

Como esse número sempre deve ser uma potência de 2, emos de achar a quantidade de bits que mais chega perto do número necessário.

| 128 | 64  | 32  | 16  | 8   | 4   | 2   |
| --- | --- | --- | --- | --- | --- | --- |
| 2^7 | 2^6 | 2^5 | 2^4 | 2^3 | 2^2 | 2^1 |
Olhando a tabela, o que mais chega perto é a de 4 que resulta em 16.

##### Máscara CIDR
No total um endereço IPv4 tem 32 bits, como aqui dedicamos 4 bits para host faremos uma subtração.
**32-4 = 28** - Então a máscara CIDR é a /28.

##### Rede e Broadcast
Sendo o endereço de rede o primeiro endereço de uma rede e o endereço de bradcast o último endereço.

Como temos 16 endereços, somaremos de 16 em 16 para definir o início e o término de cada rede.

Também podemos usar o salto. Que em tese dá no mesmo

| **Num. sub rede** | 1   | 2   | 3   | 4   | 5   | ... |
| ----------------- | --- | --- | --- | --- | --- | --- |
| **End. rede**     | 0   | 16  | 32  | 48  | 64  | ... |
| **End. broad.**   | 15  | 31  | 47  | 63  | 79  | ... |



