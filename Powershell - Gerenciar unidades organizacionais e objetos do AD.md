O Windows PowerShell fornece cmdlets que você pode usar para criar, modificar e excluir as UOs (Unidades Organizacionais) do AD DS (Active Directory Domain Services). Os cmdlets de gerenciamento de UO têm o texto "organizationalunit" no nome.

A tabela a seguir lista os cmdlets que podem ser usados para gerenciar UOs.

| Cmdlet                          | Descrição                       |
| ------------------------------- | ------------------------------- |
| **New-ADOrganizationalUnit**    | Cria uma UO                     |
| **Set-ADOrganizationalUnit**    | Modifica propriedades de uma UO |
| **Get-ADOrganizationalUnit**    | Exibe as propriedades de uma UO |
| **Remove-ADOrganizationalUnit** | Exclui uma UO                   |

## Criando novas UOs
Você pode usar o cmdlet **New-ADOrganizationalUnit** para criar uma nova UO para representar departamentos ou locais físicos na sua organização.

A tabela a seguir lista parâmetros comuns para o cmdlet **New-ADOrganizationalUnit**.

| Parâmetro                        | Descrição                                                                |
| -------------------------------- | ------------------------------------------------------------------------ |
| ‑Name                            | Define o nome de uma nova UO                                             |
| ‑Path                            | Define o local de uma nova UO                                            |
| ‑ProtectedFromAccidentalDeletion | Impede que alguém exclua acidentalmente uma UO; o valor padrão é `$true` |

O exemplo a seguir é um comando para criar uma nova UO:
```powershell
New-ADOrganizationalUnit -Name Sales -Path "ou=marketing,dc=adatum,dc=com" -ProtectedFromAccidentalDeletion $true 

```

## Cmdlets de objeto do Active Directory
Às vezes, você precisará gerenciar objetos do Active Directory que não têm seus próprios cmdlets de gerenciamento, como contatos. Talvez você também queira gerenciar vários tipos de objeto em uma única operação, como mover usuários e computadores de uma UO para outra UO.

 O módulo do Active Directory fornece cmdlets que permitem criar, excluir e modificar esses objetos e as respectivas propriedades. Como esses cmdlets podem gerenciar todos os objetos, eles repetem algumas funcionalidades dos cmdlets para gerenciar usuários, computadores, grupos e UOs.

Os cmdlets **`*-ADObject`** às vezes são executados mais rápido do que cmdlets específicos para o tipo de objeto. Isso ocorre porque esses cmdlets adicionam o custo de filtrar o conjunto de objetos aplicáveis às operações. Os cmdlets para alterar objetos genéricos do Active Directory têm o texto "Object" na parte substantiva do nome.

A tabela a seguir lista cmdlets que você pode usar para gerenciar objetos do Active Directory.

| Cmdlet               | Descrição                                                                      |
| -------------------- | ------------------------------------------------------------------------------ |
| **New-ADObject**     | Cria um novo objeto do Active Directory                                        |
| **Set-ADObject**     | Modifica propriedades de um objeto do Active Directory                         |
| **Get-ADObject**     | Exibe propriedades de um objeto do Active Directory                            |
| **Remove-ADObject**  | Exclui um objeto do Active Directory                                           |
| **Rename-ADObject**  | Renomeia um objeto do Active Directory                                         |
| **Restore-ADObject** | Restaura um objeto do Active Directory excluído da Lixeira do Active Directory |
| **Move-ADObject**    | Move um objeto do Active Directory de um contêiner para outro contêiner        |
| **Sync-ADObject**    | Sincroniza um objeto do Active Directory entre dois controladores de domínio   |

## Criando um novo objeto do Active Directory
Você pode usar o cmdlet **New-ADObject** para criar objetos. Ao usar **New-ADObject**, você deve especificar o nome e o tipo de objeto.

A tabela a seguir lista parâmetros comuns para **New-ADObject**.

|Parâmetro|Descrição|
|---|---|
|‑Name|Define o nome de um objeto|
|‑Type|Define o tipo LDAP de um objeto|
|‑OtherAttributes|Define propriedades de um objeto que não são acessíveis de outros parâmetros|
|‑Path|Define o contêiner no qual um objeto é criado|

O seguinte comando cria um novo objeto de contato:
```powershell
New-ADObject -Name "AnaBowmancontact" -Type contact 
```