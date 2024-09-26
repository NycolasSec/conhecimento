## Parâmetros e função que são escalares
Já sabemos que os valores reais dos parâmetros são **atribuídos aos parâmetros formais** durante a invocação da função, o que significa que a execução do programa no editor:
```shell-session
#include <stdio.h>

void function(int param) {
    printf("I've received value %d\n", param);
}

int main(void) {
    int variable = 111;

    function(1000);
    function(variable);
    function(variable + 1000);
    return 0;
}
```

enviará o seguinte texto para a tela:

```text
I've received value 1000
I've received value 111
I've received value 1111
```

O que acontece se a função alterar o valor do parâmetro formal? É sempre possível. Em outras palavras, o que o programa no editor enviará para a tela?

```c
#include <stdio.h>

void function(int param) {
    printf("I've received value %d\n", param);
    param++;
}

int main(void) {
    int variable = 111;

    function(variable);
    printf("variable %d\n", variable);
    return 0;
}
```

A função ``function`` não apenas imprime seu único parâmetro mas o modifica. Isso significa que a função modifica não apenas o parâmetro mas também o valor da variável ? De forma alguma – o programa emitirá o seguinte texto:

```text
I've received value 111
variable=111
```

**A função não é capaz de alterar o valor do parâmetro real.**

O valor do parâmetro real é copiado para o parâmetro formal e o relacionamento entre eles definitivamente termina neste ponto. Qualquer alteração do parâmetro formal não afeta o estado do parâmetro real. O parâmetro real e o parâmetro formal são duas entidades totalmente separadas e o único contato entre eles ocorre quando a função está sendo invocada. A partir desse momento, os dois parâmetros vivem suas próprias vidas separadas e não interagem de forma alguma.

Esse tipo de cooperação entre parâmetros é conhecido como **passagem de parâmetros por valor** .

No entanto, podemos lidar com essa limitação usando **ponteiros** . É assim que função `scanf` funciona quando tem que obter o valor do usuário e atribuí-lo a uma variável. Dê uma olhada no programa no editor.

```c
#include <stdio.h>

void functionx(int *ptr) {
    *ptr = *ptr + 100;
}

int main(void) {
    int i = 100;
    int *p = &i;

    printf("i = %d\n", i);
    functionx(p);
    printf("i = %d\n", i);
    return 0;
}
```


Vamos navegar pelo nosso programa.
- nós declaramos a variável `i` e inicializá-la com um valor de 100
- nós declaramos a variável `p` e inicializá-la com um ponteiro para a variável `i`.
- a figura abaixo ilustra a configuração atual:

![[Pasted image 20240926082330.png]]

- Nós invocamos a  função ``x`` e passamos o valor de ``p``.
- o valor é copiado para o parâmetro `ptr`.
- a figura ilustra a configuração resultante:

![[Pasted image 20240926082614.png]]

- A função `x` sai
-  a variável `p` não mudou seu valor (a regra que definimos vários slides atrás é respeitada sem exceções)
- a variável `i` mudou seu valor e agora é 200
- a tela de saída se parece com a seguinte:

```text
i = 100
i = 200
```

Apesar de não podermos alterar o **valor real do parâmetro** , alteramos com sucesso o valor da variável usando o ponteiro (que permanece inalterado).

Podemos simplificar a função ``main`` da seguinte maneira:

```c
int main(void) {
    int i = 100;

    printf("i = %d\n", i);
    functionx(&i);
    printf("i = %d\n", i);
    return 0;
}
```

O ponteiro `p` é completamente desnecessário e podemos removê-lo. Um ponteiro para `i` pode ser recuperado usando o operador `&`, aplicado diretamente no momento em que invocamos a função. O efeito será exatamente o mesmo.

Vamos escrever uma função que _incrementa o valor apontado pelo seu parâmetro_ .

Já sabemos que o parâmetro formal deve ser um ponteiro, então a função completa ficará assim:

```c
void incr(int *value) {
     (*value)++;
}
```

Você pode explicar por que temos que usar os parênteses em volta do ``*valor`` ? O que aconteceria se os removêssemos?

Agora estamos prontos para usar nossa nova função:

```c
int main(void) {
    int var = 100;

    incr(&var);
    printf("var = %d\n", var);
    return 0;
}
```

O programa completo está no editor.

```c
#include <stdio.h>

void incr(int *value) {
	(*value)++;
}

int main(void) {
	int var = 100;

	incr(&var);
	printf("var = %d\n", var);
	return 0;
}
```