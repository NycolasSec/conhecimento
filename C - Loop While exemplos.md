Tente lembrar como a linguagem "C" interpreta a verdade de uma condição e observe que essas duas formas são equivalentes:
```c
while (Number != 0) { ... }
while (Number) { ... }
```

Agora a condição que verifica se é ímpar ou par, codificamos assim:
```c
if (Number % 2 == 1) ...
if (Number % 2) ...
```

Nada te surpreenderia agora, certo? Mas há duas coisas que podem ser escritas de forma mais compacta. Primeiro, a condição do loop while:
```c
#include <stdio.h>

int main(void)
{
    int counter = 5;

    while(counter != 0) {
        puts("I am an awesome program");
        counter--;
    }
    return 0;
}
```

Outra mudança requer algum conhecimento de como funciona o pós-decremento. Podemos usá-lo para compactar nosso programa mais uma vez.
```c
#include <stdio.h>

int main(void)
{
    int counter = 5;

    while(counter) {
        puts("I am an awesome program");
        counter--;
    }
    return 0;
}
```

Acreditamos que esta é a forma mais simples do programa, mas você pode nos desafiar se tiver coragem.
```c
#include <stdio.h>

int main(void)
{
    int counter = 5;

    while(counter--) 
        puts("I am an awesome program");
    return 0;
}
```