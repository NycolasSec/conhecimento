##### Adicionando o registro
```powershell
Add-DnsServerResourceRecordMX -Name "mail" -MailExchange "mail.corp.com" -ZoneName "corp.com" -Preference 10 -TimeToLive 01:00:00 -PassThru
```

##### Enumerando os registros
```powershell
Get-DnsServerResourceRecord -ZoneName "corp.com" | Format-Table -AutoSize -Wrap
```

##### Removendo registros MX
```powershell
Remove-DnsServerResourceRecord -ZoneName "corp.com" -RRType "MX" -Name "mail" -PassThru
```