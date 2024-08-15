```powershell
New-NetFirewallRule `
-Name 'ICMPv4' `
-DisplayName 'ICMPv4' `
-Description 'Allow ICMPv4' `
-Profile Any `
-Direction Inbound `
-Action Allow `
-Protocol ICMPv4 `
-Program Any `
-LocalAddress Any `
-RemoteAddress Any
```