## Noções básicas sobre registro de eventos do Windows
`Windows Event Logs` armazena logs de diferentes componentes do sistema, incluindo o próprio sistema, aplicativos em execução nele, provedores de ETW, serviços e outros.

Oferece recursos abrangentes de log para erros de aplicativo eventos de segurança e informações de diagnóstico.

São categorizados em diferentes logs de eventos, como "Aplicativo", "Sistema", "Segurança" e outros, para organizar eventos com base em sua origem ou finalidade.

Podem ser acessados usando o aplicativo `Event Viewer` ou programaticamente usando APIs como a API de Log de Eventos do Windows. 

Acessando como administrador temos mais acesso as informações
![[image42.webp]]

![[image1.webp]]

Os logs de eventos padrão do Windows consistem em `Application`, `Security`, `Setup`, `System`e `Forwarded Events`.

Os quatro primeiros logs abrangem erros de aplicativo, eventos de segurança, atividades de configuração do sistema e informações gerais do sistema.

A seção "Forwarded Events" é única, exibindo dados de log de eventos encaminhados de outras máquinas.

O Visualizador de Eventos do Windows tem a capacidade de abrir e exibir arquivos `.evtx`, que podem ser encontrados na seção "Logs salvos".

![[image89.webp]]

## Anatomia de um log de eventos

Ao examinar `Application` logs, encontramos dois níveis distintos de eventos: `information` e `error`.

![[image2.webp]]

**`Information`** : Fornecem detalhes de uso sobre o aplicativo, como seus eventos de início ou parada.
**`Error`** : Destacam erros específicos e frequentemente oferecem insights detalhados sobre os problemas encontrados.

![[image3.webp]]

Cada entrada no Log de Eventos do Windows é um "Evento" e contém os seguintes componentes principais:

1. `Log Name`: O nome do log de eventos (por exemplo, Aplicativo, Sistema, Segurança, etc.).
2. `Source`: O software que registrou o evento.
3. `Event ID`: Um identificador exclusivo para o evento.
4. `Task Category`: Isso geralmente contém um valor ou nome que pode nos ajudar a entender o propósito ou uso do evento.
5. `Level`: A gravidade do evento (Informação, Aviso, Erro, Crítico e Detalhado).
6. `Keywords`: Palavras-chave são sinalizadores que nos permitem categorizar eventos de maneiras além das outras opções de classificação. Essas são geralmente categorias amplas, como "Sucesso de Auditoria" ou "Falha de Auditoria" no log de Segurança.
7. `User`: A conta de usuário que estava conectada quando o evento ocorreu.
8. `OpCode`: Este campo pode identificar a operação específica que o evento relata.
9. `Logged`: A data e a hora em que o evento foi registrado.
10. `Computer`: O nome do computador onde o evento ocorreu.
11. `XML Data`:Todas as informações acima também estão incluídas em um formato XML junto com dados adicionais do evento.

`Keywords` é útil ao filtrar logs de eventos para tipos específicos de eventos. Aumenta significativamente a precisão das nossas consultas.

Ao olhar o log de eventos acima, temos elementos cruciais. O `Event ID` no canto superior esquerdo serve como identificador exclusivo, que pode ser pesquisado no site da Microsoft para obter mais informações.

O rótulo "SideBySide" ao lado do ID do evento representa a fonte do evento. Ao clicar nos detalhes, podemos analisar melhor o impacto do evento usando XML ou uma visualização bem formatada.

![[image4.webp]]

Além disso, podemos extrair informações complementares do log de eventos, como o ID do processo onde o erro ocorreu, permitindo uma análise mais precisa.

![[image5.webp]]

Mudando nosso foco para logs de segurança, vamos considerar o ID do evento 4624, um evento que ocorre comumente (detalhado em [https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4624) ).

![[image6.webp]]

De acordo com a Microsoft, esse evento se refere a criação de uma sessão de logon na máquina de destino. Nesse log achamos detalhes cruciais como o 

**`Logon ID`** : Nos permite correlacionar esse logon com outros eventos que compartilham o mesmo "Logon ID".
**`Logon Type`** : Indica o tipo de logon

Uma investigação mais aprofundada é necessária para determinar o serviço específico envolvido, utilizando técnicas de correlação com dados adicionais como o "Logon ID".

## Aproveitando consultas XML personalizadas

Podemos criar consultas XML personalizadas para identificar eventos relacionados usando o "Logon ID" como ponto de partida.

Em "Filtrar Log Atual" -> "XML" -> "Editar Consulta Manualmente", obtemos acesso a uma linguagem de consulta XML personalizada que permite pesquisas de log mais granulares.

![[image7.webp]]

Na consulta de exemplo, focamos em eventos que contêm o campo "SubjectLogonId" com um valor de "0x3E7".

A seleção desse valor decorre da necessidade de correlacionar eventos associados a um "Logon ID" específico e entender os detalhes relevantes dentro desses eventos.

