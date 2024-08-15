Para criar uma conta local, usamos o Comando
`New-LocalUser`, que possui os seguintes parâmetros.


| Parâmetro                        | Valor              | Requirido |
| -------------------------------- | ------------------ | --------- |
| ``-AccountEspires``              | ``<DateTime>``     | Não       |
| ``-AccountNeverExpires``         |                    | Não       |
| ``-Description``                 | ``<String>``       | Não       |
| ``-Disable``                     |                    | Não       |
| ``-FullName``                    | ``<String>``       | Não       |
| ``-Name``                        | ``<String>``       | Sim       |
| ``-Password`` \| ``-NoPassword`` | ``<SecureString>`` | Sim       |
| ``-PasswordNeverExpires``        |                    | Não       |
| ``-UserMayNotChangePassword``    |                    | Não       |
| -``Confirm``                     |                    | Não       |
| -``WhatIf``                      |                    | Não       |

## Parâmetros
#### -AccountExpires
Especifica quando a conta de usuário expira. Você pode usar o `Get-Date` cmdlet para obter um **objeto DateTime** . Se você não especificar esse parâmetro, a conta não expirará.

|   |   |
|---|---|
|Type:|[DateTime](https://learn.microsoft.com/pt-br/dotnet/api/system.datetime)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -AccountNeverExpires
Indica que a conta não expira.

|                             |                                                                                                              |
| --------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Type:                       | [SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter) |
| Position:                   | Named                                                                                                        |
| Default value:              | None                                                                                                         |
| Required:                   | False                                                                                                        |
| Accept pipeline input:      | True                                                                                                         |
| Accept wildcard characters: | False                                                                                                        |

#### -Confirm
Solicita sua confirmação antes de executar o cmdlet.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Aliases:|cf|
|Position:|Named|
|Default value:|False|
|Required:|False|
|Accept pipeline input:|False|
|Accept wildcard characters:|False|

#### -Description
Especifica um comentário para a conta de usuário. O comprimento máximo é de 48 caracteres.

|   |   |
|---|---|
|Type:|[String](https://learn.microsoft.com/pt-br/dotnet/api/system.string)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -Disabled
Indica que esse cmdlet cria a conta de usuário como desabilitada.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -FullName
Especifica o nome completo da conta de usuário. O nome completo é diferente do nome de usuário da conta de usuário.

|   |   |
|---|---|
|Type:|[String](https://learn.microsoft.com/pt-br/dotnet/api/system.string)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -Name
Especifica o nome de usuário para a conta de usuário.

Um nome de usuário pode conter até 20 caracteres maiúsculos ou minúsculos. Um nome de usuário não pode conter os seguintes caracteres:

`"`, `/`, `\`, `[`, `]`, `:`, `;`, `|`, `=``,``+``*``?``<``>``@`

Um nome de usuário não pode consistir apenas em pontos `.` ou espaços.

|   |   |
|---|---|
|Type:|[String](https://learn.microsoft.com/pt-br/dotnet/api/system.string)|
|Position:|0|
|Default value:|None|
|Required:|True|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -NoPassword
Indica que a conta de usuário não tem uma senha.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Position:|Named|
|Default value:|None|
|Required:|True|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -Password
Especifica uma senha para a conta de usuário. Você pode usar `Read-Host -AsSecureString`, `Get-Credential`ou `ConvertTo-SecureString` para criar um **objeto SecureString** para a senha.

Se você omitir os **parâmetros Password** e **NoPassword** , `New-LocalUser` solicitará a senha do novo usuário.

|   |   |
|---|---|
|Type:|[SecureString](https://learn.microsoft.com/pt-br/dotnet/api/system.security.securestring)|
|Position:|Named|
|Default value:|None|
|Required:|True|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -PasswordNeverExpires
Indica se a senha do novo usuário expira.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -UserMayNotChangePassword
Indica que o usuário não pode alterar a senha na conta de usuário.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Position:|Named|
|Default value:|None|
|Required:|False|
|Accept pipeline input:|True|
|Accept wildcard characters:|False|

#### -WhatIf
Mostra o que aconteceria se o cmdlet fosse executado. O cmdlet não é executado.

|   |   |
|---|---|
|Type:|[SwitchParameter](https://learn.microsoft.com/pt-br/dotnet/api/system.management.automation.switchparameter)|
|Aliases:|wi|
|Position:|Named|
|Default value:|False|
|Required:|False|
|Accept pipeline input:|False|
|Accept wildcard characters:|False|

### Exemplos

**Exemplo 1**
```powershell
New-LocalUser -Name 'User02' -Description 'Description of this account.' -NoPassword
```
se comando cria uma conta de usuário local e não especifica os **parâmetros AccountExpires** ou **Password** . A conta não expira nem tem uma senha.

**Exemplo 2**
```powershell
$Password = Read-Host -AsSecureString
$params = @{
    Name        = 'User03'
    Password    = $Password
    FullName    = 'Third User'
    Description = 'Description of this account.'
}
New-LocalUser @params

Name    Enabled  Description
----    -------  -----------
User03  True     Description of this account.
```
O primeiro comando usa o `Read-Host` cmdlet para solicitar uma senha. O comando armazena a senha como uma cadeia de caracteres segura na `$Password` variável.

O segundo comando cria uma conta de usuário local e define a senha da nova conta para a cadeia de caracteres segura armazenada no `$Password`. O comando especifica um nome de usuário, nome completo e descrição para a conta de usuário.

**Exemplo 2**
```powershell
New-LocalUser -Name "Serverworld" -FullName "Server World" -Description "Administrator of this Computer"
-Password (ConvertTo-SecureString -AsPlainText "P@ssw0rd01" -Force) -PasswordNeverExpires
-AccountNeverExpires
```