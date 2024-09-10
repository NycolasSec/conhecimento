### Arquitetura do SELinux

O **Security Enhanced Linux (SELinux)** é um recurso avançado de segurança no Linux que controla o acesso a arquivos, portas e recursos de maneira granular, por meio de políticas específicas ou configurações booleanas. Diferente das permissões tradicionais de arquivos, que controlam apenas quem pode acessar um arquivo, o SELinux define como um recurso pode ser utilizado, prevenindo usos indevidos, como a corrupção de dados.

As políticas de SELinux são configuradas por desenvolvedores e especificam permissões detalhadas para executáveis, arquivos de configuração e dados de aplicativos. Isso garante uma proteção mais robusta e orientada por finalidade.

### Uso do SELinux

O SELinux (Security Enhanced Linux) impõe regras de acesso detalhadas, garantindo que apenas as ações explicitamente permitidas nas políticas de segurança sejam realizadas. Aplicativos sem políticas específicas operam sem a proteção do SELinux, enquanto os aplicativos com políticas são "confinados" em domínios específicos. 

### Modos operacionais do SELinux:

O **SELinux (Security Enhanced Linux)** impõe regras de acesso detalhadas, garantindo que apenas as ações explicitamente permitidas nas políticas de segurança sejam realizadas. Aplicativos sem políticas específicas operam sem a proteção do SELinux, enquanto os aplicativos com políticas são "confinados" em domínios específicos.

O SELinux tem os seguintes modos operacionais:
1. **Coercitivo (Enforcing)**: O SELinux aplica rigorosamente as políticas carregadas, bloqueando acessos não autorizados. Esse é o modo padrão no Red Hat Enterprise Linux.
    
2. **Permissivo (Permissive)**: As políticas são carregadas, mas as violações não bloqueiam o acesso; em vez disso, são apenas registradas para análise e depuração.
    
3. **Desabilitado (Disabled)**: O SELinux é completamente desativado, o que não é recomendado, pois elimina as proteções e registros de violações.
    
O modo permissivo é útil para desenvolvimento e solução de problemas, enquanto o coercitivo garante a proteção total.

### Conceitos básicos do SELinux

O SELinux (Security Enhanced Linux) tem como objetivo proteger os dados dos usuários contra o uso inadequado por aplicativos ou serviços comprometidos. Ele complementa o modelo tradicional de **Controle de Acesso Discricionário** (DAC), que depende de permissões configuradas por administradores, com o **Controle de Acesso Obrigatório** (MAC), que impõe regras de segurança obrigatórias para todos os usuários.

No SELinux, cada recurso do sistema (como arquivos, diretórios ou portas) possui um "**contexto SELinux**" que segue regras específicas de acesso. Por padrão, o SELinux nega qualquer acesso a menos que uma regra explícita permita. Isso reforça a segurança contra ameaças, como vulnerabilidades em serviços como o **Apache**, prevenindo o comprometimento de dados críticos.

Os rótulos SELinux têm campos `user`, `role`, `type` e `security level`. A política de destino, que é habilitada no RHEL por padrão, define regras usando o contexto `type`. Nomes de contexto de tipo geralmente terminam com `_t`.

![[Pasted image 20240909170139.png]]

#### Conceitos de regra de acesso à política
No SELinux, diferentes tipos de processos e recursos são rotulados com contextos específicos para controlar o acesso. Por exemplo:

- **Processos de servidor web** (como o Apache) têm o contexto `httpd_t`.
- **Arquivos e diretórios do servidor web** (localizados em `/var/www/html/`) são rotulados com `httpd_sys_content_t`.
- **Arquivos temporários** nos diretórios `/tmp` e `/var/tmp` têm o contexto `tmp_t`.
- **Portas do servidor web** são rotuladas com `http_port_t`.

As regras de política SELinux permitem que o Apache acesse arquivos com o contexto `httpd_sys_content_t`, mas não permitem acesso a arquivos rotulados como `tmp_t`. Isso impede que um processo Apache comprometido acesse arquivos temporários.

De forma similar, o **servidor MariaDB** executa com o contexto `mysqld_t`, e os arquivos no diretório `/data/mysql` têm o contexto `mysqld_db_t`. O MariaDB pode acessar arquivos com `mysqld_db_t`, mas não pode acessar arquivos rotulados como `httpd_sys_content_t`, garantindo que diferentes serviços e dados permaneçam isolados e protegidos.

![[Pasted image 20240909170525.png]]

Muitos comandos que listam recursos usam a opção `-Z` para gerenciar contextos SELinux. Por exemplo, os comandos `ps`, `ls`, `cp` e `mkdir` usam a opção `-Z`.

```shell-session
[root@host ~]# ps axZ
```
![[Pasted image 20240909170604.png]]

```shell-session
[root@host ~]# systemctl start httpd
```

```shell-session
[root@host ~]# ps -ZC httpd
```
![[Pasted image 20240909170648.png]]

```shell-session
[root@host ~]# ls -Z /var/www
```
![[Pasted image 20240909170732.png]]

#### Alteração do modo SELinux
Use o comando `getenforce` para visualizar o modo SELinux atual.
Use o comando `setenforce` para alterar o modo SELinux.

```shell-session
[root@host ~]# getenforce
```
![[Pasted image 20240909170901.png]]

```shell-session
[root@host ~]# setenforce
```
![[Pasted image 20240909170922.png]]

```shell-session
[root@host ~]# setenforce 0
[root@host ~]# getenforce
```
![[Pasted image 20240909170956.png]]

```shell-session
[root@host ~]# setenforce Enforcing
[root@host ~]# getenforce
```
![[Pasted image 20240909171023.png]]

Como alternativa, defina o modo SELinux no momento do boot com um parâmetro de kernel. Passe o parâmetro do kernel `enforcing=0` para inicializar o sistema no modo `permissive` ou transmita `enforcing=1` para inicializar no modo `enforcing`. Desabilite o SELinux transmitindo o parâmetro do kernel `selinux=0` ou transmita `selinux=1` para habilitar o SELinux.

#### Definição do modo padrão do SELinux
Para configurar o SELinux de modo persistente, use o arquivo `/etc/selinux/config`.

No exemplo padrão a seguir, a configuração define o SELinux para o modo `enforcing`. Os comentários listam outros valores válidos, como os modos `permissive` e `disabled`.

![[Pasted image 20240909171342.png]]

O sistema lê esse arquivo no momento do boot e inicie o SELinux de acordo. Os argumentos do kernel `selinux=0|1` e `enforcing=0|1` substituem essa configuração.






























