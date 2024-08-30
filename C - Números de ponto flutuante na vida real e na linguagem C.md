Vamos voltar aos valores que citamos há pouco. “ _Dois e meio_ ” parece normal quando você o escreve em um programa, embora se sua linguagem nativa preferir usar uma vírgula em vez de um ponto no número, você deve garantir que seu número não contenha nenhuma vírgula. O compilador não o aceitará, ou (em casos muito raros, mas possíveis) entenderá mal suas intenções, pois a vírgula em si tem seu próprio significado reservado na linguagem “C”.

Se você quiser usar um valor de apenas “ _dois e meio_ ”, você deve escrevê-lo como mostrado na figura. Note mais uma vez – há um **ponto** entre “2” e “5” - não uma vírgula.

Como você provavelmente pode imaginar, o valor de “ _zero vírgula quatro_ ” poderia ser escrito em “C” como:
```txt
0.4
```

Não se esqueça desta regra simples: você pode **omitir** zero quando for o único dígito na frente ou depois do ponto decimal
```txt
.4
```

Por exemplo: o valor _4.0_ poderia ser escrito como _4._ sem alterar seu tipo ou valor.

Nota: o ponto decimal é essencial para reconhecer números de ponto flutuante em “C”. Observe estes dois números:

Para você, eles podem ser exatamente os mesmos, mas o compilador “C” vê esses dois números de uma maneira completamente diferente:

- 4é um _int_ (representa números inteiros, ou seja, números inteiros).
- 4.0é um _double_ (representa números de ponto flutuante de precisão dupla - eles têm quinze dígitos decimais de precisão e podem ser facilmente atribuídos a _float_ s, ou seja, números de ponto flutuante de precisão simples que têm seis dígitos decimais de precisão.

Quando você quiser usar qualquer número que seja muito grande ou muito pequeno, você pode usar **a notação científica** . Tome, por exemplo, a velocidade da luz, expressa em metros por segundo. Escrito diretamente, ficaria assim: `300000000
`
Para evitar escrever tantos zeros de forma tediosa, os livros didáticos de física usam uma forma abreviada, que você provavelmente já viu:
**3 • 10^8**
Diz: “ _três vezes dez elevado a oito_ ”

![[Pasted image 20240830190006.png]]

Na linguagem “C”, o mesmo efeito é obtido de uma forma ligeiramente diferente – veja:

```txt
3E8
```

A letra _E_ (você também pode usar a letra minúscula _e_ – ela vem da palavra expoente) é uma versão concisa da frase “ _vezes dez elevado a_ ”.

Observação:
- o expoente (o valor após o “ _E_ ”) **deve** ser um inteiro.
- a base (o valor antes do “ _E_ ”) **pode** ser um inteiro.

Vamos ver como usamos essa convenção para registrar números que são muito pequenos (no sentido de seu valor absoluto, que é próximo de zero).

Uma constante física chamada _constante de Planck_ (e denotada como _h_ ) tem, de acordo com os livros didáticos, o valor de:
**6,62607 × 10^-34**

Se você quiser usá-lo em um programa, você o escreveria desta forma:
```
6.62607E-34
```


O que acontece quando somos forçados a converter valores inteiros em valores float ou vice-versa? A transformação do tipo int em float é sempre possível e viável, mas em alguns casos pode causar perda de precisão. Considere o exemplo abaixo:

```c
int i;
float f;

i = 100;
f = i;
```
Após a segunda atribuição, o valor da variável _f_ é 100.0, porque o valor do tipo _int_ (100) é automaticamente **convertido** em um _float_ (100.0). A transformação afeta a representação **interna** (máquina) desses valores, pois os computadores usam métodos diferentes para armazenar _floats_ e _ints_ em sua memória.

Vamos considerar a situação oposta agora.
Como você provavelmente adivinhou, essas substituições resultam em perda de precisão – o valor da variável _i_ será 100. Vinte e cinco centésimos não têm significado no mundo _int_ . Há outro aspecto da operação: converter um _float_ em um _int_ nem sempre é viável. Variáveis ​​inteiras (como _floats_ ) têm **capacidade limitada** .
Eles não podem conter números arbitrariamente grandes (ou arbitrariamente pequenos). Por exemplo, se um certo tipo de computador usa quatro bytes (ou seja, 32 bits) para armazenar valores int, você só pode usar os números do intervalo de **-2147483648..2147483647** .

```c
int i;
float f;

f = 100.25;
i = f;
```

A variável _i_ não consegue armazenar um valor tão grande, mas não está claro o que acontecerá durante a atribuição. Certamente, ocorrerá uma **perda de precisão , mas o valor atribuído à variável** _i_ não é conhecido antecipadamente. Em alguns sistemas, pode ser o valor _int_ máximo permitido , enquanto em outros ocorre um erro, e em outros ainda o valor atribuído pode ser completamente aleatório.

Isso é o que chamamos de **problema dependente de implementação** . É a segunda (e mais feia) face da **portabilidade de software** .

```c
int i;
float f;

f = 1E10;
i = f;
```




















































































