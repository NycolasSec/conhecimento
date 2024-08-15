##### Adicionando usuário a um grupo
```powershell
Add-LocalGroupMember -Group "Administrators" -Member "Serverworld"
```

##### Verificando
```powershell
Get-LocalUser -Name Serverworld 

Name        Enabled Description
----        ------- -----------
Serverworld True    Administrator of this Computer
```

##### Removendo um usuário
```powershell
Remove-LocalUser -Name "Serverworld"
```

