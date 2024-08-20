##### Adicionando registro CNAME
```powershell
Add-DnsServerResourceRecordCName -Name "ftpadm" -HostNameAlias "ftp.corp.com" -ZoneName "corp.com" -PassThru
```
![[Pasted image 20240820155416.png]]

##### Enumerando os registros
```powershell
Get-DnsServerResourceRecord -ZoneName "corp.com" | Format-Table -AutoSize -Wrap
```
![[Pasted image 20240820155459.png]]

##### Testando a resolução
```powershell
Resolve-DnsName ftpadm.corp.com -Server 127.0.0.1
```
![[Pasted image 20240820155722.png]]

##### Removendo o registro
```powershell
Remove-DnsServerResourceRecord -ZoneName "corp.com" -RRType "CNAME" -Name "ftpadm" -PassThru
```























