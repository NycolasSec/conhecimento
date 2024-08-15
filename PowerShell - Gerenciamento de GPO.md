Você pode usar o Windows PowerShell para automatizar o gerenciamento da maioria das tarefas que envolvem gpos (objetos Política de Grupo), incluindo criação, exclusão, backup, geração de relatório e importação de GPOs.

Você também pode associar GPOs a OUs de Active Directory Domain Services (AD DS), incluindo configurar herança e permissões de GPO. Os cmdlets de gerenciamento de Política de Grupo precisam do RSAT (Remote Server Administration Tools) instalado.

Cmdlets de gerenciamento de Política de Grupo fazem parte do módulo **GroupPolicy** para Windows PowerShell. Os nomes do cmdlet incluem o prefixo "GP" e a maioria tem "GPO" como substantivo.

A tabela a seguir lista cmdlets comumente usados para gerenciar GPOs.

|**Cmdlet**|**Descrição**|
|---|---|
|**New-GPO**|Cria um novo GPO|
|**Get-GPO**|Recupera um GPO|
|**Set-GPO**|Modifica propriedades de um GPO|
|**Remove-GPO**|Exclui um GPO|
|**Rename-GPO**|Renomeia um GPO|
|**Backup-GPO**|Faz backup de um ou mais GPOs em um domínio|
|**Copy-GPO**|Copia um GPO de um domínio para outro|
|**Restore-GPO**|Restaura um GPO usando arquivos de backup|
|**New-GPLink**|Vincula um GPO a um contêiner do AD DS|
|**Import-GPO**|Importa as configurações de GPO de um backup de um GPO|
|**Set-GPRegistryValue**|Configura uma ou mais configurações de política baseadas em registro em um GPO|
## Criar um novo GPO
**New-GPO** requer apenas o parâmetro _-Name_, que deve ser exclusivo no domínio no qual você cria o GPO. Por padrão, o GPO é criado no domínio do usuário que está executando o comando. **New-GPO**, além disso, não vincula o GPO criado a um contêiner do AD DS. Para vincular um GPO a um contêiner, use o cmdlet **New-GPLink**.

Cria um novo GPO usando um GPO inicial.
```powershell
New-GPO -Name "IT Team GPO" -StarterGPOName "IT Starter GPO"
```

Vincula o novo GPO a uma unidade organizacional do AD DS
```powershell
New-GPLink -Name "IT Team GPO" -Target "OU=IT,DC=adatum,DC=com"
```

