[[Linux - MSSQL]] | [[Cyber - MSSQL]]

---

## MSSQL
O Microsoft SQL é o sistema de gerenciamento de banco de dados relacional baseado em SQL da Microsoft, é de código fechado, feito para lidar com Windows. Tem forte suporte ao .NET, há versões que rodam em Linux, mas é provável que encontraremos em alvos Windows

## Clientes MSSQL
O SQL Server Management Studio (SSMS) vem como um  recurso que pode ser instalado com o pacote de instalação do MSSQL ou pode ser baixado e instalado separadamente. Tenha em mente que, como o SSMS é um aplicativo do lado do cliente, ele pode ser instalado e usado em qualquer sistema do qual um administrador ou desenvolvedor esteja planejando gerenciar o banco de dados.

![[ssms.webp]]

Muitos outros clientes podem ser usados ​​para acessar um banco de dados em execução no MSSQL. Incluindo, mas não limitado a:
  
| | | | |
|---|---|---|---|
|[mssql-cli](https://docs.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver15)|[Servidor SQL PowerShell](https://docs.microsoft.com/en-us/sql/powershell/sql-server-powershell?view=sql-server-ver15)|[HeidiSQL](https://www.heidisql.com/)|[SQLPro](https://www.macsqlclient.com/)|[mssqlclient.py do Impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|

Dos clientes MSSQL listados acima, os pentesters podem achar o mssqlclient.py do Impacket o mais útil devido ao projeto Impacket do SecureAuthCorp estar presente em muitas distribuições de pentesting na instalação.

Podemos descobrir onde o cliene está instalado podemos usa :
```bash
locate mssqlclient

/usr/bin/impacket-mssqlclient
/usr/share/doc/python3-impacket/examples/mssqlclient.py
```

### Banco de dados MSSQL
Tem banco de dados de sistema padrão, que nos ajudam a entender a estrutura de todos os bancos. Aqui está um banco de dados padrão e uma breve descrição de cada um:

| Banco de dados do sistema padrão | Descrição                                                                                                                                                                                                                                                  |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `master`                         | Rastreia todas as informações do sistema para uma instância do servidor SQL                                                                                                                                                                                |
| `model`                          | Banco de dados de modelo que atua como uma estrutura para cada novo banco de dados criado. Qualquer configuração alterada no banco de dados de modelo será refletida em qualquer novo banco de dados criado após as alterações no banco de dados de modelo |
| `msdb`                           | O SQL Server Agent usa esse banco de dados para agendar trabalhos e alertas                                                                                                                                                                                |
| `tempdb`                         | Armazena objetos temporários                                                                                                                                                                                                                               |
| `resource`                       | Banco de dados somente leitura contendo objetos do sistema incluídos no servidor SQL                                                                                                                                                                       |
[Fonte da tabela](https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15)

---

## Referências

HTB - https://academy.hackthebox.com/module/112/section/1246
MicroSoft - https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15
