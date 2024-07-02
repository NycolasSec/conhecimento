Para criarmos os usuários, temos de saber o caminho de onde eles serão criados.

### Caminho de uma UO

Selecione a sua UO e vá e propriedades

![[Pasted image 20240702180915.png]]

Então vá em **Extensions > Attribute Editor > distinguishedName**

No nosso caso :OU=Users,OU=Vendas,DC=empresa,DC=local

![[Pasted image 20240702181007.png]]
### Criação do usuário

```powershell
New-ADUser -name 'Atylas Ramos' -SamAccountName atylasRamos -UserPrincipalName atylasramos@empresa.local -Path "OU=Users,OU=Vendas,DC=empresa,DC=local" -AccountPassword (ConvertTo-SecureString -AsPlainText 'Senha123' -force) -Enable
```
