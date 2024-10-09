Para criar números aleatórios usamos o método `rand()`.
```c
    printf("Numero gerado : %d", rand());
```
![[Pasted image 20241008191055.png]]

#### Semente
Apesar de gerar números aleatórios, podemos, colocar uma "semente" no `rand()` para ele usar na geração de números aleatórios.

Usamos o `srand()` para isso.
```c
srand(time(NULL));
printf("Numero gerado : %d", rand());
```
![[Pasted image 20241008191917.png]]

#### Intervalo de tempo
Com um pouco de matemática, podemos criar um range de números aleatórios.

Números de 0 á 10:
```c
srand(time(NULL));
int r = rand() % 10;
printf("Numero gerado : %d", r);
```
![[Pasted image 20241008192614.png]]

#### Máximo e mínimo
Mínimo 6 e máximo 22:
```c
srand(time(NULL));

int max = 22;
int min = 6;

int r = (rand() % (max - min + 1)) + min;
printf("Numero gerado : %d", r);
```
![[Pasted image 20241008193246.png]]









