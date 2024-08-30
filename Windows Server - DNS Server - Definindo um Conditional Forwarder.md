Nessa configuração, é possível transferir consultas específicas de um domínio para um servidor específico que você definir.

##### Adicionando uma Zona de Conditional Forwarder
```powershell
Add-DnsServerConditionalForwarderZone -Name "corp.education" -MasterServers 192.168.206.21 -PassThru
```
![[Pasted image 20240827173444.png]]