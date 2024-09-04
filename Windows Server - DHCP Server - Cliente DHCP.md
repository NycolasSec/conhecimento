#### Interfaces de rede
```powershell
Get-NetIPInterface -AddressFamily IPv4
```

#### Definir DHCP
```powershell
Set-NetIPInterface -InterfaceIndex 6 -Dhcp Enabled
```
