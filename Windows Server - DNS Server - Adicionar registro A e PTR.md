##### Adicionando um registro
```powershell
Add-DnsServerResourceRecordA -Name "ftp" -ZoneName "corp.com" -IPv4Address "192.168.174.2" -TimeToLive 01:00:00 -CreatePtr -PassThru
```
Caso não especifiquemos o TTL, o padrão é 1 hora.

##### Enumerando os registros
```powershell
Get-DnsServerResourceRecord -ZoneName "corp.com" | Format-Table -AutoSize -Wrap
```
![[Pasted image 20240815143847.png]]

##### Removendo registros
```powershell
Remove-DnsServerResourceRecord -ZoneName "corp.com" -RRType "A" -Name "ftp" -RecordData "192.168.174.1" -PassThru
```

##### Testando a resolução
```powershell
nslookup 192.168.174.2 127.0.0.1
```
Testa a resolução por endereço.

```powershell
nslookup ftp.corp.com 127.0.0.1
```
Testa a resolução por domínio.

```powershell
Resolve-DnsName ftp.corp.com -Type A -Server 127.0.0.1
```
Testa a resolução por registro A.

```powershell
Resolve-DnsName 10.0.0.102 -Type PTR -Server 127.0.0.1
```
Testa a resolução por registro PTR.