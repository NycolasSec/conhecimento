[[Redes - MySQL]] | [[Linux - MySQL]]

---

## Footprinting

Há muitas razões de  um servidor MySQL poder ser acessado remotamente, mais isso arriscado. Normalmente, o servidor MySQL é executado em `TCP port 3306`, e podemos escanear essa porta com `Nmap`para obter informações mais detalhadas.

```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

Algumas informações podem ser falso positivos, então temos de ter cuidado.

#### Interação com o servidor MySQL

```sh
mysql -u root -h 10.129.14.132

# ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)

```

Por exemplo, se usarmos uma senha que adivinhamos ou encontramos por meio de nossa pesquisa, poderemos efetuar login no servidor MySQL e executar alguns comandos.

```sh
mysql -u root -pP4SSw0rd -h 10.129.14.128
```
```mysql
#Welcome to the MariaDB monitor.  Commands end with ; or \g.
#Your MySQL connection id is 150165
#Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                         
#Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                     
#Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                           
      
MySQL [(none)]> show databases;                                                                          
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.006 sec)

```

Aqui vemos que existem vários bancos de dados, os mais importantes são `system schema`( `sys`) e `information schema`( `information_schema`). O esquema do sistema contém tabelas, informações e metadados necessários para o gerenciamento.

```mysql
mysql> use sys;
mysql> show tables;  

+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
...SNIP...
| x$waits_global_by_latency                     |
+-----------------------------------------------+


mysql> select host, unique_users from host_summary;

+-------------+--------------+                   
| host        | unique_users |                   
+-------------+--------------+                   
| 10.129.14.1 |            1 |                   
| localhost   |            2 |                   
+-------------+--------------+                   
2 rows in set (0,01 sec)  
```

O `information schema`também é um banco de dados que contém metadados, mas esses metadados são recuperados principalmente do banco de dados `system schema`. Esses dois é o padrão ANSI/ISO que foi estabelecido.

`System schema`é um catálogo de sistema da Microsoft para servidores SQL e contém muito mais informações do que o `information schema`.

Aqui está alguns comandos que devemos saber para trabalhar com MySQL.

| **Comando**                                          | **Descrição**                                                                               |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `mysql -u <user> -p<password> -h <IP address>`       | Conecte-se ao servidor MySQL. **Não** deve haver espaço entre o sinalizador '-p' e a senha. |
| `show databases;`                                    | Mostrar todos os bancos de dados.                                                           |
| `use <database>;`                                    | Selecione um dos bancos de dados existentes.                                                |
| `show tables;`                                       | Mostrar todas as tabelas disponíveis no banco de dados selecionado.                         |
| `show columns from <table>;`                         | Mostrar todas as colunas no banco de dados selecionado.                                     |
| `select * from <table>;`                             | Mostrar tudo na tabela desejada.                                                            |
| `select * from <table> where <column> = "<string>";` | Procure o necessário `string`na tabela desejada.                                            |

---

## Referências

**`Referência dp MySQL`** : https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html
**`HTB - Footiprinting`** : https://academy.hackthebox.com/module/112/section/1238
