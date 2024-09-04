#### Install DHCP
```powershell
Install-WindowsFeature DHCP -IncludeManagementTools
```

#### Adicionar um grupo de segurança
```powershell
Add-DhcpServerSecurityGroup -ComputerName "ns1.corp.com"
```

##### Caso esteja em um domínio do AD
**Obtenha a autorização do AD com o seguinte comando.**
```powershell
Add-DhcpServerInDC -DnsName "ns1.corp.com" -IPAddress 10.0.0.11
```

**Para criar os grupos de gerenciamento do DHCP execute o seguinte comando:**
```powershell
Add-DhcpServerSecurityGroup -Computer "DhcpServerName"
```

#### Criação do escopo para IPv4
```powershell
Add-DhcpServerv4Scope -Name "Internal Network" `
-StartRange 10.0.0.100 `
-EndRange 10.0.0.254 `
-SubNetMask 255.255.255.0 `
-LeaseDuration 8.00:00:00 `
-State Active `
-Passhru
```

#### Domain name, DNS Server, Gateway(Router)
```powershell
Set-DhcpServerv4OptionValue -DnsDomain "corp.com" `
-DnsServer "10.0.0.11" `
-Router "10.0.0.2" `
-ScopeId "10.0.0.0" `
-PassThru
```

#### Reiniciando o Servidor
```powershell
Restart-Service DHCPServer
```