![[image8.webp]]

Se for necessária assistência na elaboração da consulta, filtros automáticos podem ser habilitados, permitindo a exploração de seu impacto na representação XML.

Microsoft oferece artigos informativos sobre [filtragem XML avançada no Windows Event Viewer](https://techcommunity.microsoft.com/t5/ask-the-directory-services-team/advanced-xml-filtering-in-the-windows-event-viewer/ba-p/399761) 

Ao construir tais consultas, podemos restringir nosso foco à conta responsável por iniciar o serviço e eliminar detalhes desnecessários. No entanto, mesmo com esse refinamento, a quantidade de dados continua significativa.

Aprofundar-se nos detalhes do log revela progressivamente uma narrativa. Por exemplo, a análise começa com o [Event ID 4907](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4907) , que significa uma mudança na política de auditoria.

![[image9.webp]]

Na [descrição do evento](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4907) , encontramos informações valiosas, como "Este evento é gerado quando o SACL de um objeto (por exemplo, uma chave de registro ou arquivo) foi alterado".

Caso não esteja familiarizado com SACL, consultar o link fornecido ( [https://docs.microsoft.com/en-us/windows/win32/secauthz/access-control-lists](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-control-lists) ) esclarece as listas de controle de acesso (ACLs). "S" em SACL denota uma lista de controle de acesso do sistema, que permite que os administradores registrem tentativas de acesso para proteger objetos.

Cada Entrada de Controle de Acesso (ACE) dentro de uma SACL especifica os tipos de tentativas de acesso por um administrador designado que acionam a geração de registros no log de eventos de segurança. As ACEs em uma SACL podem gerar registros de auditoria em tentativas de acesso com falha, bem-sucedidas ou ambos os tipos. Para obter mais informações sobre SACLs, consulte [Geração de Auditoria](https://learn.microsoft.com/en-us/windows/win32/secauthz/audit-generation) e [Direito de Acesso SACL](https://learn.microsoft.com/en-us/windows/win32/secauthz/sacl-access-right) ."

Com base nessas informações, fica aparente que as permissões de um arquivo foram alteradas para modificar o registro ou auditoria de tentativas de acesso. Uma exploração mais aprofundada dos detalhes do evento revela aspectos intrigantes adicionais.

![[image10.webp]]

O processo responsável pela alteração é identificado como "SetupHost.exe", indicando um potencial processo de configuração (embora valha a pena notar que o malware pode às vezes se disfarçar sob nomes legítimos).

O nome do objeto impactado parece ser o "bootmanager", e podemos examinar os novos e antigos descritores de segurança ("NewSd" e "OldSd") para identificar as alterações. A compreensão do significado de cada campo no descritor de segurança pode ser realizada por meio de referências como o artigo [ACE Strings](https://docs.microsoft.com/en-us/windows/win32/secauthz/ace-strings?redirectedfrom=MSDN) e [Understanding SDDL Syntax](https://itconnect.uw.edu/wares/msinf/other-help/understanding-sddl-syntax/) .

Dos eventos observados, podemos inferir que ocorreu um processo de configuração, envolvendo a criação de um novo arquivo e a configuração inicial de permissões de segurança para fins de auditoria. Posteriormente, encontramos o evento de logon, seguido por um evento de "logon especial".

![[image11.webp]]

Ao analisar o evento de logon especial, obtemos insights sobre as permissões de token concedidas ao usuário após um logon bem-sucedido.

![[image12.webp]]

Uma lista abrangente de privilégios pode ser encontrada na documentação sobre [constantes de privilégios](https://docs.microsoft.com/en-us/windows/win32/secauthz/privilege-constants) . Por exemplo, o privilégio "SeDebugPrivilege" indica que o usuário possui a habilidade de adulterar memória que não lhe pertence.

Aqui está uma boa lista de logs úteis do windows : [[Cyber - Lista de logs de eventos úteis do Windows]]

Lembre-se, um dos principais aspectos da detecção de ameaças é ter uma boa compreensão do que é "normal" em nosso ambiente. Anomalias que podem indicar uma ameaça em um ambiente podem ser comportamento normal em outro.

É crucial ajustar nossos sistemas de monitoramento e alerta ao nosso ambiente para minimizar falsos positivos e tornar ameaças reais mais fáceis de detectar. Além disso, é essencial ter uma solução de gerenciamento de log centralizada em vigor que possa coletar, analisar e alertar sobre esses eventos em tempo real.

Monitorar e revisar regularmente esses logs pode ajudar na detecção precoce e mitigação de ameaças. Por fim, precisamos ter certeza de correlacionar esses logs com outros logs de sistema e segurança para obter uma visão mais holística dos eventos de segurança em nosso ambiente.

[EventData[Data[@Name='ObjectName'='C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll']]



C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll

























































































































































































