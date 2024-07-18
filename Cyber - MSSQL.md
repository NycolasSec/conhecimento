[[Linux - MSSQL]] | [[Redes - MSSQL]]

---

## Footprinting
 O NMAP tem scripts mssql padrão que podem ser usados ​​para direcionar a porta tcp padrão `1433`que o MSSQL escuta.  

O scan NMAP com script abaixo nos fornece informações úteis. Podemos ver o `hostname`, `database instance name`, `software version of MSSQL`e `named pipes are enabled`. Nós nos beneficiaremos ao adici onar essas descobertas às nossas notas.

### verificação de script NMAP MSSQL
```sh
NycolasES6@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```
```output
PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: SQL-01
|   NetBIOS_Domain_Name: SQL-01
|   NetBIOS_Computer_Name: SQL-01
|   DNS_Domain_Name: SQL-01
|   DNS_Computer_Name: SQL-01
|_  Product_Version: 10.0.17763

Host script results:
| ms-sql-dac: 
|_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed)
| ms-sql-info: 
|   Windows server name: SQL-01
|   10.129.201.248\MSSQLSERVER: 
|     Instance name: MSSQLSERVER
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|     Named pipe: \\10.129.201.248\pipe\sql\query
|_    Clustered: false
```

Podemos usar o Metasploit para executar um scanner auxiliar chamado , `mssql_ping`que escaneará o serviço MSSQL e fornecerá informações úteis em nosso processo de footprinting.

## Ping MSSQL no Metasploit
```sh
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248

rhosts => 10.129.201.248

msf6 auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
[+] 10.129.201.248:       -    ServerName      = SQL-01
[+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
[+] 10.129.201.248:       -    IsClustered     = No
[+] 10.129.201.248:       -    Version         = 15.0.2000.5
[+] 10.129.201.248:       -    tcp             = 1433
[+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
[*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

### Conectando com Mssclient.py
Se tivermos as credenciais, podemos nos conectar remotamente ao servidor MSSQL e começar a interagir com bancos de dados usando T-SQL ( `Transact-SQL`). A autenticação com MSSQL nos permitirá interagir diretamente com bancos de dados por meio do SQL Database Engine.

Podemos usar o mssqlclient.py do Impacket para conectar. Uma vez conectados podemos dar comandos.

```sh
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```
```output
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL-01): Line 1: Changed database context to 'master'.
[*] INFO(SQL-01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands

SQL> select name from sys.databases

name                                                                                                                          
------------------------------------------------------------

master                                                         

tempdb    

model                                                                                                                         
msdb                                                                                                                          
Transactions  
```

---

## Referências

HTB - https://academy.hackthebox.com/module/112/section/1246
MicroSoft - https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15
















































