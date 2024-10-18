Embora o Kea seja um servidor DHCP bem completo, podemos fazer uma configuração básica para ver seu funcionamento.

---
---
#### Tempo de uso
---
Devemos definir por quanto tempo um endereço fornecido pelo servidor será válido. E quando ele deve ser renovado. Esse tempo deve ser passado em segundos.

```conf
"valid-lifetime":4000,
"rebind-timer":2000,
"renew-timer":1000,
```
**`valid-lifetime`**: Define por quanto tempo os endereços (leases) fornecidos pelo servidor são válidos.
**`renew-timer`**: Define quando os clientes iniciam o processo de renovação.
**`rebind-timer`**: Define quando os clientes iniciam o processo de vinculação.

Ambos `renew-timer` e `rebind-timer` são opcionais.

---
---
#### Interfaces config
---
Especifica as interfaces de rede nas quais o servidor deve escutar mensagens DHCP.

```conf
"interfaces-config":{
        "interfaces":["eth0", "eth1"]
    },
```

---
---
#### Lease database
---
Aqui definimos o banco de dados de `lease`, o local onde o servidor armazena suas informações de lease.

```conf
"lease-database":{
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/dhcp4.leases"
    },
```
Este exemplo em particular diz ao servidor para usar ``memfile``, que é o ``backend`` de banco de dados mais simples e rápido. Este é um exemplo de configuração muito simples; geralmente, a configuração do banco de dados de lease é mais extensa e contém parâmetros adicionais.

---
---
#### Subnet4
---
Ela define todas as sub-redes das quais o servidor deve receber solicitações. Sendo uma lista começa e termina com colchetes e separa seu elementos por vírgula. Cada item dessa lista deve ter pelo menos 2 atributos : 
**`subnet`**: Define todas as sub-redes.
**`pools`**: É uma lista de pools alocados dinamicamente pelo servidor DHCP.

```conf
"subnet4": [
    {
	    "id": 1,
        "pools": [{ "pool":  "192.0.2.1 - 192.0.2.200" }],
        "subnet": "192.0.2.0/24"
    },

    {
        "id": 2,
        "pools": [{ "pool": "192.0.3.100 - 192.0.3.200"} ],
        "subnet": "192.0.3.0/24"
    },

    {
        "id": 3,
        "pools": [{ "pool": "192.0.4.1 - 192.0.4.254" }],
        "subnet": "192.0.4.0/24"
    }
],
```

---
---
#### Option Data
---
São opções que podemos colocar em cada item da subnet, como o gateway, e servidor DNS. Sendo uma lista, separamos seu items por vírgula. 

```conf
{
    "id": 1,
    "subnet": "10.0.0.0/24",
    "pools": [{"pool": "10.0.0.100 - 10.0.0.200"}],
    
    "option-data": [
        {
            "name": "domain-name-servers",
            "data": "10.0.0.2"
        },
        {
            "name": "routers",
            "data": "10.0.0.1"
        }
    ]
}
```
**`name`**: Nome da opção a configurar.
**`data`**: Qual o valor da opção definida.

---
---
#### Exemplo
```conf
{
"Dhcp4":{
	"valid-lifetime":4000,
    "renew-timer":1000,
    "rebind-timer":2000,


    "interfaces-config":{
        "interfaces":["eth0"]
    },


    "lease-database":{
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/dhcp4.leases"
    },


    "subnet4":[
        {
            "id": 1,
            "subnet": "10.0.0.0/24",
            "pools": [{"pool": "10.0.0.100 - 10.0.0.200"}],
            
            "option-data": [
                {
                    "name": "domain-name-servers",
                    "data": "10.0.0.2"
                },
                {
                    "name": "routers",
                    "data": "10.0.0.1"
                }
            ]
        }
    ],

}
}
```

















































































