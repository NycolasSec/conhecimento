## Exemplo de detecção 1: Detectando relacionamentos estranhos entre pais e filhos

Relacionamentos anormais pai-filho entre processos podem ser indicativos de atividades maliciosas. Em ambientes Windows padrão, certos processos nunca chamam ou geram outros.

Por exemplo, é altamente improvável ver "calc.exe" gerando "cmd.exe" em um ambiente Windows normal. Entender esses relacionamentos pai-filho típicos pode ajudar a detectar anomalias. Samir Bousseaden compartilhou um mapa mental perspicaz apresentando relacionamentos pai-filho comuns, que podem ser referenciados [aqui](https://twitter.com/SBousseaden/status/1195373669930983424).

Ao utilizar o Process Hacker, podemos explorar relacionamentos pai-filho dentro do Windows. Classificar os processos por menus suspensos na visualização Processes revela uma representação hierárquica dos relacionamentos.

![[Pasted image 20240829140324.png]]
Analisar esses relacionamentos em ambientes padrão e personalizados nos permite identificar desvios de padrões normais. Por exemplo, se observarmos o processo "spoolsv.exe" criando "whoami.exe" em vez de seu comportamento esperado de criar um "conhost", isso levanta suspeitas.

![[Pasted image 20240829140339.png]]

Para mostrar um estranho relacionamento pai-filho, onde "cmd.exe" parece ser criado por "spoolsv.exe" `with no accompanying arguments`, utilizaremos uma técnica de ataque chamada Parent PID Spoofing. O Parent PID Spoofing pode ser executado por meio do [projeto psgetsystem](https://github.com/decoder-it/psgetsystem) da seguinte maneira.

```powershell-session
PS C:\Tools\psgetsystem> powershell -ep bypass
PS C:\Tools\psgetsystem> Import-Module .\psgetsys.ps1 
PS C:\Tools\psgetsystem> [MyProcess]::CreateProcessFromParent([Process ID of spoolsv.exe],"C:\Windows\System32\cmd.exe","")
```

![[Pasted image 20240829140529.png]]

Devido à técnica de spoofing de PID pai que empregamos, o Evento Sysmon 1 é exibido incorretamente `spoolsv.exe`como o pai de `cmd.exe`. No entanto, foi ele `powershell.exe`quem realmente criou `cmd.exe`.

Vamos começar coletando dados do `Microsoft-Windows-Kernel-Process`provedor usando [SilkETW](https://github.com/mandiant/SilkETW) (o provedor pode ser identificado usando `logman`como descrito anteriormente, `logman.exe query providers | findstr "Process"`). Depois disso, podemos prosseguir para simular o ataque novamente para avaliar se ETW pode nos fornecer informações mais precisas sobre a execução de `cmd.exe`.

```cmd-session
c:\Tools\SilkETW_SilkService_v8\v8\SilkETW>SilkETW.exe -t user -pn Microsoft-Windows-Kernel-Process -ot file -p C:\windows\temp\etw.json
```

![[Pasted image 20240829140822.png]]

O arquivo `etw.json` (que inclui dados do `Microsoft-Windows-Kernel-Process`provedor) parece conter informações sobre `powershell.exe`quem o criou `cmd.exe`.

![[Pasted image 20240829140903.png]]

Vale ressaltar que os logs de eventos do SilkETW podem ser ingeridos e visualizados pelo Visualizador de Eventos do Windows `SilkService`para nos fornecer uma visibilidade mais profunda e abrangente das ações executadas em um sistema.

## Exemplo de detecção 2: Detectando carregamento de assembly .NET malicioso
Tradicionalmente, os adversários empregavam uma estratégia conhecida como ["Living off the Land" (LotL)](https://www.attackiq.com/2023/03/16/hiding-in-plain-sight/) , explorando ferramentas legítimas do sistema, como o PowerShell, para executar suas operações maliciosas, reduzindo o risco de detecção.

Respondendo a esses avanços defensivos, os invasores desenvolveram uma nova abordagem que a Mandiant rotula como ["Bring Your Own Land" (BYOL)](https://www.mandiant.com/resources/blog/bring-your-own-land-novel-red-teaming-technique) . Em vez de depender das ferramentas já presentes no sistema da vítima, os agentes de ameaças e os testadores de penetração que emulam essas táticas agora empregam assemblies .NET executados inteiramente na memória.


Isso envolve a criação de ferramentas personalizadas tornando-as independentes das ferramentas pré-existentes no sistema de destino. São bastante eficazes pelos seguintes motivos:
- Cada sistema Windows vem equipado com uma determinada versão do .NET pré-instalada por padrão.
- Um recurso saliente do .NET é sua natureza gerenciada, aliviando a necessidade de programadores lidarem manualmente com o gerenciamento de memória. Esse atributo é parte do processo de execução de código gerenciado do framework, onde o Common Language Runtime (CLR) assume a responsabilidade por operações-chave no nível do sistema, como coleta de lixo, eliminando vazamentos de memória e garantindo uma utilização mais eficiente de recursos.
- Uma das vantagens intrigantes de usar assemblies .NET é sua capacidade de serem carregados diretamente na memória. Isso significa que um executável ou DLL não precisa ser gravado fisicamente no disco - em vez disso, ele é executado diretamente na memória. Esse comportamento minimiza os artefatos deixados para trás no sistema e pode ajudar a ignorar algumas formas de detecção que dependem da inspeção de arquivos gravados no disco.
- A Microsoft integrou uma ampla gama de bibliotecas no .NET framework para lidar com vários desafios comuns de programação. Essas bibliotecas incluem funcionalidades para estabelecer conexões HTTP, implementar operações criptográficas e habilitar a comunicação entre processos (IPC), como pipes nomeados. Essas ferramentas pré-construídas simplificam o processo de desenvolvimento, reduzem a probabilidade de erros e facilitam a construção de aplicativos robustos e eficientes. Além disso, para um agente de ameaça, esses recursos avançados fornecem um kit de ferramentas para criar métodos de ataque mais sofisticados e secretos.

Uma ilustração poderosa dessa estratégia BYOL é o comando ["execute-assembly"](https://www.cobaltstrike.com/blog/cobalt-strike-3-11-the-snake-that-eats-its-tail/) implementado no CobaltStrike, uma plataforma de software amplamente usada para Simulações de Adversários e Operações de Red Team. O comando 'execute-assembly' do CobaltStrike permite que o usuário execute assemblies .NET diretamente da memória, tornando-o uma ferramenta ideal para implementar uma estratégia BYOL.

De forma semelhante a como detectamos a execução de scripts PowerShell não gerenciados por meio da observação de atividade anômala `clr.dll`e `clrjit.dll`de carregamento em processos que normalmente não os exigiriam, podemos empregar uma abordagem semelhante para identificar carregamento de assembly .NET malicioso. Isso é obtido examinando a atividade relacionada ao carregamento de [DLLs associadas ao .NET](https://redhead0ntherun.medium.com/detecting-net-c-injection-execute-assembly-1894dbb04ff7) , especificamente `clr.dll`e `mscoree.dll`.

Monitorar o carregamento dessas bibliotecas pode ajudar a revelar tentativas de executar assemblies .NET em contextos incomuns ou inesperados, o que pode ser um sinal de atividade maliciosa. Esse tipo de comportamento de carregamento de DLL pode frequentemente ser detectado aproveitando o Event ID 7 do Sysmon, que corresponde aos eventos "Image Loaded".

Para fins demonstrativos, vamos emular um carregamento de assembly .NET malicioso executando uma versão pré-compilada do [Seatbelt](https://github.com/GhostPack/Seatbelt) que reside no disco. `Seatbelt`é um assembly .NET bem conhecido, frequentemente empregado por adversários que o carregam e executam na memória para obter consciência situacional em um sistema comprometido.

```powershell-session
PS C:\Tools\GhostPack Compiled Binaries>.\Seatbelt.exe TokenPrivileges

                        %&&@@@&&
                        &&&&&&&%%%,                       #&&@@@@@@%%%%%%###############%
                        &%&   %&%%                        &////(((&%%%%%#%################//((((###%%%%%%%%%%%%%%%
%%%%%%%%%%%######%%%#%%####%  &%%**#                      @////(((&%%%%%%######################(((((((((((((((((((
#%#%%%%%%%#######%#%%#######  %&%,,,,,,,,,,,,,,,,         @////(((&%%%%%#%#####################(((((((((((((((((((
#%#%%%%%%#####%%#%#%%#######  %%%,,,,,,  ,,.   ,,         @////(((&%%%%%%%######################(#(((#(#((((((((((
#####%%%####################  &%%......  ...   ..         @////(((&%%%%%%%###############%######((#(#(####((((((((
#######%##########%#########  %%%......  ...   ..         @////(((&%%%%%#########################(#(#######((#####
###%##%%####################  &%%...............          @////(((&%%%%%%%%##############%#######(#########((#####
#####%######################  %%%..                       @////(((&%%%%%%%################
                        &%&   %%%%%      Seatbelt         %////(((&%%%%%%%%#############*
                        &%%&&&%%%%%        v1.2.1         ,(((&%%%%%%%%%%%%%%%%%,
                         #%%%%##,


====== TokenPrivileges ======

Current Token's Privileges

                     SeIncreaseQuotaPrivilege:  DISABLED
                          SeSecurityPrivilege:  DISABLED
                     SeTakeOwnershipPrivilege:  DISABLED
                        SeLoadDriverPrivilege:  DISABLED
                     SeSystemProfilePrivilege:  DISABLED
                        SeSystemtimePrivilege:  DISABLED
              SeProfileSingleProcessPrivilege:  DISABLED
              SeIncreaseBasePriorityPrivilege:  DISABLED
                    SeCreatePagefilePrivilege:  DISABLED
                            SeBackupPrivilege:  DISABLED
                           SeRestorePrivilege:  DISABLED
                          SeShutdownPrivilege:  DISABLED
                             SeDebugPrivilege:  SE_PRIVILEGE_ENABLED
                 SeSystemEnvironmentPrivilege:  DISABLED
                      SeChangeNotifyPrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                    SeRemoteShutdownPrivilege:  DISABLED
                            SeUndockPrivilege:  DISABLED
                      SeManageVolumePrivilege:  DISABLED
                       SeImpersonatePrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                      SeCreateGlobalPrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                SeIncreaseWorkingSetPrivilege:  DISABLED
                          SeTimeZonePrivilege:  DISABLED
                SeCreateSymbolicLinkPrivilege:  DISABLED
    SeDelegateSessionUserImpersonatePrivilege:  DISABLED
```

Supondo que tenhamos o Sysmon configurado apropriadamente para registrar eventos de carregamento de imagem (ID do Evento 7), executar 'Seatbelt.exe' acionaria o carregamento de DLLs relacionadas ao .NET, como 'clr.dll' e 'mscoree.dll'. O Sysmon, observando atentamente as atividades do sistema, registrará essas operações de carregamento de DLL como registros de ID do Evento 7.

![[Pasted image 20240829142316.png]]

![[Pasted image 20240829142320.png]]

Confiar somente no Sysmon Event ID 7 para detectar ataques pode ser desafiador devido ao grande volume de eventos que ele gera. Embora ele nos informe sobre as DLLs que estão sendo carregadas, ele não fornece detalhes granulares sobre o conteúdo real do assembly .NET carregado.

Para aumentar nossa visibilidade e obter insights mais profundos sobre o assembly que está sendo carregado, podemos novamente aproveitar o Event Tracing for Windows (ETW) e, especificamente, o Microsoft-Windows-DotNETRuntimeprovedor.

Vamos usar SilkETW para coletar dados do Microsoft-Windows-DotNETRuntimeprovedor. Depois disso, podemos prosseguir para simular o ataque novamente para avaliar se ETW pode nos fornecer inteligência mais detalhada e acionável sobre o carregamento e a execução do assembly .NET 'Seatbelt'.

```cmd-session
c:\Tools\SilkETW_SilkService_v8\v8\SilkETW>SilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\windows\temp\etw.json
```

O `etw.json`arquivo (que inclui dados do `Microsoft-Windows-DotNETRuntime`provedor) parece conter muitas informações sobre o assembly carregado, incluindo nomes de métodos.

![[Pasted image 20240829142651.png]]

Vale a pena notar que em nossa configuração atual do SilkETW, não estamos capturando a totalidade dos eventos do provedor "Microsoft-Windows-DotNETRuntime". Em vez disso, estamos direcionando seletivamente um subconjunto específico (indicado por `0x2038`), que inclui: `JitKeyword`, `InteropKeyword`, `LoaderKeyword`, e `NGenKeyword`.

- O `JitKeyword`se relaciona com os eventos de compilação Just-In-Time (JIT), fornecendo informações sobre os métodos sendo compilados em tempo de execução. Isso pode ser particularmente útil para entender o fluxo de execução do assembly .NET.
- O `InteropKeyword`refere-se a eventos de Interoperabilidade, que entram em jogo quando o código gerenciado interage com o código não gerenciado. Esses eventos podem fornecer insights sobre interações potenciais com APIs nativas ou outros componentes não gerenciados.
- `LoaderKeyword`Os eventos fornecem detalhes sobre o processo de carregamento do assembly dentro do tempo de execução do .NET, o que pode ser vital para entender quais assemblies do .NET estão sendo carregados e potencialmente executados.
- Por fim, o `NGenKeyword`corresponde aos eventos Native Image Generator (NGen), que estão relacionados à criação e ao uso de assemblies .NET pré-compilados. Monitorá-los pode ajudar a detectar cenários em que invasores usam assemblies .NET pré-compilados para evitar detecções relacionadas a JIT.

Esta [postagem do blog](https://medium.com/threat-hunters-forge/threat-hunting-with-etw-events-and-helk-part-1-installing-silketw-6eb74815e4a0) fornece perspectivas valiosas sobre o SilkETW, bem como a identificação de malware baseado em .NET.




















.