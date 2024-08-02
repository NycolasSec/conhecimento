O módulo do Active Directory para o Windows PowerShell também tem cmdlets para criar, modificar e excluir contas de computadores. Você pode usar esses cmdlets em operações individuais ou como parte de um script para executar operações em massa.

Os cmdlets de gerenciamento de objetos de computador têm o texto "computador" em seus nomes.

A tabela a seguir lista os cmdlets que podem ser usados para gerenciar contas de computadores.

| Cmdlet                            | Descrição                                                                 |
| --------------------------------- | ------------------------------------------------------------------------- |
| **New-ADComputer**                | Criar uma nova conta de computador                                        |
| **Set-ADComputer**                | Modifica propriedades de uma conta de computador                          |
| **Get-ADComputer**                | Exibe as propriedades de uma conta de computador                          |
| **Remove-ADComputer**             | Excluir uma conta de computador                                           |
| **Test-ComputerSecureChannel**    | Verifica ou repara a relação de confiança entre um computador e o domínio |
| **Reset-ComputerMachinePassword** | Redefine a senha de uma conta de computador                               |
|                                   |                                                                           |

## Criar novas contas de computador
Você pode usar o cmdlet **New-ADComputer** para criar uma nova conta de computador antes de ingressar o computador no domínio. Faça isso para que você possa criar a conta de computador na UO correta antes de implantar o computador.

A tabela a seguir lista parâmetros os comuns de **New-ADComputer**.

| Parâmetro | Descrição                                                                                                                                           |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| ‑Name     | Define o nome de uma conta de computador                                                                                                            |
| ‑Path     | Define a UO ou o contêiner em que uma conta de computador é criada                                                                                  |
| ‑Enabled  | Define se a conta do computador está habilitada ou desabilitada; por padrão, uma conta de computador está habilitada e uma senha aleatória é gerada |

O exemplo a seguir é um comando que você pode usar para criar uma conta de computador:
```powershell
New-ADComputer -Name LON-CL10 -Path "ou=marketing,dc=adatum,dc=com" -Enabled $true 
```

## Reparar a relação de confiança de uma conta de computador
Você pode usar o cmdlet **Test-ComputerSecureChannel** com o parâmetro _-Repair_ para reparar uma relação de confiança perdida entre um computador e um domínio. Você deve executar o cmdlet no computador com a relação de confiança perdida.

## Cmdlets de gerenciamento de conta versus de dispositivo
Os cmdlets **-ADComputer** fazem parte do módulo do Active Directory e gerenciam o objeto de computador, não o dispositivo físico ou seu sistema operacional. Por exemplo, você pode usar o cmdlet **Add-Computer** para ingressar um computador em um domínio. Para gerenciar as propriedades do computador físico e seu sistema operacional, use os cmdlets **-Computer**.





















