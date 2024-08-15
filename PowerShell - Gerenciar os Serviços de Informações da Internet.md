Os IIS dão suporte a cmdlets do PowerShell para permitir que você configure e gerencie pools de aplicativos, sites, aplicativos Web e diretórios virtuais.

Os cmdlets de gerenciamento de IIS estão disponíveis no módulo **IISAdministration** do PowerShell e têm o prefixo "IIS" na parte substantiva do nome. Os sites usam o substantivo "IISSite".

Para gerenciar aplicativos baseados na Web, você pode usar o módulo **WebAdministration** para o PowerShell, que inclui cmdlets para gerenciar aplicativos Web. Esses cmdlets usam o substantivo "WebApplication". Os cmdlets para gerenciar pools de aplicativos usam o substantivo "WebAppPool".

A tabela a seguir lista cmdlets comuns de administração de aplicativos Web e IIS.

| **Cmdlet**                | **Descrição**                                                         |
| ------------------------- | --------------------------------------------------------------------- |
| **New-IISSite**           | Cria um novo site de IIS                                              |
| **Get-IISSite**           | Obtém propriedades e informações de configuração sobre um site de IIS |
| **Start-IISSite**         | Inicia um site de IIS existente no servidor de IIS                    |
| **Stop-IISSite**          | Interrompe um site de IIS                                             |
| **New-WebApplication**    | Criar um novo aplicativo Web                                          |
| **Remove-WebApplication** | Excluir um aplicativo Web                                             |
| **New-WebAppPool**        | Criar um novo pools de aplicativos Web                                |
| **Restart-WebAppPool**    | Reinicia um pool de aplicativos Web                                   |















































