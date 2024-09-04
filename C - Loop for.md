O loop for se resumi em :
```c
for(initialization; checking; modifying ){
    /* the body goes here */
}
```

Pode assumir a seguinte forma:
```c
for(i = 0; i < 100; i++ ){
    /* the body goes here */
}
```

A variável usada para contar as voltas do loop é freqüentemente chamada de **variável de controle**.

O loop for tem uma **singularidade** interessante. Se **omitirmos qualquer um** de seus três componentes, **presume-se que haja um ``1`` lá**:

```c
for(<b> ; ; </b>) {
    /* the body goes here */
}
```











