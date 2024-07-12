[[Redes - SNMP]]
[[Linux - SNMP]]

---

## Footprinting the Service
Podemos usar ferramentas como :

**`snmpwalk`** : Consulta os OIDS com suas informações

**`onesixtyone`** : Pode ser usado para força bruta do nome das strings da comunidade, já que elas podem ser nomeadas arbitrariamente pelo administrador.

Já que essas strings da comunidade podem ser vinculadas a qualquer fonte, identificar as strings da comunidade existentes pode levar algum tempo.

### snmpwalk
```shell-session
snmpwalk -v2c -c public 10.129.14.128
```
```output
iso.3.6.1.2.1.1.1.0 = STRING: "Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (5134) 0:00:51.34
iso.3.6.1.2.1.1.4.0 = STRING: "mrb3n@inlanefreight.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "htb"
iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1

...SNIP...

iso.3.6.1.2.1.25.6.3.1.2.1232 = STRING: "printer-driver-sag-gdi_0.1-7_all"
iso.3.6.1.2.1.25.6.3.1.2.1233 = STRING: "printer-driver-splix_2.0.0+svn315-7fakesync1build1_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1234 = STRING: "procps_2:3.3.16-1ubuntu2.3_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1235 = STRING: "proftpd-basic_1.3.6c-2_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1236 = STRING: "proftpd-doc_1.3.6c-2_all"
```

No caso de uma configuração incorreta, obteríamos aproximadamente os mesmos resultados `snmpwalk`mostrados acima. Uma vez que conhecemos a string da comunidade e o serviço SNMP que não requer autenticação (versões 1, 2c), podemos consultar informações internas do sistema como no exemplo anterior.

Aqui reconhecemos alguns pacotes Python que foram instalados no sistema. Se não soubermos a string community, podemos usar `onesixtyone`e `SecLists` wordlists para identificar essas strings community.

### onesixtyone
```bash
sudo apt install onesixtyone
onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt 10.129.14.128

#Scanning 1 hosts, 3220 communities
#10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```

Quando certas strings de comunidade são vinculadas a endereços IP específicos, elas são nomeadas com o nome do host do host, se imaginarmos uma rede extensa usando SNMP, os rótulos, nesse caso, terão algum padrão para eles. Assim podemos criar wordlists personalizadas com  [crunch](https://secf00tprint.github.io/blog/passwords/crunch/advanced/en)

Ao conhecermos uma sequência de caracteres da comunidade podemos usá-la com [o braa](https://github.com/mteg/braa) para aplicar força bruta aos OIDs individuais e enumerar as informações por trás deles.

### braa
**`Syntax`** : `braa <community string>@<IP>:.1.3.6.*   # Syntax`

```bash
sudo apt install braa
braa public@10.129.14.128:.1.3.6.*
```
```output
10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.6.0:US
10.129.14.128:20ms:.1.3.6.1.2.1.1.7.0:78
...SNIP...
```


---

## Referências
**`HTB`** - https://academy.hackthebox.com/module/112/section/1075
**`Manpage`** - http://www.net-snmp.org/docs/man/snmpd.conf.html