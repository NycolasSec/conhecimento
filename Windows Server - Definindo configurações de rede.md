##### Obtendo as interfaces de rede
```powershell
Get-NetIPInterface -AddressFamily IPv4
```
![[Pasted image 20240813152002.png]]

##### Definindo o DHCP como desabilitado
```powershell
Set-NetIPInterface -InterfaceIndex 6 -Dhcp Disabled 
```

##### Definindo IP e o gateway
Definindo o IP para ``10.0.0.101/24`` e gateway para ``10.0.0.1``
```powershell
New-NetIPAddress -InterfaceIndex 11 -AddressFamily IPv4 -IPAddress "10.0.0.101" -PrefixLength 24 -DefaultGateway "10.0.0.1"

#IPAddress         : 10.0.0.101
#InterfaceIndex    : 11
#InterfaceAlias    : Ethernet0
#AddressFamily     : IPv4
#Type              : Unicast
#PrefixLength      : 24
#PrefixOrigin      : Manual
#SuffixOrigin      : Manual
#AddressState      : Preferred
#ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
#PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
#SkipAsSource      : False
#PolicyStore       : ActiveStore
```

##### Definindo o DNS
Definindo o DNS para `10.0.0.10`
```powershell
Set-DnsClientServerAddress -InterfaceIndex 11 -ServerAddresses "10.0.0.10" -PassThru

#InterfaceAlias               Interface Address #ServerAddresses
                             Index     Family
#--------------               --------- ------- ---------
#Ethernet0                           11 IPv4    {10.0.0.10}
#Ethernet0                           11 IPv6    {}
```

##### Verificando as configurações
```powershell
ipconfig /all
```









































