`Server Message Block`( `SMB`) é um protocolo cliente-servidor que regula o acesso a arquivos e diretórios inteiros e outros recursos de rede, como impressoras, roteadores ou interfaces liberadas para a rede.

Com o projeto de software livre Samba, há também uma solução que permite o uso de SMB em distribuições Linux e Unix e, portanto, comunicação entre plataformas via SMB.

Em redes IP, o SMB usa o protocolo TCP para esse propósito, que fornece um handshake de três vias entre cliente e servidor antes que uma conexão seja finalmente estabelecida.

Os direitos de acesso são definidos por `Access Control Lists`( `ACL`). Eles podem ser controlados de maneira detalhada com base em atributos como `execute`, `read`e `full access`para usuários individuais ou grupos de usuários.

As ACLs são definidas com base nos compartilhamentos e, portanto, não correspondem aos direitos atribuídos localmente no servidor.

## Samba

O Samba implementa o protocolo de rede `Common Internet File System`( ). [CIFS](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e) é um "dialeto" do SMB. Em outras palavras, CIFS é uma implementação muito específica do protocolo SMB, que por sua vez foi criado pela Microsoft. Portanto, geralmente é chamado de **SMB/CIFS**.

No entanto, CIFS é a extensão do protocolo SMB. Então, quando passamos comandos SMB pelo Samba para um serviço NetBIOS mais antigo, ele geralmente se conecta ao servidor Samba pelas portas TCP `137`, `138`, `139`. Mas o CIFS usa apenas a porta TCP ``445``.

Em algumas infraestruturas específicas ainda é usado uma versão desatualizada do ``SMB``.

|**Versão SMB**|**Suportado**|**Características**|
|---|---|---|
|CIFS|Windows NT 4.0|Comunicação via interface NetBIOS|
|PME 1.0|Windows 2000|Conexão direta via TCP|
|PME 2.0|Windows Vista, Windows Server 2008|Atualizações de desempenho, assinatura aprimorada de mensagens, recurso de cache|
|PME 2.1|Windows 7, Windows Server 2008 R2|Mecanismos de bloqueio|
|PME 3.0|Windows 8, Windows Server 2012|Conexões multicanal, criptografia de ponta a ponta, acesso remoto ao armazenamento|
|SMB 3.0.2|Windows 8.1, Windows Server 2012 R2||
|PME 3.1.1|Windows 10, Windows Server 2016|Verificação de integridade, criptografia AES-128|

Com a versão 3, o servidor Samba ganhou a capacidade de ser um membro pleno de um domínio do Active Directory. Com a versão 4, o Samba ainda fornece um controlador de domínio do Active Directory.

O daemon do servidor SMB  (**smbd**) pertence ao Samba fornece as duas primeiras funcionalidades, enquanto o daemon do bloco de mensagens NetBios (**nmbd**) implementa as duas últimas funcionalidades. O serviço SMB controla esses dois programas de segundo plano.

Samba é adequado para sistemas Linux e Windows. Em uma rede, cada host participa da mesma **Work Group**. Pode haver vários grupos de trabalho na mesma rede.

A IBM desenvolveu uma ``API`` para computadores em rede chamado Network Basic Input/Output System (NetBIOS).

A API NetBIOS forneceu um modelo para um aplicativo conectar e compartilhar dados com outros computadores. Em ambiente NetBIOS, quando uma máquina fica online, ela precisa de um nome, o que é feito através do chamado `name registration`procedimento.

