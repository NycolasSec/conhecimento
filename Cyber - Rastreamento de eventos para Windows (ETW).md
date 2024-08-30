Na detecção de ameaças e resposta a incidentes, muitas vezes dependemos de dados de log limitados, ignorando o potencial do **Event Tracing for Windows (ETW)**. Essa ferramenta poderosa pode fornecer insights abrangentes e detalhados, mas é subutilizada devido à falta de conscientização sobre suas capacidades.

O **Event Tracing for Windows (ETW)** é um mecanismo de rastreamento de eventos altamente eficiente, integrado ao Windows, que fortalece as capacidades de defesa. Ele permite a geração, coleta e análise dinâmica de eventos do sistema, criando logs detalhados e em tempo real que cobrem uma ampla gama de atividades.

Utilizando o **ETW** de forma eficaz, podemos acessar uma vasta gama de telemetria que vai além das limitações dos dados de log tradicionais. O ETW captura eventos variados, como chamadas de sistema, criação e término de processos, atividade de rede, e modificações em arquivos e registros, criando uma visão detalhada da atividade do sistema. Isso oferece um contexto valioso para identificar comportamentos anômalos, detectar possíveis incidentes de segurança e facilitar investigações forenses.

A versatilidade e extensibilidade do ETW são ainda mais acentuadas por sua integração perfeita com Event Providers. Esses componentes especializados geram tipos específicos de eventos e podem ser perfeitamente incorporados em aplicativos, componentes do sistema operacional ou software de terceiros.

Notavelmente, a natureza leve e o impacto mínimo no desempenho do ETW o tornam uma solução de telemetria ideal para monitoramento em tempo real e avaliação contínua de segurança.

Ao habilitar e configurar seletivamente provedores de eventos relevantes, podemos ajustar finamente o escopo da coleta de dados para alinhar com nossos objetivos de segurança específicos, atingindo um equilíbrio harmonioso entre a riqueza de informações e as considerações de desempenho do sistema.

Além disso, a existência de ferramentas e utilitários robustos, incluindo o Message Analyzer da Microsoft e o `Get-WinEvent`cmdlet do PowerShell, simplifica muito a recuperação, análise e análise de logs do ETW. Essas ferramentas oferecem recursos avançados de filtragem, mecanismos de correlação de eventos e recursos de monitoramento em tempo real, capacitando os membros da equipe azul a extrair insights acionáveis ​​do vasto conjunto de informações capturadas pelo ETW.

