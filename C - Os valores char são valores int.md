Em `C`  o tipo `Character` é tratado como um tipo especial do tipo ``Inteiro``. Isso significa que:

- Você sempre pode **atribuir um valor Character** para uma variável inteiro;
- Você sempre pode atribuir um valor inteiro para uma variável ``Char``, mas se o valor exceder 255 (o código de caractere mais alto em ASCII), você deve esperar uma perda de valor;
- O valor do tipo `Character` pode estar sujeito aos **mesmos operadores** que os dados do tipo ``Inteiro``.

Podemos verificar isso usando um exemplo simples. Dissemos anteriormente que em ASCII, a “distância” entre letras maiúsculas e minúsculas é 32, e que 32 é o código do caractere de espaço. Veja este trecho:

```c
char Char;
Char = 'A';
Char += 32;
Char -= ' ';
```

Esta sequência de adição e subtração subsequentes trará o valor `Char` ao seu valor original (`A`). Você deveria ser capaz de explicar o porquê, certo?