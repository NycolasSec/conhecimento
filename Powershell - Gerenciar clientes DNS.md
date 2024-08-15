Os cmdlets de gerenciamento de cliente DNS fazem parte do módulo **DNSClient** do PowerShell e têm o texto "DnsClient" como substantivo do nome.

A tabela a seguir lista cmdlets comuns para modificar as configurações do cliente DNS.

| Cmdlet                         | Descrição                                                                        |
| ------------------------------ | -------------------------------------------------------------------------------- |
| **Get-DnsClient**              | Obtém detalhes sobre um adaptador de rede                                        |
| **Set-DnsClient**              | Define as configurações de configuração do cliente DNS para um adaptador de rede |
| **Get-DnsClientServerAddress** | Obtém as configurações de endereço do servidor DNS para um adaptador de rede     |
| **Set-DnsClientServerAddress** | Define o endereço do servidor DNS para um adaptador de rede                      |
O comando a seguir define o sufixo específico da conexão para uma interface:

```
Set-DnsClient -InterfaceAlias Ethernet -ConnectionSpecificSuffix "adatum.com"
```

































