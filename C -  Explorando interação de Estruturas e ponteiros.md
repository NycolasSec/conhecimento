## Ponteiros para estruturas

Vamos declarar um ponteiro para as estruturas do tipo struct STUDENT. É assim que funciona:

```c
struct STUDENT *sptr;
```

Se quisermos alocar memória para uma estrutura, podemos fazer isso da seguinte maneira:

```c
sptr = (struct STUDENT *) malloc(sizeof(struct STUDENT));
```































