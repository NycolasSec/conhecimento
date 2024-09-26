## Parâmetros de funções que são arrays multidimensionais

Vamos começar com uma limitação significativa: agora vamos falar sobre **matrizes multidimensionais** verdadeiras (mais precisamente, matrizes bidimensionais).

Como você já sabe, matrizes multidimensionais podem existir:

- de forma verdadeira e pura quando são declaradas com um conjunto completo de dimensões, como esta:

```c
int array[3][3];
```

que cria uma matriz de nove elementos dispostos em três linhas e três colunas;

- como uma matriz de ponteiros, ou seja, como o **vetor que contém ponteiros** para as linhas da matriz e é declarado assim:

```c
int *ptrarr[3];
```

Embora as referências aos elementos de ambas as matrizes sejam sintaticamente idênticas e tenham esta aparência:

```c
array[2][2] = ptrarr[2][2];
```

elas diferem significativamente em termos de semântica.

No editor temos uma simples função `main` , contendo uma declaração de uma pequena matriz bidirecional.

```c
int main(void) {
    int arr[3][3] = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

    /* we want to invoke a smart function here which is able
       to print the content of the tab in a nice way */

    return 0;
}
```

Queremos usar uma função conveniente e útil para gerar o conteúdo do array. Queremos que os números sejam apresentados claramente em _três linhas e três colunas_ .

Vamos imprimir o array mais de uma vez, então uma **função** parece ser a melhor solução.

Projetaremos o **esqueleto** desta função, negligenciando completamente a maneira como o parâmetro formal deve ser declarado e substituindo-o por três pontos de interrogação.

Assumimos apenas que o nome do parâmetro formal será `t`.

```c
void printarr( ??? ) {
    int i,j;
    for(i = 0; i<3; i++) {
        for(j = 0; j<3; j++) 
            printf("%d\t",t[i][j]);
        printf("\n");
    }
}
```

Gostaríamos de invocar esta função da seguinte maneira:

```c
printarr(arr);
```

Vamos analisar como a função ``printarr`` opera na matriz ``t``. Assumimos que queremos alcançar o elemento ``t[1][1]``

Então:

- temos que “pular” toda a primeira linha da matriz `t` para chegar ao início da segunda linha;
- uma vez na segunda linha devemos “pular” para o segundo elemento.

Vamos fazer duas perguntas difíceis. Considere as respostas e pense nelas cuidadosamente.

- **Pergunta** : Para passar com sucesso pelos passos descritos acima, precisamos saber **quantas linhas** o array contém?  
      
    **Resposta** : **Não** , não precisamos, porque quando queremos “pular” para o início do número da linha ``x``, e por isso não nos importamos com quantas linhas existem na matriz.

- **Pergunta** : Para passar com sucesso pelos passos descritos acima, precisamos saber **quantas colunas** o array contém (ou seja, quantos elementos existem em cada linha)?  
      
    **Resposta** : **Sim** , porque sem essa informação não seremos capazes de pular ``x-1`` linhas; precisamos saber qual é o comprimento de cada linha.

Isto significa que a função `printarr` não precisa saber o número de linhas, mas precisa saber o número de colunas. Caso contrário, o compilador não conseguirá calcular os endereços das linhas.

Como um protótipo do ``printarr`` não deve se parecer? A forma a seguir é estritamente proibida, embora se assemelhe a um array unidimensional:

```c
void printarr(int **t);
```

A declaração é **inválida** porque _t_ não é um ponteiro para uma matriz de ponteiros, mas um ponteiro para uma matriz real.

A seguinte declaração também é proibida:

```
void printarr(int t[][]);
```

Há uma sugestão de que `t` é uma matriz bidimensional, mas o compilador não sabe quantas linhas ela contém (como já sabemos, essa informação é essencial para o endereçamento adequado das linhas).

Podemos fazer isso dessa maneira?

```c
void printarr(int t[3][3]);
```

  

Isso é bom, embora agora tenhamos fornecido mais informações do que o compilador realmente precisa. A informação que diz que t tem três linhas não é útil para o compilador.

Isto significa que um formulário de declaração que é aceitável e suficiente é o seguinte:

  

```c
void printarr(int t[][3]);
```

  

Vamos reformular o seguinte princípio universal: se você declarar um parâmetro formal que seja uma matriz, você poderá desconsiderar as informações sobre o tamanho da **primeira** dimensão; mas os tamanhos restantes **precisam** ser fornecidos.

Agora vamos dar uma olhada em um conjunto completo de funções e declarações no editor.

```c
#include <stdio.h>

void printarr(int t[][3]) {
    int i,j;
    for(i = 0; i<3; i++) {
        for(j = 0; j<3; j++) 
            printf("%d\t",t[i][j]);
        printf("\n");
    }
}

int main(void) {
    int arr[3][3] = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    
    printarr(arr);
    return 0;
}
```

Agora volte para as soluções que rejeitamos anteriormente, onde o parâmetro passado para a função era apenas um **array de ponteiros** . Podemos chamá-lo (não tão elegantemente) de um array **falso** .

Dê uma olhada função `main` contendo a declaração e inicialização do array - o código está no editor:

```c
int main(void) {
    int *arrptr[3];
    int i,j;

    for(i = 0; i < 3; i++) {
        arrptr[i] = (int *) malloc(3 * sizeof(int));
        for(j = 0; j < 3; j++)
            arrptr[i][j] = (3 * i) + j + 1;
    }

    /* we want to invoke a smart function here which is able
		to print the content of the tab in a nice way */

    for(i = 0; i < 3; i++)
        free(arrptr[i]);
    return 0;
}
```

Neste caso, não há dificuldade na declaração. Ela pode assumir uma das duas formas seguintes:

- Primeira variante:
```c
void printarrptr(int *t[])
```

- Segunda variante:
```c
void printarrptr(int *t[])
```

- Aqui está o código completo:
```c
#include <stdio.h>
#include <stdlib.h>
void printarrptr(int **t) {
    int i,j;
    for(i = 0; i<3; i++) {
        for(j = 0; j<3; j++) 
            printf("%d\t",t[i][j]);
        printf("\n");
    }
}
int main(void) {
    int *arrptr[3];
    int i,j;

    for(i = 0; i < 3; i++) {
        arrptr[i] = (int *) malloc(3 * sizeof(int));
        for(j = 0; j < 3; j++)
            arrptr[i][j] = (3 * i) + j + 1;
    }
    printarrptr(arrptr);
    for(i = 0; i < 3; i++)
        free(arrptr[i]);
    return 0;
}
```


































