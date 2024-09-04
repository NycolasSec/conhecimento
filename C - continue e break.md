o desenvolvedor pode se deparar com as seguintes opções:
- Parece que é **desnecessário continuar** o loop como um todo; devemos nos abster de executar o corpo do loop e ir mais longe;
- Parece que precisamos **iniciar o teste de condição** sem concluir a execução do turno atual.

Essas duas instruções são:
- ``break`` - **sai do loop imediatamente** e encerra incondicionalmente a operação do loop; o programa começa a executar a instrução mais próxima após o corpo do loop;
- ``continue`` – se comporta como se o programa tivesse chegado repentinamente ao fim do corpo; **O final do corpo do loop é alcançado**, a variável de controle é modificada (no caso de loops for) e a expressão de condição é testada.
#### Exemplo break
```c
/* The break variant */

#include <stdio.h>

int main(void) {
    int number;
    int max = -100000;
    int counter = 0;

    for( ; ; ){
        scanf("%d",&number);
        if(number == -1)
            break;
        counter++;
        if(number > max)
            max = number;
    }
    if(counter)
          printf("The largest number is %d \n",max);
    else 
        printf("Are you kidding? You haven't entered any number!");
    return 0;
}
```

####
```c
#include <stdio.h>

int main(void) {
    int number;
    int max = -100000;
    int counter = 0;

    do {
        scanf("%d",&number);
        if(number == -1)
            continue;
        counter++;
        if(number > max)
            max = number;
    } while (number != -1);
    if(counter)
          printf("The largest number is %d\n",max);
    else 
        printf("Are you kidding? You haven't entered any number!");
    return 0;
}
```
