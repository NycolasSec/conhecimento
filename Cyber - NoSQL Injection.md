Se trata da utilização de um dado submetido pelo usuário sem o devido tratamento. Possibilitando assim a injeção de comandos no banco de dados tanto ``SQL`` quanto ``NoSQL``.

## Operadores MongoDB
### Comparação
Os seguintes operadores podem ser usados ​​em consultas para comparar valores:

- `$eq`:Os valores são iguais
- `$ne`:Os valores não são iguais
- `$gt`: O valor é maior que outro valor
- `$gte`: O valor é maior ou igual a outro valor
- `$lt`: O valor é menor que outro valor
- `$lte`: O valor é menor ou igual a outro valor
- `$in`: O valor é correspondido dentro de uma matriz

### Lógico
Os seguintes operadores podem comparar logicamente várias consultas.

- `$and`: Retorna documentos onde ambas as consultas correspondem
- `$or`: Retorna documentos onde qualquer consulta corresponde
- `$nor`: Retorna documentos onde ambas as consultas não correspondem
- `$not`: Retorna documentos onde a consulta não corresponde

### Avaliação
Os seguintes operadores auxiliam na avaliação de documentos.

- `$regex`: Permite o uso de expressões regulares ao avaliar valores de campo
- `$text`: Executa uma pesquisa de texto
- `$where`: Usa uma expressão JavaScript para corresponder a documentos
Podemos colocar operadores nas nossas requisições para causar um processamento diferente. Algumas linhagens tem interações diferentes quando recebem algum parâmetro não esperado.

## PHP
Como o PHP que se colocarmos o nome de um input e depois `[]`, ele considera como um array.

```html
<input name="name" id="name">
<input name="password" id="password">
```
Podemos alterar o nome para causar algum processamento diferente.
```html
<input name="name[]" id="name">
<input name="password[]" id="password">
```
ou
```html
<input name="name[$in]" id="name">
<input name="password[$in]" id="password">
```
ou
```html
<input name="name" id="name">
<input name="password[$ne]" id="password">
```

## Tipos de injeção NoSQL

- Injeção de sintaxe - Isso ocorre quando você pode quebrar a sintaxe de consulta NoSQL, permitindo que você injete sua própria carga útil. A metodologia é semelhante à usada na injeção de SQL. No entanto, a natureza do ataque varia significativamente, pois os bancos de dados NoSQL usam uma variedade de linguagens de consulta, tipos de sintaxe de consulta e diferentes estruturas de dados.
- Injeção de operadores - Isso ocorre quando você pode usar operadores de consulta NoSQL para manipular consultas.

## Injeção de sintaxe NoSQL

Você pode detectar vulnerabilidades de injeção NoSQL ao tentar quebrar a sintaxe da consulta. Para fazer isso, teste sistematicamente cada entrada enviando strings fuzz e caracteres especiais que disparam um erro de banco de dados ou algum outro comportamento detectável se não forem adequadamente higienizados ou filtrados pelo aplicativo.

Se você conhece a linguagem da API do banco de dados de destino, use caracteres especiais e fuzz strings que sejam relevantes para essa linguagem. Caso contrário, use uma variedade de fuzz strings para direcionar várias linguagens da API.

### Detectando injeção de sintaxe no MongoDB

Considere um aplicativo de compras que exibe produtos em diferentes categorias. Quando o usuário seleciona a categoria **Fizzy drinks** , seu navegador solicita a seguinte URL:

`https://insecure-website.com/product/lookup?category=fizzy`

Isso faz com que o aplicativo envie uma consulta JSON para recuperar produtos relevantes da `product`coleção no banco de dados MongoDB:

`this.category == 'fizzy'`

Para testar se a entrada pode ser vulnerável, envie uma string fuzz no valor do `category`parâmetro. Uma string de exemplo para MongoDB é:

```
'"`{
;$Foo}
$Foo \xYZ
```

Use esta sequência de caracteres fuzz para construir o seguinte ataque:

```
https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00
```

Se isso causar uma alteração na resposta original, isso pode indicar que a entrada do usuário não foi filtrada ou higienizada corretamente.


## Por Swigger
Bancos de dados NoSQL armazenam e recuperam dados em um formato diferente das tabelas relacionais SQL tradicionais. Eles usam uma ampla gama de linguagens de consulta em vez de um padrão universal como SQL, e têm menos restrições relacionais.

![[nosql-injection-graphic.svg]]

























---

## Referencias
PortSwigger - https://portswigger.net/web-security/nosql-injection