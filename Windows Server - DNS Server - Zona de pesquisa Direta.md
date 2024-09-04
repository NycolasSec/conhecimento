## Instalação
```powershell
Install-WindowsFeature DNS -IncludeManagementTools
```
![[Pasted image 20240813164917.png]]

## Zona de pesquisa direta

##### Cria uma zona
```powershell
Add-DnsServerPrimaryZone -Name "srv.corp" -ZoneFile "srv.corp.dns" -DynamicUpdate None -PassThru
```
![[Pasted image 20240813165245.png]]

##### Verifica as zonas de pesquisa criadas
```powershell
Get-DnsServerZone
```
![[Pasted image 20240813165624.png]]

##### Deleta uma zona de pesquisa
```powershell
Remove-DnsServerZone "srv.world" -PassThru
```
![[Pasted image 20240813165806.png]]






















