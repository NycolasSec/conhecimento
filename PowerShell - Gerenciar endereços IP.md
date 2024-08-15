O PowerShell conta com o módulo **NetTCPIP**, que consiste em cmdlets específicos de TCP/IP usados para gerenciar as configurações de rede para servidores e dispositivos Windows.

Os cmdlets de gerenciamento de endereço IP usam o substantivo "NetIPAddress" em seus nomes.

A tabela a seguir lista cmdlets comumente usados para gerenciar configurações de endereços IP.

| Cmdlet                  | Descrição                                  |
| ----------------------- | ------------------------------------------ |
| **New-NetIPAddress**    | Cria um novo endereço IP                   |
| **Get-NetIPAddress**    | Exibe as propriedades de um endereço IP    |
| **Set-NetIPAddress**    | Modifica as propriedades de um endereço IP |
| **Remove-NetIPAddress** | Exclui um endereço IP                      |

## Criar novas configurações de endereço IP

O cmdlet **New-NetIPAddress** requer um endereço IPv4 ou IPv6 e o alias ou o índice de um adaptador de rede. Como melhor prática, você também deve definir o gateway padrão e a máscara de sub-rede ao mesmo tempo.

A tabela a seguir lista parâmetros comuns do cmdlet **New-NetIPAddress**.

| Parâmetro       | Descrição                                                   |
| --------------- | ----------------------------------------------------------- |
| -IPAddress      | Define o endereço IPv4 ou IPv6 a ser criado                 |
| -InterfaceIndex | Define o adaptador de rede, por índice, para um endereço IP |
| -InterfaceAlias | Define o adaptador de rede, por nome, para um endereço IP   |
| -DefaultGateway | Define o endereço IPv4 ou IPv6 do host de gateway padrão    |
| -PrefixLength   | Define a máscara de sub-rede para o endereço IP             |
O comando a seguir cria um novo endereço IP na interface Ethernet:

```powershell
New-NetIPAddress -IPAddress 192.168.1.10 -InterfaceAlias "Ethernet" -PrefixLength 24 -DefaultGateway 192.168.1.1
```

O cmdlet **New-NetIPAddress** também aceita o parâmetro _–AddressFamily_, que define a família de endereços IP IPv4 ou IPv6. Se você não usar esse parâmetro, a propriedade da família de endereços será detectada automaticamente.

































