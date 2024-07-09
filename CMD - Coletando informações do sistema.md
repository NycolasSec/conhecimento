## Que tipos de informações podemos coletar do sistema?

Para responder a essa pergunta, precisamos ter um entendimento básico de todos os diferentes tipos de informações disponíveis para nós em um sistema. Abaixo está um gráfico que podemos consultar para nos dar um esboço generalizado dos principais tipos de informações que precisamos estar cientes ao executar a enumeração de host.

![[InformationTypesChart_Updated.webp]]

Os tipos de informação que procuraríamos podem ser divididos nas seguintes categorias:

| Tipo                         | Descrição                                                                                                                                                                                                                                                                                                                               |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `General System Information` | Contém informações sobre o sistema de destino geral. As informações do sistema de destino incluem, mas não estão limitadas a, `hostname`da máquina, detalhes específicos do SO ( `name`, `version`, `configuration`, etc.) e `installed hotfixes/patches`para o sistema.                                                                |
| `Networking Information`     | Contém informações de rede e conexão para o sistema de destino e sistema(s) aos quais o destino está conectado pela rede. Exemplos de informações de rede incluem, mas não estão limitados ao seguinte: `host IP address`, `available network interfaces`, `accessible subnets`, `DNS server(s)`, `known hosts`, e `network resources`. |
| `Basic Domain Information`   | Contém informações do Active Directory sobre o domínio ao qual o sistema de destino está conectado.                                                                                                                                                                                                                                     |
| `User Information`           | Contém informações sobre usuários e grupos locais no sistema de destino. Isso pode ser expandido para conter qualquer coisa acessível a essas contas, como `environment variables`, `currently running tasks`, `scheduled tasks`e `known services`.                                                                                     |

Para nos mantermos no alvo durante a enumeração, queremos tentar nos fazer algumas das seguintes perguntas:

- Que informações do sistema podemos extrair do nosso host de destino?
- Com quais outros sistemas nosso host de destino está interagindo na rede?
- A quais contas de usuário temos acesso e quais informações podem ser acessadas a partir delas?

## Por que precisamos dessas informações?

Seguindo um cenário de exemplo em recebemos a máquina com acesso a um usuário sem privilégios, podemos ver que recebemos acesso direto ao nosso host inicial por meio de uma conta de usuário presumidamente sem privilégios. No entanto, nosso objetivo é eventualmente escalar nossos privilégios para uma conta com acesso a privilégios mais altos ou permissões administrativas, se tivermos sorte. Para fazer isso, precisaremos de um entendimento completo do nosso ambiente, incluindo o seguinte:

- A qual conta de usuário temos acesso?
- A quais grupos nosso usuário pertence?
- A qual conjunto de privilégios de trabalho atual nosso usuário tem acesso?
- Quais recursos nosso usuário pode acessar pela rede?
- Quais tarefas e serviços estão sendo executados em nossa conta de usuário?

É por isso que dedicar nosso tempo e reunir todas as informações que pudermos sobre um sistema ou ambiente deve ser priorizado em termos de importância em vez de simplesmente explorar um sistema aleatoriamente.

## Como obtemos essas informações?

O CMD fornece um balcão único para informações por meio do `systeminfo`comando. Ele é excelente para encontrar informações relevantes sobre o host, como nome do host, endereço(s) IP, se ele pertence a um domínio, quais hotfixes foram instalados e muito mais. Essas informações são super valiosas para um administrador de sistemas ao tentar diagnosticar problemas.

