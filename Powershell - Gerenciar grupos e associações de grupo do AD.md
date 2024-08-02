O gerenciamento de grupos do Active Directory está intimamente relacionado ao gerenciamento de usuários. Você pode usar o módulo do Active Directory para cmdlets do Windows PowerShell para criar e excluir grupos e modificar as propriedades do grupo.

## Gerenciando grupos
Os cmdlets para modificar grupos têm o texto "group" no nome. Os cmdlets que modificam a associação ao grupo adicionando membros a um grupo, por exemplo, têm o texto "groupmember" no nome.

Os cmdlets que modificam os grupos dos quais um usuário, computador ou outro objeto do Active Directory é membro têm o texto "principalgroupmembership" no nome.

A tabela a seguir lista alguns cmdlets comumente usados para gerenciar grupos.

|Cmdlet|Descrição|
|---|---|
|**New-ADGroup**|Cria um novo grupo|
|**Set-ADGroup**|Modifica as propriedades de um grupo|
|**Get-ADGroup**|Exibe as propriedades de um grupo|
|**Remove-ADGroup**|Exclui um grupo|
|**Add-ADGroupMember**|Adiciona membros a um grupo|
|**Get-ADGroupMember**|Exibe os membros de um grupo|
|**Remove-ADGroupMember**|Remove membros de um grupo|
|**Add-ADPrincipalGroupMembership**|Adiciona a associação de um objeto ao grupo|
|**Get-ADPrincipalGroupMembership**|Exibe a associação de um objeto ao grupo|
|**Remove-ADPrincipalGroupMembership**|Remove a associação de um objeto do grupo|

## Criando novos grupos
Você pode usar o cmdlet **New-ADGroup** para criar grupos. Ao criar grupos usando o cmdlet **New-ADGroup**, você deve usar o parâmetro _-GroupScope_, além do nome do grupo. Esse parâmetro é o único necessário.

A tabela a seguir lista parâmetros comuns para **New-ADGroup**.

|Parâmetro|Descrição|
|---|---|
|‑Name|Define o nome de um grupo|
|‑GroupScope|Define o escopo de um grupo como **DomainLocal**, **Global** ou **Universal**; você deve fornecer esse parâmetro|
|‑DisplayName|Define o nome de exibição LDAP (protocolo LDAP) para um objeto|
|‑GroupCategory|Define se um grupo é um grupo de segurança ou um grupo de distribuição; se você não especificar nenhum, um grupo de segurança será criado|
|‑ManagedBy|Define um usuário ou grupo que pode gerenciar um grupo|
|‑Path|Define a UO ou o contêiner no qual um grupo é criado|
|‑SamAccountName|Define um nome compatível com versões anteriores de sistemas operacionais mais antigos|

Para criar um novo grupo chamado **FileServerAdmins**:
```powershell
New-ADGroup -Name FileServerAdmins -GroupScope Global
```

## Gerenciando a associação ao grupo
Como mencionado anteriormente, você pode usar os cmdlets **`*-ADGroupMember`** ou **`*-ADPrincipalGroupMembership`** para gerenciar o grupo de duas maneiras diferentes.

A diferença entre os dois é uma questão de se concentrar em um objeto e modificar os grupos aos quais ele pertence ou concentrar-se no grupo e modificar os membros que pertencem a ele.

Além disso, você pode escolher qual conjunto usar com base na decisão de _canalizar_ uma lista de membros para o comando ou fornecer uma lista de membros.

os cmdlets **`*-ADGroupMember`** modificam a associação a um grupo. Por exemplo:
	- Você pode adicionar ou remover membros de um grupo.
	- Você pode passar uma lista de grupos para esses cmdlets.
	- Você não pode _canalizar_ uma lista de membros para esses cmdlets.

os cmdlets **`*-ADPrincipalGroupMembership`** modificam a associação de um objeto, como um usuário, ao grupo. Por exemplo:
	- Você pode adicionar uma conta de usuário a um grupo, como membro.
	- Você não pode fornecer uma lista de grupos para esses cmdlets.
	- Você pode _canalizar_ uma lista de membros para esses cmdlets.























