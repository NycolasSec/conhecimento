##### Descobrindo o nome do recurso
```powershell
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
#ou
(Get-WindowsCapability -Online).Name | Select-String -Pattern 'OpenSSH'
```
![[Pasted image 20240813135757.png]]


##### Instalando OpenSSH Server
```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```
![[Pasted image 20240813141127.png]]


##### Iniciando o serviço
```powershell
Start-Service -Name "sshd"
```


##### Inicialização automática
```powershell
Set-Service -Name "sshd" -StartupType Automatic
```


##### Verificando as configurações
```powershell
Get-Service -Name "sshd" | Select-Object *
```
![[Pasted image 20240813141630.png]]


##### Adicionando regra ao firewall
Se o firewall estiver rodando, habilite a porta 22/TCP
```powershell
New-NetFirewallRule -Name "SSH" `
-DisplayName "SSH" `
-Description "Allow SSH" `
-Profile Any `
-Direction Inbound `
-Action Allow `
-Protocol TCP `
-Program Any `
-LocalAddress Any `
-RemoteAddress Any `
-LocalPort 22 `
-RemotePort Any
```
![[Pasted image 20240813141933.png]]

















