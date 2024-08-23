#### Cenário

<pre style="overflow:scroll;">
+----------------------+           |           +----------------------+
|  [   DNS Primary  ]  |10.0.0.101 | 10.0.0.102|  [  DNS Secondary ]  |
|     srv1.corp.com    +-----------+-----------+    srv2.corp.com      |
|                      |                       |                      |
+----------------------+                       +----------------------+
</pre>

Temos um servidor DNS primário e queremos configurara um servidor secundário.

#### srv1
##### Adicione srv2.corp.com como um servidor de nome na zona corp.com.
```powershell
Add-DnsServerResourceRecord -Name "@" -NS -ZoneName "corp.com" -NameServer "srv2.corp.com" -PassThru
```

#### srv2
##### Adicione a zona corp.com como zona secundária.
```powershell
Add-DnsServerSecondaryZone -Name "corp.com" -ZoneFile "corp.com.dns" -MasterServers 192.168.174.133 -PassThru
```
![[Pasted image 20240822164220.png]]

##### Enumerando as Zonas
```powershell
Get-DnsServerZone
```







































































