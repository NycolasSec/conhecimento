## Instalação
```powershell
Install-WindowsFeature DNS -IncludeManagementTools
```
![[Pasted image 20240813164917.png]]

## Zona de pesquisa direta

##### Criando a zona
```powershell
Add-DnsServerPrimaryZone -NetworkID 192.168.174.0/24 -ZoneFile "174.168.192.in-addr.arpa.dns" -DynamicUpdate None -PassThru
```
![[Pasted image 20240813170521.png]]

##### Verifica as zonas criadas
```powershell
Get-DnsServerZone
```
![[Pasted image 20240813170602.png]]

##### Deleta uma zona
```powershell
Remove-DnsServerZone "174.168.192.in-addr.arpa"
```
