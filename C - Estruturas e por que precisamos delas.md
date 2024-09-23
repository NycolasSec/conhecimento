Estruturas são objetos onde podemos armazenar variáveis de tipos diferentes, assim não precisamos criar uma variedade de arrays de tipos diferentes.

Uma estrutura contém **qualquer número de elementos de qualquer tipo** . Cada um desses elementos é chamado de **campo** . Cada campo é identificado por seu nome, não por seu número. Obviamente, os nomes dos campos devem ser **únicos** e não podem ser duplicados dentro de uma única estrutura.

#### Exemplo 1
```c
sctruct STUDENT{
	char name[26];
	float time;
	int recent_chapter;
}
```

- A declaração da estrutura começa com a palavra ``sctruct``.
- Uma tag struct é colocada depois da palavra-chave (_STRUCT_ no caso); é o nome da estrutura em si; há um costume de declarar tags de estrutura com letras maiúsculas apenas para diferenciar de variáveis comuns.
- Depois vem a chave de abertura que indica onde começa o campo de declaração, no caso acima temos 3 campos.

A declaração anterior não cria nenhuma variável, apenas descreve a estrutura que usaremos.

Podemos declarar uma variável como uma estrutura da seguinte forma.

```c
struct STUDENT stdnt;
```
Assim configuramos uma variável estruturada chamada `stdnt`. Essa variável é do tipo struct `ESTUDENT`, sabemos que essa variável consiste em 3 campos nomeados.

Como a linguagem “C” oferece um operador de indexação especializado **`[]`** para matrizes, também temos o **operador de seleção** , projetado para estruturas e denotado como um único caractere.(ponto).

A prioridade do operador de seleção é muito alta, igual à prioridade do **``[]``** operador.

Este é um operador binário. Seu argumento esquerdo **deve identificar a estrutura** enquanto o argumento direito deve ser o **nome do campo** conhecido nesta estrutura.

O resultado desse operador é o campo da estrutura selecionado.

```c
stdnt.time = 1.5;

float t;

t = stdnt.time;
```

As estruturas podem ser agregadas dentro de um array, então se quisermos declarar um array consistindo de estruturas ``ESTUDANTE`` , fazemos assim:

```c
struct STUDENT stdnt[100000];
```

O acesso aos campos selecionados requer duas operações subsequentes:

- no primeiro passo, o[]o operador indexa o array para acessar a estrutura que precisamos;
- na segunda etapa, o operador de seleção seleciona o campo desejado.

Isso significa que se quisermos selecionar o campo de tempo do quarto elemento stdnts, escrevemos da seguinte forma:

```c
stdnts[3].time
```

Coletamos todas essas atribuições que foram realizadas para os três arrays separados. Analise-as cuidadosamente:

```c
strcpy(stdnts[0].name, "Bond");
stdnts[0].time = 3.5;
stdnts[0].recent_chapter = 4;
```

---
#### Exemplo 2

```c
struct DATE {
    int year;
    int month;
    int day;
};

struct DATE DateOfBirth;

DateOfBirth.year = 1980;
DateOfBirth.month = 7;
DateOfBirth.day = 31;
```
A primeira declaração não cria nenhuma estrutura, apenas anuncia ao compilador que nossa intenção de usar essa tag para declarar novas variáveis.

Também podemos usar a tag DAY para declarar um array de estruturas.

```c
struct DATE visits[100];
```

Também é possível matar dois coelhos com uma cajadada só definindo a tag structure e declarando qualquer número de variáveis ​​simultaneamente na mesma instrução, assim:

```c
struct DATE {
	int year, month, day;
} DateOfBirth, visits[100];

struct DATE current_date;
```

Também podemos omitir a tag e declarar apenas as variáveis:

```c
struct {
	int year, month, day;
} the_date_of_the_end_of_the_world;
```

Uma estrutura pode ser um campo dentro de outra estrutura.

```c
struct STUDENT {
    char  name[26];
    float time;
    int   recent_chapter;
    struct DATE last_visit;
} HarryPotter;
```

Isso significa que teremos que usar duas operações de seleção subsequentes para **nos aprofundarmos** na estrutura, ou seja, primeiro selecionamos uma estrutura dentro da estrutura e depois selecionamos o campo desejado da estrutura interna.

É assim que funciona:

```c
HarryPotter.last_visit.year = 2012;
HarryPotter.last_visit.month = 12;
HarryPotter.last_visit.day = 21;
```