Cada host reserva seu nome de host na rede ou o [servidor de nomes NetBIOS](https://networkencyclopedia.com/netbios-name-server-nbns/) ( `NBNS`) é usado para essa finalidade. Ele também foi aprimorado para [o Windows Internet Name Service](https://networkencyclopedia.com/windows-internet-name-service-wins/) ( `WINS`).

## Configuração padrão

O SAMBA oferece uma gama de configurações, que definimos por meio de arquivos de texto.

### Configurações padrão

```bash
cat /etc/samba/smb.conf | grep -v "#\|/;"
```

```txt
[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

Aqui vemos configurações globais e dois compartilhamentos destinados a impressoras. As configurações globais são usadas para todos os compartilhamentos, no entanto os compartilhamentos individuais podem substituir as configurações globais, o que pode ser configurado com grande probabilidade até mesmo incorretamente. Vejamos algumas das configurações para entender como os compartilhamentos são configurados no Samba.

| **Contexto**                   | **Descrição**                                                                                    |
| ------------------------------ | ------------------------------------------------------------------------------------------------ |
| `[sharename]`                  | O nome do compartilhamento de rede.                                                              |
| `workgroup = WORKGROUP/DOMAIN` | Grupo de trabalho que aparecerá quando os clientes fizerem consultas.                            |
| `path = /path/here/`           | O diretório ao qual o usuário terá acesso.                                                       |
| `server string = STRING`       | A string que aparecerá quando uma conexão for iniciada.                                          |
| `unix password sync = yes`     | Sincronizar a senha do UNIX com a senha do SMB?                                                  |
| `usershare allow guests = yes` | Permitir que usuários não autenticados acessem o compartilhamento definido?                      |
| `map to guest = bad user`      | O que fazer quando uma solicitação de login de usuário não corresponde a um usuário UNIX válido? |
| `browseable = yes`             | Essa participação deve constar na lista de ações disponíveis?                                    |
| `guest ok = yes`               | Permitir conexão ao serviço sem usar senha?                                                      |
| `read only = yes`              | Permitir que os usuários leiam apenas arquivos?                                                  |
| `create mask = 0700`           | Quais permissões precisam ser definidas para arquivos recém-criados?                             |

### Configurações perigosas

| **Contexto**                | **Descrição**                                                               |
| --------------------------- | --------------------------------------------------------------------------- |
| `browseable = yes`          | Permitir listar os compartilhamentos disponíveis no compartilhamento atual? |
| `read only = no`            | Proibir a criação e modificação de arquivos?                                |
| `writable = yes`            | Permitir que os usuários criem e modifiquem arquivos?                       |
| `guest ok = yes`            | Permitir conexão ao serviço sem usar senha?                                 |
| `enable privileges = yes`   | Honrar privilégios atribuídos a um SID específico?                          |
| `create mask = 0777`        | Quais permissões devem ser atribuídas aos arquivos recém-criados?           |
| `directory mask = 0777`     | Quais permissões devem ser atribuídas aos diretórios recém-criados?         |
| `logon script = script.sh`  | Qual script precisa ser executado no login do usuário?                      |
| `magic script = script.sh`  | Qual script deve ser executado quando o script for fechado?                 |
| `magic output = script.out` | Onde a saída do script mágico precisa ser armazenada?                       |

amos criar um compartilhamento chamado `[notes]`e alguns outros e ver como as configurações afetam nosso processo de enumeração.

Usaremos todas as configurações acima e as aplicaremos a esse compartilhamento. Por exemplo, essa configuração é frequentemente aplicada, mesmo que apenas para fins de teste.

### Exemplo de Compartilhamento

```txt
...SNIP...

[notes]
	comment = CheckIT
	path = /mnt/notes/

	browseable = yes
	read only = no
	writable = yes
	guest ok = yes

	enable privileges = yes
	create mask = 0777
	directory mask = 0777
```

É altamente recomendável consultar as páginas de manual do Samba, configurá-lo e experimentar as configurações.

Depois de nos ajustarmos `/etc/samba/smb.conf`às nossas necessidades, temos que reiniciar o serviço no servidor.

### Reinicie o SAMBA

```bash
systemctl restart smbd
```

Agora podemos exibir uma lista ( `-L`) dos compartilhamentos do servidor com o `smbclient`comando do nosso host. Utilizamos o chamado `null session`( `-N`), que é `anonymous`o acesso sem a inserção de usuários existentes ou senhas válidas.

### SMB client - Conectando ao Compartilhamento

```bash
smbclient -N -L //10.129.14.128
```

```txt
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```

Podemos ver que agora temos cinco compartilhamentos diferentes no servidor Samba a partir do resultado. 

Assim, `print$`e um `IPC$`já estão incluídos por padrão na configuração básica, como já vimos.

Vamos inspecioná-lo usando o mesmo programa cliente. Se nã estiver familiarizado com o programa cliente, podemos utilizar o comando `help`, quando fizer login.

```bash
smbclient //10.129.14.128/notes
```

```output
Enter WORKGROUP\<username>'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.

```

```bash
smb: \> help
```

```bash
smb: \> ls
```

```output
  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available
```

Podemos baixar arquivos e pastas com o comando `get`. Smbclient também nos permite executar comandos do sistema local usando um ponto de exclamação no início ( `!<cmd>`) sem interromper a conexão.

### Baixando arquivos do SMB

```bash
smb: \> get prep-prod.txt

#getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec) 
#(average 8,7 KiloBytes/sec)
```

```bash
smb: \> !ls

#prep-prod.txt
```

```bash
smb: \> !cat prep-prod.txt

#[] check your code with the templates
#[] run code-assessment.py
#[] …	
```

Do ponto de vista administrativo, podemos verificar essas conexões usando `smbstatus`. Além da versão Samba, também podemos ver quem, de qual host e em qual compartilhamento o cliente está conectado. Isto é especialmente importante quando entramos em uma sub-rede (talvez até isolada) que os outros ainda podem acessar.

Com segurança em nível de domínio o samba atua como um membro de um domínio do Windows. Cada domínio tem pelo menos um controlador de domínio (Normalmente um Windows NT que fornece autenticação de senha). Este controlador fornece ao grupo um servidor de senha definitivo.

Os controladores de domínio controlam usuários e senhas em seus próprios `NTDS.dit` e `Security Authentication Module`( `SAM`) e autenticam cada usuário quando eles efetuam login pela primeira vez e desejam acessar o compartilhamento de outra máquina.

### Estado do Samba

```
root@samba:~# smbstatus

Samba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing              
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -                    

Service      pid     Machine       Connected at                     Encryption   Signing     
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -           

No locked files

```

## Enumeração

O Nmap também tem muitas opções e scripts NSE que podem nos ajudar a examinar o serviço SMB, a desvantagem é de que as varreduras podem demorara muito tempo, é recomendável que olhemos o serviço manualmente.

Primeiro, no entanto, vamos ver o que o Nmap pode encontrar em nosso servidor Samba de destino, onde criamos o `[notes]`compartilhamento para fins de teste.

## Nmap

```bash
sudo nmap 10.129.14.128 -sV -sC -p139,445
```

```txt
Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds
```

Podemos ver que não é muita coisa que o Nmap nos forneceu aqui.

Portanto, devemos recorrer a outras ferramentas que nos permitam interagir manualmente com o SMB e enviar solicitações específicas para as informações. Uma das ferramentas úteis para isso é o `rpcclient`. Esta é uma ferramenta para executar funções MS-RPC.

O processo de comunicação via RPC inclui a passagem de parâmetros e o retorno de um valor de função.

### Cliente RPC

```bash
rpcclient -U "" 10.129.14.128

#Enter WORKGROUP\'s password:
rpcclient $>
```

O `rpcclient`nos oferece muitas requisições diferentes com as quais podemos executar funções específicas no servidor SMB para obter informações. Uma lista completa de todas essas funções pode ser encontrada na [página man](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) do rpcclient.

| **Consulta**              | **Descrição**                                                               |
| ------------------------- | --------------------------------------------------------------------------- |
| `srvinfo`                 | Informação do servidor.                                                     |
| `enumdomains`             | Enumere todos os domínios implantados na rede.                              |
| `querydominfo`            | Fornece informações de domínio, servidor e usuário de domínios implantados. |
| `netshareenumall`         | Enumera todos os compartilhamentos disponíveis.                             |
| `netsharegetinfo <share>` | Fornece informações sobre um compartilhamento específico.                   |
| `enumdomusers`            | Enumera todos os usuários do domínio.                                       |
| `queryuser <RID>`         | Fornece informações sobre um usuário específico.                            |

### RPCclient - Enumeração

```bash
rpcclient $> srvinfo

#        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
#        platform_id     :       500
#        os version      :       6.1
#        server type     :       0x809a03

rpcclient $> enumdomains

#name:[DEVSMB] idx:[0x0]
#name:[Builtin] idx:[0x1]

rpcclient $> querydominfo

#Domain:         DEVOPS
#Server:         DEVSMB
#Comment:        DEVSM
#Total Users:    2
#Total Groups:   0
#Total Aliases:  0
#Sequence No:    1632361158
#Force Logoff:   -1
#Domain Server State:    0x1
#Server Role:    ROLE_DOMAIN_PDC
#Unknown 3:      0x1

rpcclient $> netsharegetinfo notes

#netname: notes
#        remark: CheckIT
#        path:   C:\mnt\notes\
#        password:
#        type:   0x0
#        perms:  0
#        max_uses:       -1
#        num_uses:       1
#revision: 1
#type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
#DACL
#        ACL     Num ACEs:       1       revision:       2
#        ---
#        ACE
#                type: ACCESS ALLOWED (0) flags: 0x00 
#                Specific bits: 0x1ff
#                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
#                SID: S-1-1-0
```

Esses exemplos nos mostram quais informações podem ser vazadas para usuários anônimos. Depois que um `anonymous`usuário tem acesso a um serviço de rede, basta um erro para conceder-lhe muitas permissões ou muita visibilidade para colocar toda a rede em risco significativo.

Vamos ver como podemos enumerar usuários usando o `rpcclient`.

## Rpcclient - Enumeração de Usuário

```bash
rpcclient $> enumdomusers

#user:[mrb3n] rid:[0x3e8]
#user:[cry0l1t3] rid:[0x3e9]
```

```bash
rpcclient $> queryuser 0x3e9

User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...
```

Podemos então usar os resultados para identificar o RID do grupo, que podemos usar para recuperar informações de todo o grupo.

### Rpcclient - Informações do Grupo

```bash
rpcclient $> querygroup 0x201

#Group Name:     None
#        Description:    Ordinary Users
#        Group Attribute:7
#        Num Members:2
```

Pode ser que tenhamos algumas restrições de acordo com o usuário, no entanto a consulta queryuser **<RID\>** é permitida principalmente com base no RID.

Como podemos não saber a quem foi atribuído qual RID, sabemos que obteremos informações sobre isso assim que consultarmos um RID atribuído.

Existem várias maneiras e ferramentas que podemos usar para isso. Para continuar com a ferramenta, podemos criar um `For-loop`using `Bash`onde enviamos um comando para o serviço usando rpcclient e filtramos os resultados.

### RIDs de usuário de força bruta

```bash 
for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```

```output
   User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
		
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
		
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201
```

Uma alternativa para isso seria um script Python da [Impacket](https://github.com/SecureAuthCorp/impacket) chamado [samrdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py) .

```bash
samrdump.py 10.129.14.128
```


As informações que já obtivemos `rpcclient`também podem ser obtidas por meio de outras ferramentas. Por exemplo, as ferramentas [SMBMap](https://github.com/ShawnDEvans/smbmap) e [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) também são amplamente utilizadas e úteis para a enumeração de serviços SMB.

### SMBMap

```bash
smbmap -H 10.129.14.128
```

```output
[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

### CrackMapExec

```bash
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

```output
SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

Outra ferramenta que vale a pena mencionar é a chamada [enum4linux-ng](https://github.com/cddmp/enum4linux-ng) , que é baseada em uma ferramenta mais antiga, a enum4linux.

### Enum4Linux-ng - Instalação

```bash
$ git clone https://github.com/cddmp/enum4linux-ng.git
$ cd enum4linux-ng
$ pip3 install -r requirements.txt
```

### Enum4Linux-ng - Enumeração

```
$ ./enum4linux-ng.py 10.129.14.128 -A
```






























































































































































































































































































































































