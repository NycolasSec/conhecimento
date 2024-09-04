Como podemos usar o valor do tipo ``char`` em C ? Podemos fazer de duas maneiras.

A primeira maneira nos permite especificar o próprio caractere, mas **entre aspas simples** :``''``(apóstrofos).

```c
Character = 'A';
```

Não é permitido omitir apóstrofos em nenhuma circunstância. 

Agora vamos atribuir um asterisco à nossa variável. Fazemos isso da seguinte maneira:
```c
Character = '*';
```

O segundo método consiste em atribuir um **valor inteiro não negativo** que é o código do caractere desejado. Isso significa que a atribuição abaixo colocará um `A` na variável de caractere:
```c
Character = 65;
```

A segunda solução, no entanto, é menos recomendada e, se você puder evitá-la, você deve. Por quê?

Primeiro, porque é **ilegível** . Sem saber o código ASCII, é impossível adivinhar o que é. A segunda razão é mais exótica, mas ainda verdadeira. Há um número significativo de computadores no mundo que usam códigos **diferentes do ASCII** .





































