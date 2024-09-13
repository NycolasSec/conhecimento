Semelhante ao **IPv4** o **IPv6** é dividido em bits para a rede e bits para o host. Mas agora em vez de chamarmos de ``Host portion`` vamos chamar de ``Interface Identifier``.
O ``Interface Identifier`` pode ser dinamicamente atribuído pelo ``MAC Address`` da interface (``EUI-64``).

#### Exemplo

**Endereço completo :**
2001:aabb:cc11::3a **/64**

**Rede :**
``2001:aabb:cc11:0000``

**Interface Identifier :**
``0000:0000:0000:003a``

#### Padrão de atribuição
No **IPv4** temos os padrões de como vamos atribuir os IPs como classe ``A``,``B`` e ``C``. No **IPv6** também temos um padrão que utilizamos. O padrão utilizado seria 64 bits para a rede e 64 bits para o host (``/64``).

**Endereço original :**
2001:aabb:cc11:0000 **:** 0000:0000:0000:003a /64

| Rede                    | Interface Identifier    | Máscara |
| ----------------------- | ----------------------- | ------- |
| ``2001:aabb:cc11:0000`` | ``0000:0000:0000:003a`` | /64     |
 
### EUI-64
o EUI-64 utiliza o MAC address para definir o Interface Identifier, como o endereço MAC tem 48 bits e o Interface Identifier definido pelo EUI-64 tem 64 bits, ele faz um cálculo para preencher os bits faltantes.

#### Regra 1 - Injeção
Ele pega o endereço MAC divide no meio e injeta os os hexadecimais **`FFFE`**.

#### Regra 2 - Flip the 7° bit
Pega o 7° bit do endereço MAC e inverte o seu valor, se for ``0`` vira ``1`` e se for ``1`` vira ``0``.

#### Exemplo
Digamos que temos o endereço MAC **`AAAA:BBBB:CCCC`**.

**Primeiro dividimos no meio e inserimos o hexadecimal `FFFE`:**
AAAA:BBBB:CCCC -> AAAA:BB BB:CCCC -> AAAA:BB**FF**:**FE**BB:CCCC 

**Agora invertemos o valor do 7°bit :** 
Como cada número é composto por 4 bits, sabemos que essa mudança ocorrerá no segundo número do endereço.

| A    | A        | A    | A    |
| ---- | -------- | ---- | ---- |
| 1010 | 10**1**0 | 1010 | 1010 |
| 1010 | 10**0**0 | 1010 | 1010 |
| A    | 8        | A    | A    |

### Encurtamento de endereço
Podemos utilizar de algumas regras para encurtar o endereço IPv6.

#### Regra 1 : Substituição de sequência de zeros
Quando temos campos zerados em sequência ou sozinhos podemos substitui-los por ``::``, só podemos utilizar dessa regra uma vez.

#### Regra 2 : Ocultação de zeros a esquerda
Quando temos campos que iniciam com 0(s) podemos oculta-los dos campos.

#### Exemplo
Digamos que temos o endereço IPv6 **`2001:0ABC:0000:0000:0000:ABCD:00AB:0001`**

**Primeiro substituímos os campos zerados em sequência por ``::``**:
2001:0ABC:**0000:0000:0000**:ABCD:00AB:0001 -> 2001:0ABC **::** ABCD:00AB:0001

**Depois podemos ocultar ocultar os zeros que estão no início de cada campo :**
2001:**0**ABC::ABCD:**00**AB:**00**1 -> 2001:ABC::ABCD:AB:1

>[!IMPORTANT] 
>Caso tenhamos um `::` em um endereço, ele irá expandir para a quantidade de campos zerados necessários para preencher o endereço. E caso haja números faltantes em algum campo do endereço, ele será completado com zeros a esquerda.












































