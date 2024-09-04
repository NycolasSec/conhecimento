Os operadores lógicos tomam seus argumentos como um todo, **independentemente de quantos bits eles contenham**.

Os operadores estão cientes apenas do valor: 0 significa falso; não 0 significa verdadeiro. O resultado de suas operações é um dos valores: 0 ou 1. Isso significa que o seguinte snippet:
```c
int i, j;
j = !!i;
```

atribuirá um valor de 1 à variável j se i não for zero; caso contrário, será 0.

No entanto, existem quatro operadores que permitem manipular bits únicos de dados. Nós os chamamos de **operadores bit a bit.**

Eles cobrem todas as operações que mencionamos anteriormente no contexto lógico e um operador adicional. Este é o _xor (exclusivo ou)_ e é denotado como ^ (acento circunflexo). Aqui estão todos eles:

|   |   |
|---|---|
|& (E comercial)|conjunção bit a bit|
|\|   (bar)|disjunção bit a bit|
|~ (til)|Negação bit a bit|
|^ (acento circunflexo)|exclusivo bit a bit ou|

Vamos facilitar:
- & requer exatamente dois "1s" para fornecer "1" como resultado
- | requer pelo menos um "1" para fornecer "1" como resultado
- ^ requer apenas um "1" para fornecer "1" como resultado
- ~ (é um argumento e) requer "0" para fornecer "1" como resultado

Mas tome nota: os argumentos desses operadores **devem ser inteiros** (int, bem como long, short ou char); Não devemos usar floats aqui.

A diferença na operação dos operadores lógicos e de bits é importante: os operadores lógicos não penetram no nível de bits de seu argumento. Eles estão interessados apenas no valor inteiro final.

Os operadores bit a bit são mais rígidos: **eles lidam com cada bit separadamente**. Se assumirmos que a variável int ocupa 32 bits, você pode imaginar a operação bit a bit como uma avaliação de 32 vezes do operador lógico para cada par de bits dos argumentos. Obviamente, essa analogia é um tanto imperfeita, pois no mundo real todas essas 32 operações são realizadas ao mesmo tempo.

| **left** | **right** | **left&right** | **left \| right** | **left ^ right** |
| -------- | --------- | -------------- | ----------------- | ---------------- |
| 0        | 0         | 0              | 0                 | 0                |
| 0        | 1         | 0              | 1                 | 1                |
| 1        | 0         | 0              | 1                 | 1                |
| 1        | 1         | 1              | 1                 | 0                |

| **Arg** | **~Arg** |
| ------- | -------- |
| 0       | 1        |
| 1       | 0        |

Vamos dar uma olhada em um exemplo da diferença de operação entre operações lógicas e de bits. Vamos supor que a seguinte declaração tenha sido executada:
```c
int i = 15, j = 22;
```

Se assumirmos que os ``int``s são armazenados com 32 bits, a imagem bit a bit das duas variáveis será a seguinte:
```c
i: 00000000000000000000000000001111
j: 00000000000000000000000000010110
```

A declaração é dada:
```c
int log = i && j;
```

Estamos lidando com uma conjunção lógica. Vamos traçar o curso dos cálculos. Ambas as variáveis i e j não são zeros, portanto, serão consideradas ``true``.

Se olharmos para a tabela verdade para o operador &&, podemos ver que o resultado será _verdadeiro_ e que é um número inteiro igual a 1. Isso significa que a imagem bit a bit da variável log é a seguinte:
```c
log: 00000000000000000000000000000001
```

Agora, a operação bit a bit – aqui está:
```c
int bit = i & j;
```

O operador ``&`` operará com cada par de bits correspondentes separadamente, produzindo os valores dos bits relevantes do resultado. Portanto, o resultado será o seguinte:
```c
bit: 00000000000000000000000000000110
```
Esses bits correspondem a um valor inteiro de 6.

Vamos tentar os operadores de negação agora. Primeiro, o lógico:
```c
int logneg = !i;
```
A variável ``logneg`` será definida como ``0``, portanto, sua imagem consistirá apenas em zeros.

```c
int bitneg = ~i;
```

```c
logneg: 00000000000000000000000000000000

bitneg: 11111111111111111111111111110000
```

Cada um dos operadores de dois argumentos anteriores pode ser usado em suas formas abreviadas. As seguintes notações são equivalentes, respectivamente:

| x = x & y;  | x& = y;  |
| ----------- | -------- |
| x = x \| y; | x\| = y; |
| x = x ^ y;  | x^ = y;  |











