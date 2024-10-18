## Instalação
Há várias maneiras de instalar o ``kea``, um deles é adicionando o repositório do ``kea``.

#### Adicionando o repositório
Deve ser verificado se houve alguma mudança na forma de fazer essa tarefa.
```shell
curl -1sLf \
  'https://dl.cloudsmith.io/public/isc/kea-2-6/setup.rpm.sh' \
  | sudo -E bash
```

#### Dependências
Caso necessite do pacote `liblog4cplus-2.0.so.3()(64bit`
```shell
wget https://dl.fedoraproject.org/pub/epel/9/Everything/x86_64/Packages/l/log4cplus-2.0.5-15.el9.x86_64.rpm

dnf localinstall ./log4cplus-2.0.5-15.el9.x86_64.rpm
```

#### Instalando
Podemos instalar vários pacotes, é recomendável que olhe a documentação  para ter um escopo geral.

- `isc-kea-dhcp4` — Kea DHCPv4 server package
- `isc-kea-dhcp6` — Kea DHCPv6 server package
- `isc-kea-dhcp-ddns` — Kea DHCP DDNS server
- `isc-kea-ctrl-agent` — Kea Control Agent for remote configuration
- `isc-kea-admin` — Kea database administration tools
- `isc-kea-hooks` — Kea open source DHCP hooks

Instala os pacotes para o uso do servidor DHCP para IPv4.
```shell
dnf install isc-kea-dhcp4
```

#### Hierarquia da instalação
Esta é a hierarquia da instalação inicial do Kea.
- `etc/kea/` — configuration files.
- `include/` — C++ development header files.
- `lib/` — libraries.
- `lib/kea/hooks` — additional hook libraries.
- `sbin/` — server software and commands used by the system administrator.
- `share/doc/kea/` — this guide, other supplementary documentation, and examples.
- `share/kea/` — API command examples and database schema scripts.
- `share/man/` — manual pages (online documentation).
- `var/lib/kea/` — server identification and lease database files.
- `var/log/` - log files.
- `var/run/kea` - PID file and logger lock file.


































































































