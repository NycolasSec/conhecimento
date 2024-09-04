A alteração do tipo de dados (talvez combinada com uma alteração de seu valor, que pode ser causada por uma perda de precisão) é chamada de **conversão**.

A linguagem "C" conhece dois tipos de conversões:
- **conversões implícitas**, que funcionam de acordo com regras de linguagem e não são especificadas no código de forma visível; sua operação é silenciosa e automática;
- **conversões explícitas** realizadas a pedido do desenvolvedor; O desenvolvedor deve inseri-los explicitamente dentro do código, indicando qual valor deve ser convertido e em qual tipo resultante.
- 
As conversões implícitas são executadas em tempo de execução de acordo com essas regras estritas. As regras são aplicadas na ordem abaixo até que todos os dados usados na expressão específica tenham **o mesmo tipo** – essa condição é muito importante!

- Os dados do tipo char ou short int serão convertidos para o tipo int (isso é chamado de **promoção de inteiro**);
- Se houver algum valor do tipo float na expressão, os outros dados serão convertidos em float;
- Se houver algum valor do tipo double na expressão, os outros dados serão convertidos em double;
- Se houver algum valor do tipo long int na expressão, os outros dados serão convertidos em long int.

Se o contexto no qual a expressão é calculada exigir outro tipo que não o resultante das conversões implícitas, a última conversão será feita para o tipo solicitado pelo contexto.

Conversões explícitas são introduzidas no código usando o operador **typecast**. Este é um operador unário com uma prioridade alta, igual à prioridade unária menos. Você pode vê-lo aqui:

```c
(type)value
```
onde type é um nome ou descrição do tipo no qual o valor será convertido.

Por exemplo, no trecho a seguir, a variável x do tipo float é **explicitamente convertida** no tipo double.
```c
float x;
double y;

y = (double) x;
```

##### Tabela atualizada:
| (type) ++ -- + - | unary  |
| ---------------- | ------ |
| * / %            |        |
| + -              | binary |
| < <= > >=        |        |
| == !=            |        |
| = += -= *= /= %= |        |


















