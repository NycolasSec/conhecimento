O módulo **ServerManager** do PowerShell contém cmdlets para gerenciar recursos, funções e serviços do servidor. Esses cmdlets são o equivalente à interface do usuário **Gerenciador do Servidor**. Os nomes de cmdlets do **Gerenciador do Servidor** incluem o substantivo "WindowsFeature".

A tabela a seguir lista os cmdlets de gerenciamento do servidor.

|**Cmdlet**|**Descrição**|
|---|---|
|**Get-WindowsFeature**|Obtém e exibe informações sobre funções, serviços e funcionalidades do Windows Server que estão instalados ou disponíveis para instalação|
|**Install-WindowsFeature**|Instala uma ou mais funções, serviços ou funcionalidades|
|**Uninstall-WindowsFeature**|Desinstala uma ou mais funções, serviços ou funcionalidades|

O comando a seguir instala o balanceamento de carga de rede no servidor local:

```powershell
Install-WindowsFeature "nlb"
```