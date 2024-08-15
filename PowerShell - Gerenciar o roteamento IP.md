O módulo do PowerShell **NETTCPIP** também inclui cmdlets usados para gerenciar a tabela de roteamento para servidores e dispositivos Windows.

Os cmdlets usados para gerenciar entradas de tabela de roteamento contêm o substantivo "NetRoute" nos nomes.

A tabela a seguir lista cmdlets comumente usados para gerenciar as entradas e as configurações da tabela de roteamento.

| Cmdlet              | Descrição                                                                     |
| ------------------- | ----------------------------------------------------------------------------- |
| **New-NetRoute**    | Cria uma entrada na tabela de roteamento de IP                                |
| **Get-NetRoute**    | Recupera uma entrada da tabela de roteamento de IP                            |
| **Set-NetRoute**    | Modifica as propriedades de uma entrada na tabela de roteamento de IP         |
| **Remove-NetRoute** | Exclui uma entrada da tabela de roteamento de IP                              |
| **Find-NetRoute**   | Identifica o melhor endereço IP local e rota para alcançar um endereço remoto |

## Criar uma entrada na tabela de roteamento de IP
Você pode usar o cmdlet **New-NetRoute** para criar entradas de tabela de roteamento em um computador Windows. O cmdlet **New-NetRoute** exige que você identifique o adaptador de rede e o prefixo de destino.

A tabela a seguir lista parâmetros comuns do cmdlet **New-NetRoute**.

| Parâmetro          | Descrição                                                |
| ------------------ | -------------------------------------------------------- |
| ‑DestinationPrefix | Define o prefixo de destino de uma rota IP               |
| ‑InterfaceAlias    | Define o adaptador de rede, por alias, para uma rota IP  |
| ‑InterfaceIndex    | Define o adaptador de rede, por índice, para uma rota IP |
| ‑NextHop           | Define o próximo salto para uma rota IP                  |
| ‑RouteMetric       | Define a métrica de rota para uma rota IP                |

```powershell
New-NetRoute -DestinationPrefix 0.0.0.0/24 -InterfaceAlias "Ethernet" -DefaultGateway 192.168.1.1
```



















