# Linux - Segurança Linux

Uma das medidas de segurança mais importantes dos sistemas operacionais Linux é manter o SO e os pacotes instalados atualizados. Isso pode ser feito com um comando como:

```bash
apt update && apt upgrade
```

Se as regras de firewall não estiverem definidas adequadamente no nível da rede, podemos usar o firewall do Linux e/ou `iptables` para restringir o tráfego de entrada/saída do host.

Se o SSH estiver aberto no servidor, a configuração deve ser definida para não permitir login por senha e não permitir que o usuário root faça login via SSH.

O acesso dos usuários deve ser determinado com base no princípio do menor privilégio. Por exemplo, se um usuário precisa executar um comando como `root`, esse comando deve ser especificado na configuração `sudoers`

Outro mecanismo de proteção comum que pode ser usado é `fail2ban`. Ela conta o número de tentativas de login com falha e, se um usuário atingiu o número máximo, o host que tentou se conectar será tratado conforme configurado.

Também é importante auditar o sistema periodicamente para garantir que não existam problemas que possam facilitar a escalada de privilégios, como um kernel desatualizado, problemas de permissão de usuário, arquivos world-writable e tarefas cron mal configuradas ou serviços mal configurados. Muitos administradores esquecem da possibilidade de que algumas versões do kernel tenham que ser atualizadas manualmente.

Uma opção para bloquear ainda mais os sistemas Linux é `Security-Enhanced Linux`( `SELinux`) ou `AppArmor`.

O SELinux fornece controles de acesso muito granulares, como especificar quem pode anexar a um arquivo ou movê-lo.

Além disso, existem diferentes aplicativos e serviços como [Snort](https://www.snort.org/) , [chkrootkit](http://www.chkrootkit.org/) , [rkhunter](https://packages.debian.org/sid/rkhunter) , [Lynis](https://cisofy.com/lynis/) e outros que podem contribuir para a segurança do Linux. Além disso, algumas configurações de segurança devem ser feitas, como:

- Removendo ou desabilitando todos os serviços e softwares desnecessários
- Removendo todos os serviços que dependem de mecanismos de autenticação não criptografados
- Certifique-se de que o NTP esteja habilitado e o Syslog esteja em execução
- Garantir que cada usuário tenha sua própria conta
- Aplicar o uso de senhas fortes
- Configure o envelhecimento da senha e restrinja o uso de senhas anteriores
- Bloqueio de contas de usuários após falhas de login
- Desabilitar todos os binários SUID/SGID indesejados

## TCP Wrappers

O TCP wrapper é um mecanismo de segurança usado em sistemas Linux que permite ao administrador do sistema controlar quais serviços têm permissão de acesso ao sistema.

Ele funciona restringindo o acesso a certos serviços com base no nome do host ou endereço IP do usuário que solicita acesso.

Quando um cliente tenta se conectar a um serviço, o sistema primeiro consulta as regras definidas nos arquivos de configuração do TCP wrapper para determinar o endereço IP do cliente.

se os critérios não forem atendidos, a conexão será negada, fornecendo uma camada adicional de segurança para o serviço. Os TCP wrappers usam os seguintes arquivos de configuração:

- `/etc/hosts.allow`
	
- `/etc/hosts.deny`

#### /etc/hosts.allow
```bash
cat /etc/hosts.allow
```

```txt
# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```

#### /etc/hosts.deny
```bash
cat /etc/hosts.deny
```

```txt
# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```



























































































