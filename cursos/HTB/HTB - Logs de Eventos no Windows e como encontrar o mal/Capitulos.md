## Introdução
[[Cyber - Logs de eventos do Windows]]
[[Cyber - Lista de logs de eventos úteis do Windows]]
[[Cyber - Analisando o Mal com Sysmon e Logs de Eventos]]

## Fontes adicionais de telemetria
[[Cyber - Rastreamento de eventos para Windows (ETW)]]
[[Cyber - Aproveitando o ETW]]

## Analisando logs de eventos do Windows em massa
[[Cyber - Evento Get-Win]]


![[Pasted image 20240903160539.png]]

#### Procurando por DLLs não assinadas - analisando logs de eventos:

```powershell
Get-WinEvent -FilterHashtable @{Path="./*.evetx";ID=7} | Where-Object {$_.Properties[12].Value -eq "false"} | Select-Object -Property *
```