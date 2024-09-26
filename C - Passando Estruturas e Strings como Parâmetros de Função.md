## Parâmetros de funções que são estruturas

As estruturas são sempre passadas por ``valor`` – assim **como escalares** . Isso significa que o programa no editor:

```c
#include <stdio.h>

struct STR {
    int     Int;
    char    Char;
};

void fun(struct STR s) {
    s.Int = 2;
    s.Char = 'B';
}

int main(void) {
    struct STR str = { 1, 'A' };

    fun(str);
    printf("%d %c", str.Int, str.Char);
    return 0;
}
```

irá gerar o texto:
```text
1 A
```

Ou seja os campos da estrutura `str` permanece **inalterada** após a chamada de `fun`.

No entanto, temos permissão para passar um ponteiro para a estrutura como um parâmetro real.

Veja o exemplo no editor, que é apenas ligeiramente diferente do anterior.

```c
#include <stdio.h>

struct STR {
    int     Int;
    char    Char;
};

void funx(struct STR *p) {
    p -> Int = 2;
    p -> Char = 'B';
}

int main(void) {
    struct STR str = { 1, 'A' };

    funx(&str);
    printf("%d %c", str.Int, str.Char);
    return 0;
}
```

Esta função espera um **ponteiro para a estrutura** , não a estrutura em si. O ponteiro é do tipo ``struct STR *``. Consequentemente, a função não usa mais o operador ponto. Uma **seta deve ser usada** em vez disso e a tela de saída ficará assim:

```text
2 B
```

## Parâmetros de função que são matrizes unidimensionais

Vamos começar com o seguinte princípio: **os arrays são sempre passados ​​como um ponteiro para o primeiro elemento** .

Isso nos lembra de algo, certo? Já vimos que o nome de um array (sem nenhum operador de indexação acoplado) é interpretado como um ponteiro para seu primeiro elemento também. Esse princípio é uma consequência simples da regra geral.

Isso significa que qualquer alteração feita no valor do elemento de um array “formal” **é imediatamente refletida** em um array “real”. Essa dualidade, que consiste em tratamentos diferentes de parâmetros que são ou não arrays, é frequentemente um problema para programadores novatos.

Agora escreveremos uma função cuja tarefa é multiplicar cada elemento de um array por dois. O array é declarado como segue:

```c
int arr[5] = { 1, 2, 3, 4, 5 };
```

Assumimos que o parâmetro formal é simplesmente um ponteiro. Vamos aplicar o operador de indexação como se o ponteiro fosse o array “real”. Aqui está o programa completo – preste atenção em como lidamos com o parâmetro `arrptr` (código no editor).

```c
#include <stdio.h>

void mul2(int *arrptr) {
	int i;

	for(i = 0; i < 5; i++)
		arrptr[i] *= 2;
}

int main(void) {
	int arr[5] = { 1, 2, 3, 4, 5 };
	int i;

	for(i = 0; i < 5; i++)
		printf("%d ", arr[i]);
	printf("\n");
	mul2(arr);
	for(i = 0; i < 5; i++)
		printf("%d ", arr[i]);
	printf("\n");
	return 0;
}
```

Este programa produzirá duas linhas de texto, vindas do conteúdo do array antes e depois de invocar a função `mu12`:

```text
1 2 3 4 5
2 4 6 8 10
```

Observe que o parâmetro **formal** é declarado como um ponteiro:

```c
void mul2(int *arrptr)
```

enquanto o parâmetro **real** é o nome do array:

```c
mul2 (array);
```

**Nota**: a parte do código usada para imprimir o conteúdo do array é duplicada. Esta é uma ótima oportunidade para criar uma nova função e invocá-la, em vez de executar loops.

A nova função receberá o ponteiro como parâmetro real, mas o parâmetro formal será declarado de forma diferente de antes.

Dê uma olhada no editor - o programa completo é fornecido lá.

```c
#include <stdio.h>
void print(int a[]) {
    int i;
    for(i = 0; i < 5; i++)
        printf("%d ", a[i]);
    printf("\n");
}
void mul2(int *arrptr) {
    int i;
    for(i = 0; i < 5; i++)
       arrptr[i] *= 2;
}
int main(void) {
    int arr[5] = { 1, 2, 3, 4, 5 };

    print(arr);
    mul2(arr);
    print(arr);
    return 0;
}
```

Preste atenção a duas circunstâncias importantes:

- observe quão claro função `main` se torna quando nos **livramos do código repetitivo** ; o nome da nova função deixa claro seu propósito; isso é o que chamamos de _autocomentário_ ; a palavra “imprimir” afeta a imaginação do leitor mais fortemente do que qualquer comentário longo;
- usamos uma técnica ligeiramente diferente para declarar o parâmetro formal - ela sugere que vamos lidar com um array, não com um ponteiro comum:

```
int a[]
```

embora a declaração não determine o tamanho do array – por quê?

Especificar o tamanho do array é **desnecessário** , porque o array já existe – ele já foi declarado (incluindo o tamanho) em outro lugar, fora da função, e precisamos apenas de um ponteiro para ele.

Nota: do ponto de vista do compilador, ambos os métodos para declarar parâmetros formais são **equivalentes** a matrizes unidimensionais.

A função que envia o ponteiro para o array como um parâmetro não consegue adivinhar quantos elementos o array contém. Uma tentativa de usar o operador `sizeof` não dá em nada – só obteremos o tamanho do ponteiro em si. O tamanho real é escondido da função.

Existem duas possibilidades:

- o programador só conhece o tamanho do array (como nos programas citados no editor), onde sabemos que o array contém cinco elementos)
- informações sobre o tamanho do array são passadas usando outro método (por exemplo, com o uso de um parâmetro diferente)

Há também uma terceira possibilidade – a informação é codificada dentro da variável, assim como em uma string. Vamos discutir essa variante agora.

## Parâmetros de função que são strings
Uma string é apenas um tipo especial de array. Sabemos que:

- é uma matriz de tipo ``char``
- o conteúdo da matriz deve terminar com um caractere vazio (`'\ 0'`)

Tentaremos escrever nossa própria implementação da função ``strlen``. Sabemos que seu protótipo se parece mais ou menos com o seguinte:

```
int strlen(char *str);
```

A função descobre **quantos caracteres** estão contidos na string apontada pelo parâmetro `str`. Para evitar confusão e distinguir nossa função da original, nomearemos nossa função `mystrlen`. O uso do prefixo `my` é uma prática comum e popular usada por desenvolvedores sempre que eles escrevem suas próprias versões de funções conhecidas e amplamente utilizadas.

Também faremos as seguintes suposições:

- Etapa 1: precisamos de um **contador** com valor inicial zero
- Passo 2: **verificamos** o caractere apontado por ``str``– se for `'\ 0'`, deixaremos a função retornando o atual valor `count` como resultado
- Etapa 3: caso contrário, **incrementaremos** o `count` e mova o ponteiro `str` para o próximo caractere dentro da string
- Etapa 4: retornaremos **à** etapa 2

Observe que as etapas 2 a 4 formam um loop. A condição é verificada no início do corpo do loop, então usaremos o loop `while` para nosso algoritmo.

No editor, você pode ver a primeira abordagem (talvez um pouco estranha) para resolver o problema.

```c
int mystrlen(char *str) {
    int counter = 0;

    while(*str != '\0') {
        counter++;
        str++;
    }
    return counter;
}
```





























































































