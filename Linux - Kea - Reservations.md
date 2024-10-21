Podemos reservar endereços IP, para alguns hosts em específico, para que eles tenha um endereço definido por padrão. Assim podemos definir um endereço fixo para algum servidor sem colocar de forma estática no servidor.

Essa configuração fica no `"subnet4"` dentro do escopo de alguma `pool`.

---
---
#### MAC address reservation
---
Podemos reservar de acordo com o ``MAC Address`` do host.

**`hw-address`**: MAC address do host.
**`ip-address`**: Endereço a ser reservado.
**`hostname`**:   Hostname do host.

```conf
"subnet4": [
	{
		"id": 1,
        "pools": [{"pool": "10.0.0.100 - 10.0.0.200"}],
        "subnet": "10.0.0.0/24",
        "reservations":[
	        {
		        "hw-address": "00:15:5D:00:0B:45",
                "ip-address": "10.0.0.5",
                "hostname":   "web",
            },
		],
	},
],
```

---
---
#### Client-ID reservation
---
Também podemos reservar endereços de acordo com seu ``Client-ID
```conf
"subnet4": [
	{
		"id": 1,
        "pools": [{"pool": "10.0.0.100 - 10.0.0.200"}],
        "subnet": "10.0.0.0/24",
        "reservations":[
	        {
		        "client-id": "00:15:5D:00:0B:45:00",
                "ip-address": "10.0.0.5",
                "hostname":   "web",
            },
		],
	},
],
```

---
---
#### Exemplo
```conf
{
"Dhcp4": {
	"valid-lifetime": 4000,
    "rebind-timer": 2000,
    "renew-timer": 1000,


    "interfaces-config":{
	    "interfaces":["eth1"]
    },


    "lease-database":{
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/dhcp4-leases.csv",
        "lfc-interval": 3600,
    },


    "option-data": [
        {
            "name": "domain-name-servers",
            "data": "8.8.8.8, 1.1.1.1"
        },
        {
            "name": "domain-search",
            "data": "10.0.0.1"
        },
        {
            "name": "routers",
            "data": "10.0.0.1"
        },
    ],


    "subnet4": [
        {
            "id": 1,
            "pools": [{"pool": "10.0.0.100 - 10.0.0.200"}],
            "subnet": "10.0.0.0/24",
            "reservations":[
                    {
                        "hw-address": "00:15:5D:00:0B:45",
                        "ip-address": "10.0.0.5",
                        "hostname":   "web",
                    },
            ],
        },
    ],
}
}
```




















































































































