##  Questions

###### How many Event logs are there with Event ID 11?
```powershell
Get-WinEvent -FilterHashtable @{Path=".\Microsoft-Windows-Sysmon-Operational.evtx";ID=11} | Measure-Object -Line
```
![[Pasted image 20240930105124.png]]

**Answer:** 56

###### Whenever a process is created in memory, an event with Event ID 1 is recorded with details such as command line, hashes, process path, parent process path, etc. What is the malicious process that infected the victim's system?