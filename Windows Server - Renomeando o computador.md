##### Renomear o computador
```powershell
Rename-Computer -NewName RX-7 -Force -PassThru 

HasSucceeded OldComputerName           NewComputerName
------------ ---------------           ---------------
True         WIN-PA24S3GQ6G3           RX-7
WARNING: The changes will take effect after you restart the computer WIN-PA24S3GQ6G3
```

##### Mudar o sufixo de DNS Primário
```powershell
Set-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\" –Name "NV Domain" –Value "srv.world" -PassThru 

NV Domain    : srv.world
PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip
PSChildName  : Parameters
PSDrive      : HKLM
PSProvider   : Microsoft.PowerShell.Core\Registry
```

##### Reiniciar o computador
```powershell
Restart-Computer -Force
```




