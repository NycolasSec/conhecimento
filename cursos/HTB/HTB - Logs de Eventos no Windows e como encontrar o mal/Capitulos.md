## Introdução
[[Cyber - Logs de eventos do Windows]]
[[Cyber - Lista de logs de eventos úteis do Windows]]
[[Cyber - Analisando o Mal com Sysmon e Logs de Eventos]]

## Fontes adicionais de telemetria
[[Cyber - Rastreamento de eventos para Windows (ETW)]]
[[Cyber - Aproveitando o ETW]]

## Analisando logs de eventos do Windows em massa
[[Cyber - Evento Get-Win]]

![[Pasted image 20240905160859.png]]


![[Pasted image 20240903160539.png]]

#### Contando linhas de um comando
```powershell
PS C:\Users\nycol\OneDrive\Documentos\share> Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11} | Measure-Object -Line
```
![[Pasted image 20240923155210.png]]


#### Procurando por DLLs não assinadas - analisando logs de eventos:

```powershell
Get-WinEvent -FilterHashtable @{Path="./*.evetx";ID=7} | Where-Object {$_.Properties[12].Value -eq "false"} | Select-Object -Property *
```


####
```powershell
Get-WinEvent -FilterHashtable @{Path=".\PowershellExec.evtx";ID=7} | Where-Object {$_.Properties[10].Value -eq "clrjit.dll"} | Select-Object -Property Message | Format-Table -AutoSize -Wrap
```

#### 
```powershell
Get-WinEvent -FilterHashtable @{Path=".\PowershellExec.evtx";ID=7} | Where-Object {$_.Properties[3].Value -eq "3776"} | Select-Object -Index 0 | Format-Table -AutoSize -Wrap
```
![[Pasted image 20240905142501.png]]

#### 8: CreateRemoteThread
```powershell
 Get-WinEvent -FilterHashtable @{Path=".\PowershellExec.evtx";ID=8} | Where-Object {$_.Properties.Value -like "*"} | Select-Object -Property Message | Format-Table -AutoSize -Wrap
```
```
Message
---
CreateRemoteThread detected:
RuleName: -
UtcTime: 2022-04-28 02:00:13.593
SourceProcessGuid: {67e39d39-f0f6-6269-b601-000000000300}
SourceProcessId: 8364
SourceImage: C:\Windows\System32\rundll32.exe
TargetProcessGuid: {67e39d39-f4cc-6269-3203-000000000300}
TargetProcessId: 3776
TargetImage: C:\Program Files\WindowsApps\Microsoft.WindowsCalculator_10.1906.55.0_x64__8wekyb3d8bbwe\Calculator.exe
NewThreadId: 4816
StartAddress: 0x00000253B2180000
StartModule: -
StartFunction: -
SourceUser: DESKTOP-R4PEEIF\waldo
TargetUser: DESKTOP-R4PEEIF\waldo
```

#### Detectando Dumping de Credenciais
```powershell
Get-WinEvent -FilterHashtable @{Path=".\*.evtx";ID=10} | Where-Object {$_.Properties[11].Value -like "*waldo" -and -not ($_.Properties[12].Value -like "*waldo")} | Select-Object -Property Message | Format-Table -AutoSize -Wrap
```