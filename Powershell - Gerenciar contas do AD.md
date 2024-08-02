Você pode usar os cmdlets do módulo do Active Directory para Windows PowerShell para criar, modificar e excluir contas de usuário individualmente ou em massa. Os cmdlets da conta de usuário têm a palavra "User" ou "Account" na parte substantiva do nome. Para identificar os cmdlets disponíveis, inclua-as em pesquisas de nome com caractere curinga quando estiver usando **Get-Help** ou **Get-Command**.

#### cmdlets normalmente usados para gerenciamento de contas de usuários
| Cmdlet                    | Descrição                                                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **New-ADUser**            | Cria uma conta de usuário                                                                                                   |
| **Get-ADUser**            | Recupera uma conta de usuário                                                                                               |
| **Set-ADUser**            | Modifica propriedades de uma conta de usuário                                                                               |
| **Remove-ADUser**         | Exclui uma conta de usuário                                                                                                 |
| **Set-ADAccountPassword** | Redefine a senha de uma conta de usuário                                                                                    |
| **Unlock-ADAccount**      | Desbloqueia uma conta de usuário que foi bloqueada depois de exceder o número permitido de tentativas de entrada incorretas |
| **Enable-ADAccount**      | Habilita uma conta de usuário                                                                                               |
| **Disable-ADAccount**     | Desabilita uma conta de usuário                                                                                             |

## Recuperando usuários

O cmdlet **Get-ADUser** exige que você identifique o usuário ou os usuários que deseja recuperar.

Você pode fazer isso usando o parâmetro _-Identity_, que aceita um dentre vários valores de propriedade, incluindo o nome da conta SAM (Gerenciador de Contas de Segurança) ou o nome diferenciado.

O Windows PowerShell retorna apenas um conjunto padrão de propriedades quando você usa **Get-ADUser**. Para examinar outras propriedades, você precisará usar o parâmetro _-Properties_ com uma lista separada por vírgulas de propriedades ou o caractere curinga "*".

você pode recuperar o conjunto padrão de propriedades junto com o departamento e o endereço de email de um usuário com a conta SAM **anabowman** inserindo o seguinte comando no console e pressionando a tecla Enter:
```powershell
Get-ADUser -Identity anabowman -Properties Department,EmailAddress
```

A outra maneira de especificar um usuário ou usuários é com o parâmetro _-Filter_. O parâmetro _-Filter_ aceita uma consulta com base em expressões regulares, que os módulos posteriores neste curso descrevem com mais detalhes.
```powershell
Get-ADUser -Filter * -Properties *
```

## Criando novas conta de usuário
Quando você usa o cmdlet **New-ADUser** para criar novas contas de usuário, o parâmetro -_Name_ é necessário.
Você também pode definir a maioria das outras propriedades do usuário, incluindo uma senha. 

Ao criar uma nova conta, considere os seguintes pontos:
	- Se você não usar o parâmetro _-AccountPassword_, nenhuma senha será definida e a conta de usuário será desabilitada. Você não pode definir o parâmetro _-Enabled_ como `$true` quando nenhuma senha está definida.
	- Se você usar o parâmetro _-AccountPassword_ para especificar uma senha, deverá especificar uma variável que contenha a senha como uma cadeia de caracteres segura ou optar por inserir a senha do console. Uma cadeia de caracteres segura é criptografada na memória.
	- Se você definir uma senha, poderá habilitar a conta de usuário definindo o parâmetro _-Enabled_ como `$true`.


A tabela a seguir lista parâmetros comuns do cmdlet **New-ADUser**.

| Parâmetro              | Descrição                                                                                     |
| ---------------------- | --------------------------------------------------------------------------------------------- |
| ‑AccountExpirationDate | Define a data de expiração de uma conta de usuário                                            |
| ‑AccountPassword       | Define a senha de uma conta de usuário                                                        |
| ‑ChangePasswordAtLogon | Exige que uma conta de usuário altere senhas na próxima entrada                               |
| ‑Department            | Define o departamento de uma conta de usuário                                                 |
| ‑DisplayName           | Define o nome de exibição de uma conta de usuário                                             |
| ‑HomeDirectory         | Define o local do diretório inicial de uma conta de usuário                                   |
| ‑HomeDrive             | Define as letras da unidade que são mapeadas para o diretório inicial de uma conta de usuário |
| ‑GivenName             | Define o primeiro nome de uma conta de usuário                                                |

Para adicionar uma conta de usuário e definir o atributo Departamento como **TI**, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
New-ADUser "Ana Bowman" -Department IT 
```