Ter acesso rápido a coisas como a versão do sistema operacional, hotfixes instalados e versão de compilação do sistema operacional pode nos ajudar a determinar rapidamente, a partir de uma rápida pesquisa no Google ou [ExploitDB](https://www.exploit-db.com/) , se existe um exploit que pode ser rapidamente aproveitado para explorar ainda mais este host, elevar privilégios e muito mais.

#### saída do systeminfo
```cmd
C:\htb> systeminfo


Host Name:                 DESKTOP-htb
OS Name:                   Microsoft Windows 10 Pro
OS Version:                10.0.19042 N/A Build 19042
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free

<snipped>
```

### Examinando o sistema
Se precisarmos recuperar algumas informações básicas do sistema, como `hostname`ou `OS version`, podemos usar os utilitários : 

- `hostname` : Retorna o Hostname da máquina
- `ver` : Mostra a versão do Windows

### Escopo de rede
Um entendimento completo de como nosso alvo está conectado e quais dispositivos ele pode acessar pela rede é uma ferramenta inestimável em nosso arsenal como um invasor. Para ter acesso a essas configurações, podemos usar o comando :

- `ipconfig` : Exibe todas as configurações de rede TCP/IP da máquina.

```cmd
C:\htb> ipconfig

Windows IP Configuration

<SNIP>

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : htb.local
   Link-local IPv6 Address . . . . . : fe80::2958:39a:df51:b60%23
   IPv4 Address. . . . . . . . . . . : 10.0.25.17
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.0.25.1

Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : internal.htb.local
   Link-local IPv6 Address . . . . . : fe80::bc3b:6f9f:68d4:3ec5%26
   IPv4 Address. . . . . . . . . . . : 172.16.50.15
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 172.16.50.1

<SNIP>
```

Se quisermos informações adicionais podemos usar o comando :

- `ipconfig /all` : Mostra mais detalhes de rede e dos adaptadores de rede.

Se precisarmos ver rapidamente com quais hosts nosso destino entrou em contato, não procure além do comando :

- `arp` : Exibe a tabela ARP

```cmd
C:\htb> arp /a

<SNIP>

Interface: 10.0.25.17 --- 0x17
  Internet Address      Physical Address      Type
  10.0.25.1             00-e0-67-15-cf-43     dynamic
  10.0.25.5             54-9f-35-1c-3a-e2     dynamic
  10.0.25.10            00-0c-29-62-09-81     dynamic
  10.0.25.255           ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
  255.255.255.255       ff-ff-ff-ff-ff-ff     static

Interface: 172.16.50.15 --- 0x1a
  Internet Address      Physical Address      Type
  172.16.50.1           15-c0-6b-58-70-ed     dynamic
  172.16.50.20          80-e5-53-3c-72-30     dynamic
  172.16.50.32          fb-90-01-5c-1f-88     dynamic
  172.16.50.65          7a-49-56-10-3b-76     dynamic
  172.16.50.255         ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static\

<SNIP>
```

Podemos usar essas informações para começar a mapear a rede ao longo de cada uma das interfaces de rede pertencentes ao nosso alvo.

### Conhecendo nosso usuário atual
Para conhecer melhor nossa conta de usuário comprometida atual podemos usar o comando : 

>
>**Nota:** Se o usuário atual não for uma conta associada ao domínio, o `NetBIOS`nome será fornecido em vez disso. O atual `hostname`será usado na maioria dos casos.
>

- `whoami` : Retorna em qual domínio e qual usuário estamos conectados.

### Verificando nossos privilégios
Também podemos usar `whoami`para visualizar os privilégios de segurança do nosso usuário atual no sistema.

- `whoami /priv`

```cmd
C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

### Investigando Grupos
Também devemos reservar um tempo para ver de quais grupos nossa conta é membro. Para visualizar os grupos dos quais nosso usuário atual faz parte, podemos emitir o seguinte comando:

- `whoami /groups` : Exibe os grupos do usuário atual

```cmd
C:\htb> whoami /groups

GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Performance Log Users          Alias            S-1-5-32-559 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                          Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192
```

>
>**Nota:** Os comandos mostrados acima contêm apenas certas seções da saída fornecida por `whoami /all`. Dependendo da situação e das informações necessárias, podemos usar os comandos individuais ou reunir todas as informações de uma vez por meio do uso do parâmetro `/all`.
>

### Investigando outros usuários/grupos
Após investigar nossa conta de usuário comprometida atual, precisamos nos ramificar um pouco e ver se podemos obter acesso a outras contas. Devido à natureza das redes unidas ao domínio, qualquer um pode efetuar login em qualquer host físico na rede sem precisar de uma conta local na máquina.

Isso é muito benéfico como um método de manter a persistência na rede. Para fazer isso, podemos utilizar a funcionalidade específica do comando `net`.

#### net user

- `net user` : Nos permite exibir uma lista de todos os usuários em um host, informações sobre um usuário específico e criar ou excluir usuários.

```cm
C:\htb> net user

User accounts for \\ACADEMY-WIN11

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
htb-student              WDAGUtilityAccount
The command completed successfully.
```

Da saída fornecida, apenas algumas contas de usuário foram criadas para esta máquina. No entanto, se estivéssemos em uma rede mais populosa, poderíamos encontrar mais contas para tentar comprometer.

#### net group / net localgroup

Além das contas de usuário, também devemos dar uma olhada rápida em quais grupos existem na rede. Também temos a capacidade do nosso host atual de visualizar todos os grupos que existem em nosso host e no domínio com os comando:

- `net group` : Exibirá todos os grupos que existem no host do qual emitimos o comando, criará e excluirá grupos e adicionará ou removerá usuários de grupos, deve ser executado em um servidor de domínio, como o DC
- `net localgroup` : O mesmo que o `net group` mas pode ser executado em qualquer host, mostrando os grupos que ele contém.

```cmd
C:\htb> net group
net group
This command can be used only on a Windows Domain Controller.

More help is available by typing NET HELPMSG 3515.


C:\htb>net localgroup

Aliases for \\ACADEMY-WIN11

-------------------------------------------------------------------------------
*__vmware__
*Access Control Assistance Operators
*Administrators
*Backup Operators
*Cryptographic Operators
*Device Owners
*Distributed COM Users
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Remote Desktop Users
*Remote Management Users
*Replicator
*System Managed Accounts Group
*Users
The command completed successfully.
```


### Explorando recursos na rede

#### net share
O comando `net share` nos permite exibir informações sobre recursos compartilhados no host e criar novos recursos compartilhados também.

```cmd
C:\htb> net share  

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\Windows                      Remote Admin
Records      D:\Important-Files              Mounted share for records storage  
The command completed successfully.
```

Como podemos ver no exemplo acima, temos uma lista de compartilhamentos aos quais nosso usuário comprometido atual tem acesso.

Idealmente, se encontrássemos um compartilhamento aberto como esse durante um engajamento, precisaríamos monitorar o seguinte:

- Temos as permissões adequadas para acessar este compartilhamento?
- Podemos ler, escrever e executar arquivos no compartilhamento?
- Há algum dado valioso sobre a ação?

Se não estivermos muito preocupados em sermos furtivos, podemos soltar uma carga útil ou outros dados em um compartilhamento para permitir a movimentação em torno de outros hosts na rede. Embora fora do escopo deste módulo, abusar de compartilhamentos dessa maneira é um excelente método de persistência e pode ser potencialmente usado para escalar privilégios.

#### net view
O comando `net view` mostrará quaisquer recursos compartilhados que o host contra o qual você está emitindo o comando saiba. Isso inclui recursos de domínio, compartilhamentos, impressoras e mais.

```cmd
C:\htb> net view  
```

---

Tenha em mente que esta rota é bastante barulhenta, e seremos notados eventualmente até mesmo por um `blue team` semicompetente.










































































































































