#### Cenário

<pre style="overflow-x:scroll;">
+----------------------+           |           +----------------------+
|  [   DNS Server   ]  |10.0.0.101 | 10.0.0.102|  [   DNS Server  ]   |
|     srv1.corp.com    +-----------+-----------+     srv2.corp.com    |
|                      |                       |      (Stub Zone)     |
+----------------------+                       +----------------------+
</pre>

#### srv1
##### Adicione srv2.corp.com como um servidor de nome na zona corp.com.
```powershell
Add-DnsServerResourceRecord -Name "@" -NS -ZoneName "corp.com" -NameServer "srv2.corp.com" -PassThru 
```
Adicionamos `srv2` como um nome de servidor.

#### srv2
##### Adicione a zona corp.com como zona stub.
```powershell
Add-DnsServerStubZone -Name "corp.com" -MasterServers "10.0.0.101" -ZoneFile "corp.com.dns" -PassThru 
```

##### Enumere as zonas
```powershell
Get-DnsServerZone
```

##### Enumere os registros da zona
```powershell
Get-DnsServerResourceRecord -ZoneName "corp.com" | Format-Table -AutoSize -Wrap
```