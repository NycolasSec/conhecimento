Um Forwarder transfere consultas DNS para outros servidores

##### Adicionando um Forwarder
```powershell
Add-DnsServerForwarder -IPAddress 10.0.0.10 -PassThru
```

##### Enumerando Forwarders
```powershell
Get-DnsServerForwarder
```
![[Pasted image 20240827172745.png]]






