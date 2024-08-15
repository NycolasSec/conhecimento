Os servidores Windows podem ser gerenciados remotamente a partir do Windows Server 2016, com essa função habilitada por padrão. O gerenciamento remoto utiliza o protocolo WS-Management, permitindo o controle de hardware e diagnósticos através de controladores de gerenciamento de placa base, além de oferecer uma API COM para criação de aplicativos que se comunicam remotamente.

Os principais componentes utilizados para gerenciamento remoto do Windows e servidores Windows são os seguintes:

- Protocolo de Área de Trabalho Remota ( `RDP`)
    
- Gerenciamento Remoto do Windows ( `WinRM`)
    
- Instrumentação de gerenciamento do Windows ( `WMI`)

## PDR
O Remote Desktop Protocol (RDP), desenvolvido pela Microsoft, permite acesso remoto a computadores com Windows, transmitindo comandos de exibição e controle via GUI criptografada em redes IP, geralmente usando a porta TCP 3389. Para estabelecer uma sessão RDP, os firewalls de rede e do servidor devem permitir conexões externas, e, se NAT for usado, é necessário configurar o encaminhamento de porta no roteador NAT.

O RDP utiliza Transport Layer Security (TLS/SSL) desde o Windows Vista para proteger os dados, mas a criptografia fraca ainda pode ser aceita, com certificados auto assinados por padrão.

O serviço RDP é instalado por padrão em servidores Windows e pode ser ativado pelo Server Manager, permitindo conexões apenas com autenticação de nível de rede (NLA).

## Footprinting the Service
Escanear o serviço RDP pode rapidamente nos dar muitas informações sobre o host. Por exemplo, podemos determinar se `NLA`está habilitado no servidor ou não, a versão do produto e o nome do host.

```shell-session
NycolasES6@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

Além disso, podemos usar `--packet-trace`para rastrear os pacotes individuais e inspecionar seus conteúdos manualmente.

Podemos ver que o `RDP cookies`( `mstshash=nmap`) usado pelo Nmap para interagir com o servidor RDP pode ser identificado por `threat hunters`e vários serviços de segurança, como [Endpoint Detection and Response](https://en.wikipedia.org/wiki/Endpoint_detection_and_response) ( `EDR`), e pode nos bloquear como testadores de penetração em redes reforçadas.

```shell-session
NycolasES6@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n
```

Um script Perl chamado [rdp-sec-check.pl](https://github.com/CiscoCXSecurity/rdp-sec-check) também foi desenvolvido pelo [Cisco CX Security Labs](https://github.com/CiscoCXSecurity) que pode identificar de forma não autêntica as configurações de segurança dos servidores RDP com base nos handshakes.

#### Verificação de segurança RDP - Instalação
```sh
sudo cpan
```

#### Verificação de segurança RDP
```shell-session
NycolasES6@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check

NycolasES6@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248
```

A autenticação e a conexão a tais servidores RDP podem ser feitas de várias maneiras. Por exemplo, podemos nos conectar a servidores RDP no Linux usando `xfreerdp`, `rdesktop`, ou `Remmina`e interagir com a GUI do servidor adequadamente.

#### Iniciar uma sessão RDP
```shell-session
NycolasES6@htb[/htb]$ xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248
```
Após a autenticação bem-sucedida, uma nova janela aparecerá com acesso à área de trabalho do servidor ao qual nos conectamos.

## WinRm
O Windows Remote Management (WinRM) é um protocolo de gerenciamento remoto baseado em linha de comando, que utiliza o Simple Object Access Protocol (SOAP) para conectar-se a hosts remotos.

A partir do Windows 10, o WinRM deve ser habilitado e configurado explicitamente, utilizando as portas TCP 5985 e 5986 (com HTTPS). O WinRM, junto com o Windows Remote Shell (WinRS), permite a execução de comandos remotos. Ele é necessário para serviços como sessões remotas usando PowerShell e mesclagem de log de eventos.

Embora o WinRM esteja habilitado por padrão no Windows Server 2012 e posterior, ele precisa ser configurado em versões mais antigas.

### Footprinting the service
Como já sabemos, o WinRM usa portas TCP `5985`( `HTTP`) e `5986`( `HTTPS`) por padrão, que podemos escanear usando o Nmap. No entanto, frequentemente veremos que apenas HTTP ( `TCP 5985`) é usado em vez de HTTPS ( `TCP 5986`).

#### Nmap WinRm
```shell-session
NycolasES6@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

Podemos descobrir se um ou mais servidores remotos podem ser alcançados via WinRM com o PowerShell. O cmdlet [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) é responsável por isso, e o nome do host em questão é passado para ele.

Em ambientes baseados em Linux, podemos usar a ferramenta chamada [evil-winrm](https://github.com/Hackplayers/evil-winrm) , outra ferramenta de teste de penetração projetada para interagir com o WinRM.

```shell-session
NycolasES6@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```

## WMI
O Windows Management Instrumentation (WMI) é a implementação da Microsoft do Common Information Model (CIM) e é central para o Web-Based Enterprise Management (WBEM) no Windows.

Ele permite acesso de leitura e gravação a quase todas as configurações dos sistemas Windows, sendo uma interface essencial para administração e manutenção remota de PCs e servidores Windows.

O WMI pode ser acessado via PowerShell, VBScript ou Windows Management Instrumentation Console (WMIC) e consiste em vários programas e repositórios de dados, não sendo um programa único.

### Footprinting the service
A inicialização da comunicação WMI sempre ocorre na `TCP`porta `135`, e após o estabelecimento bem-sucedido da conexão, a comunicação é movida para uma porta aleatória. Por exemplo, o programa [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) do kit de ferramentas Impacket pode ser usado para isso.

```shell-session
NycolasES6@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```

Novamente, é necessário mencionar que o conhecimento adquirido com a instalação desses serviços e brincando com as configurações em nossa própria VM do Windows Server para ganhar experiência e desenvolver o princípio funcional e o ponto de vista do administrador não pode ser substituído pela leitura de manuais.






















