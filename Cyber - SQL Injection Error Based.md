A intenção aqui é gerar um erro para podermos analisar uma parte da query e/ou rodar **comandos baseados em erro**.

Existem queries no SQL que rodam baseados em erro, ou seja, caso um erro sela gerado ela é executada.

**Temos como exemplo essa query.**
```sql
SELECT * FROM produtos WHERE title LIKE '%<VALOR>%' OR description LIKE '%<VALOR>%'
```

Temos que ter uma noção de como funciona a query pada podermos ser bem sucedidos.

Como a query utiliza o operador ``LIKE`` com ``'%<VALOR>%'``, podemos encerrar essa parte colocando `%' -- -`, e então a query ficaria assim.

```sql
SELECT * FROM produtos WHERE title LIKE '%%' -- -%' OR description LIKE '%<VALOR>%'
```

Após isso podemos injetar nossos comandos.



