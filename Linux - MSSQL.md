[[Cyber - MSSQL]] | [[Redes - MSSQL]]

---

## Configuração padrão
Quando se instala para ser acessível pela rede, o serviço SQL provavelmente será executado como `NT SERVICE/MSSQLSERVER`. A conexão do lado do cliente é feita por meio da Autenticação do Windows e, por padrão, a criptografia não é aplicada ao tentar se conectar.

![[auth.webp]]

Autenticação como `Windows Authentication` diz que o `OS` subjacente processará a solicitação de login e usará o banco de dados SAM local ou o controlador de domínio (hospedando o AD) antes de permitir a conectividade com o sistema de gerenciamento de banco de dados. Usar o Active Directory pode ser ideal para auditar atividades e controlar o acesso em um ambiente Windows.

## Configurações perigosas
Pode ser benéfico nos colocarmos na perspectiva de um administrador de TI quando estamos em um compromisso.

Esta não é uma lista extensa porque há inúmeras maneiras de configurar bancos de dados MSSQL por administradores com base nas necessidades de suas respectivas organizações. Podemos nos beneficiar ao analisar o seguinte:

- Clientes MSSQL não usam criptografia para se conectar ao servidor MSSQL
    
- O uso de certificados autoassinados quando a criptografia está sendo usada. É possível falsificar certificados autoassinados
    
- O uso de [pipes nomeados](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
    
- Credenciais fracas e padrão `sa`. Os administradores podem esquecer de desabilitar esta conta

---

## Referências

HTB - https://academy.hackthebox.com/module/112/section/1246
MicroSoft - https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15
