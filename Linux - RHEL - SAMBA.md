### Instalação

```bash
sudo dnf install samba samba-client -y
```

### Status / Inicialização

```bash
sudo systemctl status  smb
```

```bash
sudo systemctl start smb
sudo systemctl enable smb
```

### Criando pasta a ser compartilhada

Criando a pasta a ser compartilhada
```bash
cd ~/Documents
mkdir public_share
sudo chmod 777 public_share
```

### Configurações

Diferente do que no Ubuntu e derivados, o arquivo de configuração do ``SAMBA`` no ``RHEL`` é bem enxuto

 As configurações do ``SAMBA`` são salvas no arquivo `/etc/samba/smb.conf`
#### Padrão
```conf
[global]
        workgroup = SAMBA
        security = user
        map to guest = Bad User

        passdb backend = tdbsam

        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes

[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
```

#### Configuração padrão de uma pasta pública
```conf
[global] 
        map to guest = Bad User
[public]
		path = /public_share/
		writable = yes
		guest ok = yes
		guest only = yes
		create mode = 0777
		directory mode = 0777
```
 
- `map to guest = Bad User` : Nas pastas onde convidados não forem aceitos, os convidados serão classificados como ``Bad User``

- `path` : Indica o caminho do compartilhamento no sistema
- `writable` : Define se os usuários tem permissão de gravação
- `guest ok` : Define se convidados são aceitos
- `guest only` : Define se só terão convidados nesta pasta
- `create mode` : Herança padrão dos arquivos criados
- `directory mode` : Herança padrão das pasta criadas

Configuramos, mas ainda precisamos liberá-lo no **Firewall**

## Firewall e SELinux

### firewalld

```bash
sudo firewall-cmd --add-service=samba --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```
![[Pasted image 20240708173346.png]]

O Firewall trata a questão de protocolos e portas, mas tem uma camada adicional de segurança chamada SELinux, que trata a questão de permissão de arquivos

### SELinux

```bash
sudo getenforce
#Enforcing
```

- `getenforce` : Retorna em qual modo está o SELinux. `Enforcing` indica que ele está ativo

```bash
sudo setenforce 0
```

- `setenforce` : Nos permite setar o modo do SELinux, mas está configuração se perde ao reiniciarmos a máquina

Para mantermos a configuração, temos de alterar o arquivo de configuração `/etc/sysconfig/selinux`

#### /etc/sysconfig/selinux
```txt
SELINUX=enforcing
SELINUXTYPE=targeted
```

Devemos alterar a opção `SELINUX`, ou criar uma regra para permitir o SAMBA.

Os modos são:
- `enforcing` : Ativo, agindo de acordo com as regras. 
- `permissive` : Permite os acessos, mas gera `log`
- `disable` : Desabilitado

#### Adicionando regra

```bash
sudo setsebool -P samba_enable_home_dirs on
```
Assim permitimos que o SAMBA pode acessar as pastas.

- `P` : Assim ele será salvo como padrão, e será salvo ao rebootarmos.

Mas ainda precisamos restaurar as permissões do pasta em específico.

```bash
sudo restorecon -R /public_share/
```

- `R` : Adiciona recursividade nos arquivos da pasta

Assim ele restaura o contexto de segurança SELinux padrão dos arquivos.



