## Arquitetura e Componentes ETW
A arquitetura subjacente e os principais componentes do Event Tracing for Windows (ETW) são ilustrados no diagrama a seguir da [Microsoft](https://web.archive.org/web/20200725154736/https://docs.microsoft.com/en-us/archive/blogs/ntdebugging/part-1-etw-introduction-and-overview) .

![[Pasted image 20240829132541.png]]

- `Controllers`: O componente Controllers, como o próprio nome indica, assume o controle sobre todos os aspectos relacionados às operações ETW. Ele abrange funcionalidades como iniciar e encerrar sessões de rastreamento, bem como habilitar ou desabilitar provedores dentro de um rastreamento específico. As sessões de rastreamento podem estabelecer assinaturas para um ou vários provedores, concedendo assim aos provedores a capacidade de iniciar operações de registro. Um exemplo de um controlador amplamente usado é o utilitário integrado "logman.exe", que facilita o gerenciamento de atividades ETW.

No centro da arquitetura da ETW está o modelo publicar-assinar. Este modelo envolve dois componentes primários:

- `Providers`: Os provedores desempenham um papel fundamental na geração de eventos e na gravação deles nas sessões ETW designadas. Os aplicativos têm a capacidade de registrar provedores ETW, permitindo que eles gerem e transmitam vários eventos. Existem quatro tipos distintos de provedores utilizados dentro do ETW.
    - `MOF Providers`: Esses provedores são baseados em Managed Object Format (MOF) e são capazes de gerar eventos de acordo com esquemas MOF predefinidos. Eles oferecem uma abordagem flexível para geração de eventos e são amplamente usados ​​em vários cenários.
    - `WPP Providers`: Significando "Windows Software Trace Preprocessor", os provedores WPP alavancam macros e anotações especializadas dentro do código-fonte do aplicativo para gerar eventos. Esse tipo de provedor é frequentemente utilizado para propósitos de depuração e rastreamento de modo kernel de baixo nível.
    - `Manifest-based Providers`: Provedores baseados em manifesto representam uma forma mais contemporânea de provedores dentro do ETW. Eles dependem de arquivos de manifesto XML que definem a estrutura e as características dos eventos. Essa abordagem oferece flexibilidade aprimorada e facilidade de gerenciamento, permitindo geração e personalização dinâmica de eventos.
    - `TraceLogging Providers`: Os provedores TraceLogging oferecem uma abordagem simplificada e eficiente para geração de eventos. Eles aproveitam a API TraceLogging, introduzida em versões recentes do Windows, que simplifica o processo de geração de eventos com sobrecarga mínima de código.
- `Consumers`: Os consumidores assinam eventos específicos de interesse e recebem esses eventos para processamento ou análise posterior. Por padrão, os eventos são normalmente direcionados para um arquivo .ETL (Event Trace Log) para manipulação. No entanto, um cenário de consumidor alternativo envolve aproveitar os recursos da API do Windows para processar e consumir os eventos.
- `Channels`: Para facilitar a coleta e o consumo eficientes de eventos, a ETW conta com canais de eventos. Os canais de eventos atuam como contêineres lógicos para organizar e filtrar eventos com base em suas características e importância. A ETW oferece suporte a vários canais, cada um com seu próprio propósito e público definidos. Os consumidores de eventos podem se inscrever seletivamente em canais específicos para receber eventos relevantes para seus respectivos casos de uso.
- `ETL files`: O ETW fornece suporte especializado para gravar eventos em disco por meio do uso de arquivos de log de rastreamento de eventos, comumente chamados de "arquivos ETL". Esses arquivos servem como armazenamento durável para eventos, permitindo análise offline, arquivamento de longo prazo e investigações forenses. O ETW permite rotação e gerenciamento contínuos de arquivos ETL para garantir utilização eficiente do armazenamento.

**Notas** :
- O ETW oferece suporte a provedores de eventos no modo kernel e no modo usuário.
- Alguns provedores de eventos geram um volume significativo de eventos, o que pode potencialmente sobrecarregar os recursos do sistema se eles estiverem constantemente ativos. Como resultado, para evitar o consumo desnecessário de recursos, esses provedores são normalmente desabilitados por padrão e são habilitados somente quando uma sessão de rastreamento solicita especificamente sua ativação.
- Além de seus recursos inerentes, o ETW pode ser estendido por meio de provedores de eventos personalizados.
- [Somente eventos do provedor ETW que tenham uma propriedade Channel aplicada a eles podem ser consumidos pelo log de eventos](https://medium.com/threat-hunters-forge/threat-hunting-with-etw-events-and-helk-part-1-installing-silketw-6eb74815e4a0)
## Interagindo com ETW
`Logman`é um utilitário pré-instalado para gerenciar Event Tracing for Windows (ETW) e Event Tracing Sessions. Isto é particularmente útil ao determinar quais sessões são definidas para coleta de dados ou ao iniciar sua própria coleta de dados.

Empregar o -etsparâmetro permitirá uma investigação direta das sessões de rastreamento de eventos, fornecendo insights sobre sessões de rastreamento de todo o sistema. Como exemplo, as Sysmon Event Tracing Sessions podem ser encontradas no final das informações exibidas.

```cmd-session
C:\Tools> logman.exe query -ets

Data Collector Set                      Type                          Status
-------------------------------------------------------------------------------
Circular Kernel Context Logger          Trace                         Running
Eventlog-Security                       Trace                         Running
DiagLog                                 Trace                         Running
Diagtrack-Listener                      Trace                         Running
EventLog-Application                    Trace                         Running
EventLog-Microsoft-Windows-Sysmon-Operational Trace                         Running

<SNIP>

The command completed successfully.
```

Quando examinamos uma Sessão de Rastreamento de Eventos diretamente, descobrimos detalhes específicos da sessão, incluindo o Nome, Tamanho Máximo do Log, Localização do Log e os provedores inscritos. Descobrir uma sessão que registra provedores relevantes para seus interesses pode fornecer logs cruciais para uma investigação.

Observe que o parâmetro “-ets” é vital para o comando. Sem ele, o Logman não identificará a Event Tracing Session.

Para cada provedor inscrito na sessão, podemos adquirir dados críticos:
- `Name / Provider GUID`: Este é o identificador exclusivo do provedor.
- `Level`: Isso descreve o nível do evento, indicando se ele está filtrando por aviso, informativo, crítico ou todos os eventos.
- `Keywords Any`: Palavras-chave criam um filtro com base no tipo de evento gerado pelo provedor.

```cmd-session
C:\Tools> logman.exe query "EventLog-System" -ets


Name:                 EventLog-System
Status:               Running
Root Path:            %systemdrive%\PerfLogs\Admin
Segment:              Off
Schedules:            On
Segment Max Size:     100 MB

Name:                 EventLog-System\EventLog-System
Type:                 Trace
Append:               Off
Circular:             Off
Overwrite:            Off
Buffer Size:          64
Buffers Lost:         0
Buffers Written:      47
Buffer Flush Timer:   1
Clock Type:           System
File Mode:            Real-time

Provider:
Name:                 Microsoft-Windows-FunctionDiscoveryHost
Provider Guid:        {538CBBAD-4877-4EB2-B26E-7CAEE8F0F8CB}
Level:                255
KeywordsAll:          0x0
KeywordsAny:          0x8000000000000000 (System)
Properties:           65
Filter Type:          0

--- SNIP ---

The command completed successfully.
```

Usando o `logman query providers`comando, podemos gerar uma lista de todos os provedores disponíveis no sistema, incluindo seus respectivos GUIDs.

```cmd-session
C:\Tools> logman.exe query providers

Provider                                 GUID
-------------------------------------------------------------------------------
ACPI Driver Trace Provider               {DAB01D4D-2D48-477D-B1C3-DAAD0CE6F06B}
Active Directory Domain Services: SAM    {8E598056-8993-11D2-819E-0000F875A064}
Active Directory: Kerberos Client        {BBA3ADD2-C229-4CDB-AE2B-57EB6966B0C4}
Active Directory: NetLogon               {F33959B4-DBEC-11D2-895B-00C04F79AB69}
ADODB.1                                  {04C8A86F-3369-12F8-4769-24E484A9E725}
ADOMD.1                                  {7EA56435-3F2F-3F63-A829-F0B35B5CAD41}
Application Popup                        {47BFA2B7-BD54-4FAC-B70B-29021084CA8F}
Application-Addon-Event-Provider         {A83FA99F-C356-4DED-9FD6-5A5EB8546D68}
ATA Port Driver Tracing Provider         {D08BD885-501E-489A-BAC6-B7D24BFE6BBF}
AuthFw NetShell Plugin                   {935F4AE6-845D-41C6-97FA-380DAD429B72}                              {24722B88-DF97-

<SNIP>

The command completed successfully.
```

O Windows 10 inclui mais de 1.000 provedores integrados. Além disso, o Software de Terceiros frequentemente incorpora seus próprios provedores ETW, especialmente aqueles operando no modo Kernel.

Devido ao alto número de provedores, geralmente é vantajoso filtrá-los usando findstr. Por exemplo, você verá vários resultados para "Winlogon" no exemplo fornecido.

```cmd-session
C:\Tools> logman.exe query providers | findstr "Winlogon"
Microsoft-Windows-Winlogon               {DBE9B383-7CF3-4331-91CC-A3CB16A3B538}
Windows Winlogon Trace                   {D451642C-63A6-11D7-9720-00B0D03E0347}
```

Ao especificar um provedor com Logman, obtemos um entendimento mais profundo da função do provedor. Isso nos informará sobre as palavras-chave que podemos filtrar, os níveis de evento disponíveis e quais processos estão atualmente utilizando o provedor.

```cmd-session
C:\Tools> logman.exe query providers Microsoft-Windows-Winlogon

Provider                                 GUID
-------------------------------------------------------------------------------
Microsoft-Windows-Winlogon               {DBE9B383-7CF3-4331-91CC-A3CB16A3B538}

Value               Keyword              Description
-------------------------------------------------------------------------------
0x0000000000010000  PerfInstrumentation
0x0000000000020000  PerfDiagnostics
0x0000000000040000  NotificationEvents
0x0000000000080000  PerfTrackContext
0x0000100000000000  ms:ReservedKeyword44
0x0000200000000000  ms:Telemetry
0x0000400000000000  ms:Measures
0x0000800000000000  ms:CriticalData
0x0001000000000000  win:ResponseTime     Response Time
0x0080000000000000  win:EventlogClassic  Classic
0x8000000000000000  Microsoft-Windows-Winlogon/Diagnostic
0x4000000000000000  Microsoft-Windows-Winlogon/Operational
0x2000000000000000  System               System

Value               Level                Description
-------------------------------------------------------------------------------
0x02                win:Error            Error
0x03                win:Warning          Warning
0x04                win:Informational    Information

PID                 Image
-------------------------------------------------------------------------------
0x00001710
0x0000025c


The command completed successfully.
```

As palavras-chave `Microsoft-Windows-Winlogon/Diagnostic`e `Microsoft-Windows-Winlogon/Operational`fazem referência aos logs de eventos gerados por este provedor.

Alternativas baseadas em GUI também existem. Estas são:

1. Usando a interface gráfica da ferramenta `Performance Monitor`, podemos visualizar várias sessões de trace em execução. Uma visão geral detalhada de um trace específico pode ser acessada simplesmente clicando duas vezes nele. Isso revela todos os dados pertinentes relacionados ao trace, desde os provedores envolvidos e seus recursos ativados até a natureza do trace em si. Além disso, essas sessões podem ser modificadas para atender às nossas necessidades, incorporando ou eliminando provedores. Por fim, podemos elaborar novas sessões optando pela categoria "Definido pelo Usuário".
	- ![[Pasted image 20240829134657.png]]
	- ![[Pasted image 20240829134729.png]]
2. Os metadados do provedor ETW também podem ser visualizados por meio do [projeto EtwExplorer](https://github.com/zodiacon/EtwExplorer) .
	- ![[Pasted image 20240829134910.png]]

## Provedores Úteis

- `Microsoft-Windows-Kernel-Process`: Este provedor ETW é instrumental no monitoramento de atividades relacionadas a processos dentro do kernel do Windows. Ele pode auxiliar na detecção de comportamentos incomuns de processos, como injeção de processos, esvaziamento de processos e outras táticas comumente usadas por malware e ameaças persistentes avançadas (APTs).
- `Microsoft-Windows-Kernel-File`: Como o nome sugere, este provedor foca em operações relacionadas a arquivos. Ele pode ser empregado para cenários de detecção envolvendo acesso não autorizado a arquivos, alterações em arquivos críticos do sistema ou operações de arquivo suspeitas indicativas de exfiltração ou atividade de ransomware.
- `Microsoft-Windows-Kernel-Network`: Este provedor ETW oferece visibilidade para a atividade relacionada à rede no nível do kernel. É especialmente útil na detecção de ataques baseados em rede, como exfiltração de dados, conexões de rede não autorizadas e sinais potenciais de comunicação de comando e controle (C2).
- `Microsoft-Windows-SMBClient/SMBServer`: Esses provedores monitoram a atividade do cliente e do servidor Server Message Block (SMB), fornecendo insights sobre compartilhamento de arquivos e comunicação de rede. Eles podem ser usados ​​para detectar padrões de tráfego SMB incomuns, potencialmente indicando movimento lateral ou exfiltração de dados.
- `Microsoft-Windows-DotNETRuntime`: Este provedor se concentra em eventos de tempo de execução do .NET, o que o torna ideal para identificar anomalias na execução de aplicativos .NET, potencial exploração de vulnerabilidades do .NET ou carregamento malicioso de assembly .NET.
- `OpenSSH`: O monitoramento do provedor OpenSSH ETW pode fornecer insights importantes sobre tentativas de conexão Secure Shell (SSH), autenticações bem-sucedidas e malsucedidas e possíveis ataques de força bruta.
- `Microsoft-Windows-VPN-Client`: Este provedor permite o rastreamento de eventos de cliente de Rede Privada Virtual (VPN). Pode ser útil para identificar conexões VPN não autorizadas ou suspeitas.
- `Microsoft-Windows-PowerShell`: Este provedor ETW rastreia a execução do PowerShell e a atividade de comandos, o que o torna inestimável para detectar uso suspeito do PowerShell, registro de blocos de script e possível uso indevido ou exploração.
- `Microsoft-Windows-Kernel-Registry`: Este provedor monitora operações de registro, o que o torna útil para cenários de detecção relacionados a alterações em chaves de registro, geralmente associadas a mecanismos de persistência, instalação de malware ou alterações na configuração do sistema.
- `Microsoft-Windows-CodeIntegrity`: Este provedor monitora verificações de integridade de código e driver, que podem ser essenciais para identificar tentativas de carregar drivers ou códigos não assinados ou maliciosos.
- `Microsoft-Antimalware-Service`: Este provedor ETW pode ser empregado para detectar possíveis problemas com o serviço antimalware, incluindo serviços desabilitados, alterações de configuração ou possíveis técnicas de evasão empregadas por malware.
- `WinRM`: O monitoramento do provedor de Gerenciamento Remoto do Windows (WinRM) pode revelar atividades de gerenciamento remoto não autorizadas ou suspeitas, geralmente indicativas de movimento lateral ou execução de comando remoto.
- `Microsoft-Windows-TerminalServices-LocalSessionManager`: Este provedor rastreia sessões locais dos Serviços de Terminal, o que o torna útil para detectar atividades de desktop remoto não autorizadas ou suspeitas.
- `Microsoft-Windows-Security-Mitigations`: Este provedor mantém o controle sobre a eficácia e as operações de mitigações de segurança em vigor. É essencial para identificar potenciais tentativas de desvio desses controles de segurança.
- `Microsoft-Windows-DNS-Client`: Este provedor ETW fornece visibilidade à atividade do cliente DNS, o que é crucial para detectar ataques baseados em DNS, incluindo tunelamento de DNS ou solicitações DNS incomuns que podem indicar comunicação C2.
- `Microsoft-Antimalware-Protection`: Este provedor monitora as operações dos mecanismos de proteção antimalware. Ele pode ser usado para detectar quaisquer problemas com esses mecanismos, como recursos de proteção desabilitados, alterações de configuração ou sinais de técnicas de evasão empregadas por agentes maliciosos.

## Provedores restritos
Certos provedores ETW são considerados "restritos". Eles oferecem telemetria valiosa, mas são acessíveis apenas a processos que carregam as permissões necessárias. Essa exclusividade é projetada para garantir que dados sensíveis do sistema permaneçam protegidos de ameaças potenciais.

Um desses provedores restritos e de alto valor é o `Microsoft-Windows-Threat-Intelligence`. Esse provedor oferece insights cruciais sobre potenciais ameaças à segurança e é frequentemente aproveitado em operações de Digital Forensics and Incident Response (DFIR). No entanto, para acessar esse provedor, os processos devem ser privilegiados com um direito específico, conhecido como Protected Process Light (PPL)

De acordo com a Elastic: _Para poder ser executado como um PPL, um fornecedor antimalware deve se inscrever na Microsoft, provar sua identidade, assinar documentos legais vinculativos, implementar um driver Early Launch Anti-Malware (ELAM), executá-lo em um conjunto de testes e enviá-lo à Microsoft para uma assinatura especial do Authenticode. Não é um processo trivial._
_Depois que esse processo for concluído, o fornecedor pode usar esse driver ELAM para que o Windows proteja seu serviço antimalware executando-o como um PPL._ Dito isso, existem [soluções alternativas para acessar o `Microsoft-Windows-Threat-Intelligence`provedor](https://posts.specterops.io/uncovering-windows-events-b4b9db7eac54)
No contexto do **Microsoft-Windows-Threat-Intelligence**, o **ETW** oferece benefícios significativos, permitindo a captura de dados detalhados sobre ameaças potenciais. Isso ajuda profissionais de segurança a detectar e analisar ataques sofisticados que poderiam passar despercebidos por outras defesas.

A telemetria do ETW é essencial em investigações forenses, revelando a origem, interações e alterações de uma ameaça. Além disso, o monitoramento em tempo real permite a identificação e mitigação de ameaças em andamento. A próxima etapa é utilizar o ETW para investigar ataques que o Sysmon pode não detectar devido a suas limitações na captura de certos eventos.

## Referências

- [https://nasbench.medium.com/a-primer-on-event-tracing-for-windows-etw-997725c082bf](https://nasbench.medium.com/a-primer-on-event-tracing-for-windows-etw-997725c082bf)
- [https://bmcder.com/blog/a-begginers-all-inclusive-guide-to-etw](https://web.archive.org/web/20230222121234/https://bmcder.com/blog/a-begginers-all-inclusive-guide-to-etw)












































































