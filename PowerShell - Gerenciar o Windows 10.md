O módulo **Microsoft.PowerShell.Management** inclui muitos cmdlets internos que podem ser usados para obter informações e executar operações específicas em um computador local. Para examinar os cmdlets incluídos neste módulo, insira o seguinte:

```powershell
Get-command -module Microsoft.PowerShell.Management
```

A tabela a seguir lista alguns dos cmdlets mais comuns incluídos no módulo **Microsoft.PowerShell.Management**.

| **Cmdlet**           | **Descrição**                                                                                                      |
| -------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Get-ComputerInfo** | Recupera todas as propriedades do sistema e do sistema operacional do computador                                   |
| **Get-Service**      | Recupera uma lista de todos os serviços no computador                                                              |
| **Get-EventLog**     | Recupera eventos e logs de eventos de computadores locais e remotos (disponível somente no Windows PowerShell 5.1) |
| **Get-Process**      | Recupera uma lista de todos os processos ativos em um computador local ou remoto                                   |
| **Stop-Service**     | Interrompe um ou mais serviços em execução                                                                         |
| **Stop-Process**     | Interrompe um ou mais processos em execução                                                                        |
| **Stop-Computer**    | Desliga computadores locais e remotos                                                                              |
| **Clear-EventLog**   | Exclui todas as entradas de logs de eventos especificados no computador local ou em computadores remotos           |
| **Clear-RecycleBin** | Exclui o conteúdo da lixeira de um computador                                                                      |
| **Restart-Computer** | Reinicia o sistema operacional em computadores locais e remotos                                                    |
| **Restart-Service**  | Interrompe e inicia um ou mais serviços                                                                            |

## Executando cmdlets de gerenciamento

Os seguintes cmdlets são exemplos de como usar alguns dos cmdlets de gerenciamento no Windows 10:

Para recuperar informações detalhadas sobre o computador local, execute o seguinte comando:
```powershell
Get-ComputerInfo
```

Para recuperar as cinco entradas de erro mais recentes do log do aplicativo, execute o seguinte comando:
```powershell
Get-EventLog -LogName Application -Newest 5 -EntryType Error
```

Para limpar o log do aplicativo no computador local, execute o seguinte comando:
```powershell
Clear-EventLog -LogName Application
```


























































































