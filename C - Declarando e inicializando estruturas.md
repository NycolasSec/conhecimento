O nomes dos campos podem sobrepor o nome da tag, isso não é considerado um erro embora possa causar alguma dificuldade na leitura do programa.

```c
struct STRUCT {
     int STRUCT;
} Structure;

Structure.STRUCT = 0; /* STRUCT is a field name here */
```

Pode ser que algum compilador reclame de um nome da tag se sobrepor ao nome de alguma variável. Portanto devemos evitar códigos mostrados a seguir.

```c
struct STR {
 int field;
} Structure;
int STR;

Structure.field = 0;
STR = 1;
```

Estruturas podem ser inicializadas logo no momento da declaração. O iniciador da estrutura é colocado entre chaves e contém **uma lista de valores atribuídos aos campos subsequentes** , começando do primeiro.

Os valores devem estar de acordo com o tipo dos campos. Se houver menos valores atribuídos, o compilador completará com zeros.

Se o campo em particular for um array ou uma estrutura, ele deve ter seu próprio iniciador, que também está sujeito à regra de extensão zero. Se um iniciador “interno” estiver completo, podemos omitir os parênteses curvos ao redor.

##### Exemplo
```c
struct DATE date = {2012, 12, 21};
```

Esse iniciador é equivalente as atribuições a seguir.
```c
date.year = 2012;
date.month = 12;
date.day = 21;
```

O iniciador deste formulário é funcionalmente equivalente às seguintes atribuições:
```c
strcpy(he.name, "Bond");
he.time = 3.5;
he.recent_chapter = 4;
he.last_visit.year = 2012
he.last_visit.month = 12;
he.last_visit.day = 21;
```

Devido à completude do inicializador interno, ele pode ser escrito na seguinte forma simplificada:
```c
struct STUDENT he = { "Bond", 3.5, 4, 2012, 12, 21 };
```

Esse tipo de simplificação (omitindo chaves internas) também pode ser aplicado no seguinte caso (com cautela, porém):
```c
struct STUDENT she = { "Mata Hari", 12., 12, { 2012 }  };
```

O inicializador interno, se referindo ao campo `last_visit`, não cobre todos os campos. Isso significa que será equivalente a seguinte sequência de atribuições :
```c
strcpy(she.name, "Mata Hari");
she.time = 12.;
she.recent_chapter = 12;
she.last_visit.year = 2012
she.last_visit.month = 0;
she.last_visit.day = 0;
```

O que acontece quando aplicamos um inicializador “vazio”?
```c
strruct STUDENT nobody = {};
```

Aqui está a resposta:
```c
strcpy(nobody.name, "");
nobody.time = 0.0;
nobody.recent_chapter = 0;
nobody.last_visit.year = 0;
nobody.last_visit.month = 0;
nobody.last_visit.day = 0;
```






















