[[Redes - SNMP]]
[[Cyber - SNMP]]

---

## Configuração padrão
A configuração padrão do daemon SNMP define as configurações básicas para o serviço, que incluem endereços IP, portas, MIB, OIDs, autenticação e strings de comunidade.

### Configuração padrão do daemon SNMP
```bash
cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'
```
```output
sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>
sysServices    72
master  agentx
agentaddress  127.0.0.1,[::1]
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly
rouser authPrivUser authpriv -V systemonly
```
Todas as configurações que podem ser feitas para o daemon SNMP são definidas e descritas na [manpage](http://www.net-snmp.org/docs/man/snmpd.conf.html) .

## Configurações perigosas
Algumas configurações perigosas que o administrador pode fazer com SNMP são:

| **Configurações**                                | **Descrição**                                                                                   |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------- |
| `rwuser noauth`                                  | Fornece acesso à árvore OID completa sem autenticação.                                          |
| `rwcommunity <community string> <IPv4 address>`  | Fornece acesso à árvore OID completa, independentemente de onde as solicitações foram enviadas. |
| `rwcommunity6 <community string> <IPv6 address>` | Mesmo acesso com `rwcommunity`a diferença de usar IPv6.                                         |

---

## Referências
**`HTB`** - https://academy.hackthebox.com/module/112/section/1075
**`Manpage`** - http://www.net-snmp.org/docs/man/snmpd.conf.html