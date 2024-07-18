Foi apresentado pela primeira vez em 20 de Novembro de 1985 pela Microsoft. Sua primeira versão era um shell gráfico para MS-DOS. Versões posteriores do Windows Desktop introduziram os programas Windows File Manager, Program Manager e Print Manager.

- **Windows 95**: Primeira integração completa de Windows e DOS, introduzindo suporte integrado à Internet e o navegador Internet Explorer.
- **Evolução do Windows Desktop**: Desde o Windows 95, mais de uma dúzia de versões foram lançadas, incluindo Windows XP, Vista, 8 e a versão atual, Windows 10. Diversas edições foram oferecidas, atendendo tanto consumidores casuais quanto clientes corporativos.

- **Windows Server**: 
  - **Windows NT 3.1 Advanced Server (1993)**: Primeira versão do Windows Server, com várias atualizações subsequentes, adicionando tecnologias como IIS e assistentes administrativos.
  - **Windows 2000 Server**: Introdução do Active Directory, Microsoft Management Console (MMC) e volumes de disco dinâmicos.
  - **Windows Server 2003**: Introdução de funções de servidor, firewall integrado e Volume Shadow Copy Service.
  - **Windows Server 2008**: Clustering de failover, Hyper-V, Server Core, Event Viewer e melhorias no Active Directory.
  - **Versões subsequentes**: Incluem Server 2012, Server 2016 e Server 2019, com suporte para Kubernetes, contêineres Linux e recursos de segurança avançados.

- **Descontinuação de versões antigas**: Com a introdução de novas versões, as antigas são descontinuadas e deixam de receber atualizações, exceto em alguns casos com contratos de suporte de longo prazo. Exemplos incluem Server 2008 e 2012, que pararam de receber atualizações de segurança em 2020. Apenas Server 2012 R2 e versões posteriores ainda têm suporte, mas patches fora de banda foram lançados para versões mais antigas devido a vulnerabilidades críticas.

- **Legado e suporte a versões antigas**: Muitas versões do Windows são consideradas legadas e não mais suportadas, mas organizações frequentemente mantêm sistemas operacionais antigos para suporte a aplicativos críticos ou por razões operacionais e orçamentárias. Avaliadores precisam entender as diferenças entre versões e as vulnerabilidades inerentes a cada uma.
## Windows Versions

Podemos usar o [cmdlet](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7) [Get-WmiObject](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1) para encontrar informações sobre o sistema operacional. Este cmdlet pode ser usado para obter instâncias de classes WMI ou informações sobre classes WMI disponíveis. Há várias maneiras de encontrar a versão e o número de compilação do nosso sistema. Podemos obter facilmente essas informações usando a classe ``win32_OperatingSystem``, que mostra que estamos em um host Windows 10, número de compilação 19041.

```powershell
Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber
```
![[Pasted image 20240716140120.png]]

Algumas outras classes úteis que podem ser usadas com `Get-WmiObject` são `Win32_Process` para obter uma listagem de processos, `Win32_Service`para obter uma listagem de serviços e `Win32_Bios`para obter informações do [Basic Input/Output System](https://en.wikipedia.org/wiki/BIOS) ( `BIOS`).

O parâmetro `ComputerName` obtém informações sobre computadores remotos. `Get-WmiObject`pode ser usado para iniciar e parar serviços em computadores locais e remotos e muito mais. Mais informações sobre o cmdlet podem ser encontradas [aqui](https://ss64.com/ps/get-wmiobject.html) e [aqui](https://adamtheautomator.com/get-wmiobject/) .

## Acessando o Windows
### Conceitos de acesso Local
Se você está lendo estas palavras agora, você tem acesso local a um computador de algum tipo. Seja um smartphone, tablet, laptop, Raspberry Pi ou Desktop.

O acesso local é a maneira mais comum de acessar qualquer computador, incluindo computadores que executam Windows.

Normalmente o `Input` acontece por meio de um teclado, trackpad e/ou mouse. O `Output` pode vir da tela de exibição.

## Conceitos de acesso remoto
O acesso remoto é acessar um computador por uma rede. Primeiro acessamos um computador localmente antes de acessá-lo remotamente. Algumas indústrias dependem completamente do acesso remoto e da administração remota de sistemas de computador.

Considere `MSPs` e `MSSPs`, ambos os setores dependem do gerenciamento remoto dos sistemas de computadores de seus clientes. Essa funcionalidade permite o gerenciamento remoto de máquinas.

Algumas das tecnologias de acesso remoto mais comuns incluem, mas não estão limitadas a:

- Redes Privadas Virtuais (VPN)
- Shell Seguro (SSH)
- Protocolo de Transferência de Arquivos (FTP)
- Computação de rede virtual (VNC)
- Gerenciamento Remoto do Windows (ou PowerShell Remoting) (WinRM)
- Protocolo de Área de Trabalho Remota (RDP)

### Protocolo de área de trabalho remota (RDP)
Usa a arquitetura cliente servidor onde um aplicativo do lado do cliente é usado para especificar o endereço `IP` de destino de um computador ou nome de host em uma rede onde o acesso RDP está habilitado. 

O computador de destino onde o acesso remoto RDP está habilitado é considerado o servidor. O RDP escuta por padrão a porta `3389`. 

 Se estivermos nos conectando a um alvo Windows de um host Windows, podemos usar o aplicativo cliente RDP integrado chamado `Remote Desktop Connection`( [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) ).

![[UsingRemoteDesktopConnection.gif]]

Para funcionar, o acesso remoto deve estar permitido no sistema windows de destino. Por padrão o acesso remoto vem desabilitado.

O Remote Desktop Connection também nos permite salvar perfis de conexão. Esse é um hábito comum entre administradores de TI porque torna a conexão com sistemas remotos mais conveniente.

![[SavingRDPConnections.gif]]

Como pentesters, podemos nos beneficiar da busca por esses arquivos salvos da Área de Trabalho Remota ( `.rdp`) durante um compromisso.

### xfreerdp
Em um host baseado em ``linux`` podemos usar a ferramenta `xfreerdp` para acessar remotamente os alvos Windows.

![[ConnectingwithXfreerdp.gif]]

Há várias opções disponíveis para nós com xfreerdp, como redirecionamento de unidade para poder transferir arquivos de/para o host de destino, que vale a pena praticar

Existem outros clientes RDP, como [Remmina](https://remmina.org/) e [rdesktop](http://www.rdesktop.org/)

## Conectando-se ao Windows Target
Conecte-se via Área de Trabalho Remota (RDP) usando o seguinte comando:

```sh
xfreerdp /v:<targetIp> /u:htb-student /p:Password
```

---
## Referências

**`HTB`** : https://academy.hackthebox.com/module/49/section/454











































