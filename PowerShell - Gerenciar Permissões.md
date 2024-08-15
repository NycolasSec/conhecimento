O módulo **Microsoft.PowerShell.Security** inclui muitos cmdlets internos que você pode usar para gerenciar os recursos básicos de segurança em um computador Windows. Para examinar os cmdlets incluídos neste módulo, insira o seguinte comando:

```powershell
Get-command -module Microsoft.PowerShell.Security
```

Para gerenciar permissões de acesso em um arquivo ou pasta, use os cmdlets a seguir incluídos no módulo **Microsoft.PowerShell.Security**.

|**Cmdlet**|**Descrição**|
|---|---|
|**Get-Acl**|Esse cmdlet obtém os objetos que representam o descritor de segurança de um arquivo ou recurso. O descritor de segurança contém as ACLs (listas de controle de acesso) do recurso. A ACL especifica as permissões que os usuários e os grupos de usuários têm para acessar o recurso.|
|**Set-Acl**|Esse cmdlet altera o descritor de segurança de um item especificado, como um arquivo, uma pasta ou uma chave de registro, para associar os valores em um descritor de segurança fornecido por você.|

## Como recuperar permissões de acesso

O cmdlet **Get-Acl** exibe o descritor de segurança de um objeto. Por exemplo, você pode recuperar o descritor de segurança de uma pasta chamada **C:\Folder1**.

Por padrão, a saída é exibida em um formato de tabela. Se você encaminhar a saída para um formato de lista, pode examinar todas as informações incluídas no descritor de segurança.

```powershell
Get-Acl -Path C:\Folder1|Format-List
```

Usando o comando a seguir, você pode recuperar uma lista mais detalhada da propriedade de acesso com os direitos do sistema de arquivos, o tipo de controle de acesso e as configurações de herança do objeto especificado:
```powershell
(Get-Acl -Path C:\Folder1).Access
```

Você também pode recuperar somente propriedades específicas de Acesso em um formato de tabela, conforme mostrado no exemplo a seguir:
```powershell
(Get-Acl -Path C:\Folder1).Access|Format-Table IdentityReference, FileSystemRights, AccessControlType, IsInherited
```

## Atualização das permissões de acesso a arquivos e pastas

O cmdlet **Set-Acl** é usado para aplicar alterações à ACL em um objeto específico. O processo de modificação de permissões de arquivo ou pasta consiste nas seguintes etapas:

1. Use **Get-Acl** para recuperar as regras de ACL existentes para o objeto.
2. Crie um novo **FileSystemAccessRule** a ser aplicado ao objeto.
3. Adicione a nova regra ao conjunto de permissões de ACL existentes.
4. Use **Set-Acl** para aplicar a nova ACL ao arquivo ou pasta existente.

O exemplo a seguir atribui a permissão **Modificar** a **C:\Folder1** para um usuário local chamado **User1**.

A primeira etapa é declarar uma variável que inclui as regras de ACL existentes para **Folder1**.
```powershell
$ACL = Get-Acl -Path C:\Folder1
```

A segunda etapa é criar uma nova variável FileSystemAccessRule, que determina as especificações de acesso a serem aplicadas:
```powershell
$AccessRule = New-Object System.Security.AccessControl.FileSystemAccessRule("User1","Modify","Allow")
```

A terceira etapa é adicionar a nova regra de acesso às regras de ACL existentes para **Folder1**:
```powershell
$ACL.SetAccessRule($AccessRule)
```

Por fim, você precisa aplicar a nova ACL à **Folder1**:
```powershell
$ACL | Set-Acl -Path C:\Folder1
```

## Como copiar um descritor de segurança para um novo objeto

Se você quiser copiar o descritor de segurança exato para um novo objeto, pode usar uma combinação dos comandos **Get-Acl** e **Set-Acl** da seguinte maneira:

```powershell
Get-Acl -Path C:\Folder1|Set-ACL -Path C:\Folder2
```

Esses comandos copiam os valores do descritor de segurança de **C:\Folder1** para o descritor de segurança de **Folder2**. Quando os comandos são concluídos, os descritores de segurança para ambas as pastas são idênticos.




















