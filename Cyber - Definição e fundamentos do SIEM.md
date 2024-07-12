
# Cyber - Definição e fundamentos do SIEM


## O que é SIEM?

O Security Information and Event Management (SIEM) integra o gerenciamento de dados de segurança com a supervisão de eventos de segurança, permitindo avaliações em tempo real de alertas gerados por hardware e aplicativos de rede.

As ferramentas SIEM oferecem funcionalidades como coleta e administração de eventos de log, análise de eventos e dados adicionais de várias fontes, além de recursos operacionais como tratamento de incidentes, visualizações resumidas e documentação.

Ao utilizar soluções SIEM, equipes de TI podem detectar ataques cibernéticos em tempo real ou mesmo antes de sua ocorrência, aumentando a velocidade de resposta durante a resolução de incidentes. Por isso, o SIEM desempenha um papel crucial na eficácia e supervisão contínua da segurança da informação de uma empresa, servindo como base para as estratégias de segurança ao oferecer um método abrangente para identificar e gerenciar ameaças potenciais.

## A evolução da tecnologia SIEM

A sigla "SIEM" (Security Information and Event Management) surgiu em 2005, através da colaboração de dois analistas da Gartner que integraram duas tecnologias anteriores: Security Information Management (SIM) e Security Event Management (SEM). O SIM, de primeira geração, baseava-se na coleta e análise de logs, incorporando inteligência de ameaças. Já o SEM, de segunda geração, focava na consolidação, correlação e notificação de eventos de segurança de diferentes dispositivos, como antivírus, firewalls e IDS. Com o tempo, as capacidades de SIM e SEM foram combinadas, formando o SIEM, que oferece uma abordagem completa para detectar e gerenciar ameaças, acumulando, preservando e examinando logs e eventos de várias fontes.


## Como funciona uma solução SIEM?

Os sistemas SIEM (Security Information and Event Management) reúnem dados de diversas fontes, como PCs, dispositivos de rede e servidores, padronizando-os e consolidando-os para facilitar a análise. Especialistas em segurança examinam esses dados para identificar possíveis ameaças, permitindo que as empresas detectem violações de segurança e analisem alertas, fornecendo informações importantes sobre a segurança da organização.

Alertas são gerados para notificar a equipe de Operações de Segurança/Monitoramento sobre eventos ou incidentes de segurança. Esses alertas, que podem ser enviados por e-mail, mensagens pop-up, mensagens de texto ou chamadas telefônicas, informam a equipe sobre ataques específicos aos sistemas da organização.

Devido ao grande volume de eventos monitorados, os sistemas SIEM geram muitos alertas, variando de centenas a milhares por hora. Ajustar o SIEM para detectar e alertar sobre eventos de alto risco é essencial.

O SIEM se destaca por sua capacidade de identificar eventos de alto risco com precisão, diferentemente de outras ferramentas de monitoramento como Intrusion Prevention Systems (IPS) ou Intrusion Detection Systems (IDS). Ele não substitui esses dispositivos, mas opera em conjunto com eles, processando e integrando seus dados para reconhecer eventos que podem levar à exploração do sistema. Ao consolidar dados de várias fontes, as soluções SIEM oferecem uma abordagem abrangente para a detecção e gerenciamento de ameaças.

## Requisitos de negócios e casos de uso do SIEM

#### Agregação e Normalização de Log
A visibilidade de ameaças proporcionada pela consolidação de logs em sistemas SIEM é crucial para a segurança cibernética de uma organização. Sem ela, a segurança seria praticamente inútil. A consolidação de logs envolve a coleta de grandes volumes de informações de segurança de firewalls, bancos de dados e aplicativos essenciais, permitindo que a equipe do SOC (Centro de Operações de Segurança) analise esses dados e identifique conexões importantes, melhorando a detecção de ameaças.

Com a consolidação de logs do SIEM, a equipe do SOC pode identificar e analisar incidentes de segurança em toda a infraestrutura de TI da organização. Centralizando e correlacionando informações de várias fontes, o SIEM oferece uma abordagem abrangente para a detecção e gestão de ameaças. Isso permite que as organizações reconheçam padrões, tendências e anomalias que podem indicar riscos à segurança, possibilitando uma reação rápida e eficiente a incidentes de segurança, minimizando seus impactos.

#### Alerta de ameaça

Ter uma solução SIEM que possa identificar e notificar a equipe de segurança de TI sobre possíveis ameaças no grande volume de dados de eventos de segurança é essencial. Esse recurso permite investigações mais rápidas e direcionadas, bem como uma resposta eficiente a incidentes de segurança.

As soluções SIEM utilizam análises avançadas e inteligência de ameaças para reconhecer ameaças potenciais e gerar alertas em tempo real. Quando uma ameaça é detectada, o sistema envia alertas à equipe de segurança de TI, fornecendo detalhes necessários para investigar e mitigar o risco de forma eficaz. Ao alertar prontamente a equipe de segurança, as soluções SIEM ajudam a minimizar o impacto de incidentes de segurança e a proteger os ativos essenciais da organização.

#### Contextualização e resposta

Gerar alertas não é suficiente para uma solução SIEM eficaz. Se uma solução SIEM envia alertas para cada evento de segurança possível, a equipe de segurança de TI pode ficar sobrecarregada com o grande volume de alertas e enfrentar muitos falsos positivos, especialmente em soluções mais antigas. Por isso, a contextualização de ameaças é crucial. Ela permite classificar alertas, determinar os atores envolvidos, as partes afetadas da rede e o momento do evento.

A contextualização ajuda a equipe de segurança a identificar ameaças genuínas e agir rapidamente. Processos automatizados de configuração podem filtrar algumas dessas ameaças, reduzindo o número de alertas recebidos pela equipe.

Uma solução SIEM ideal deve permitir que uma empresa gerencie ameaças diretamente, interrompendo operações durante as investigações, se necessário. Essa abordagem minimiza o impacto de incidentes de segurança e protege ativos críticos. As soluções SIEM fornecem contexto e automatizam a filtragem de ameaças, permitindo que as equipes de segurança se concentrem em ameaças reais, reduzindo a fadiga de alertas e aumentando a eficiência e eficácia na resposta a incidentes.

#### Conformidade

As soluções SIEM são essenciais para a conformidade regulatória, ajudando organizações a atender requisitos de normas como PCI DSS, HIPAA e GDPR por meio da detecção e gerenciamento abrangente de ameaças.

Esses regulamentos exigem medidas de segurança robustas, incluindo monitoramento e análise em tempo real do tráfego de rede. As soluções SIEM permitem que as equipes SOC (Centro de Operações de Segurança) detectem e respondam rapidamente a incidentes de segurança, auxiliando no cumprimento dessas exigências.

Além disso, as soluções SIEM oferecem recursos automatizados de relatórios e auditoria, essenciais para a conformidade. Esses recursos permitem que as organizações produzam relatórios de conformidade de maneira rápida e precisa, garantindo que atendam aos requisitos regulatórios e possam demonstrar conformidade a auditores e reguladores.

## Fluxos de dados dentro de um SIEM

Vamos agora ver brevemente como os dados trafegam dentro de um SIEM, até que estejam prontos para análise.

1. Soluções SIEM ingerem logs de várias fontes de dados. Cada ferramenta SIEM possui capacidades únicas para coletar logs de diferentes fontes. Esse processo é conhecido como ingestão de dados ou coleta de dados.
2. Os dados coletados são processados e normalizados para serem compreendidos pelo mecanismo de correlação do SIEM. Isso envolve escrever ou ler os dados em um formato que o SIEM possa interpretar e convertê-los em um formato comum para diferentes tipos de conjuntos de dados, um processo conhecido como normalização e agregação de dados.
3. A parte mais crucial do SIEM é onde as equipes do SOC utilizam os dados normalizados para criar regras de detecção, painéis, visualizações, alertas e gerenciar incidentes. Isso permite que o SOC identifique potenciais riscos de segurança e responda rapidamente a incidentes de segurança.

## Benefícios de usar uma solução SIEM

Implantar um sistema de Gerenciamento de Informações e Eventos de Segurança (SIEM) oferece vantagens significativas que superam os riscos potenciais de não tê-lo, desde que o controle de segurança esteja protegendo algo de maior importância.

Sem um SIEM, o pessoal de TI não possui uma visão centralizada de todos os logs e eventos, o que pode resultar na negligência de eventos cruciais e na acumulação de um grande volume de eventos aguardando investigação. Por outro lado, um SIEM bem calibrado melhora o processo de resposta a incidentes, aumentando a eficiência e proporcionando um painel centralizado para notificações baseadas em categorias pré-definidas e limites de eventos.

Por exemplo, se um firewall registra cinco tentativas de login incorretas sucessivas, resultando no bloqueio da conta de administrador, um sistema de registro centralizado que correlaciona todos os registros é necessário para monitorar a situação. Da mesma forma, um software de filtragem da web que detecta um computador conectando-se a um site malicioso 100 vezes em uma hora pode ser visualizado e gerenciado em uma única interface usando um SIEM.

Os SIEMs modernos frequentemente incluem inteligência integrada capaz de detectar limites configuráveis ​​e eventos dentro de prazos específicos, além de oferecer resumos e relatórios personalizáveis. Além disso, os SIEMs mais avançados estão integrando inteligência artificial para notificações baseadas em análise comportamental e de padrões.

Os recursos de relatórios e notificações de um SIEM capacitam a equipe de TI a reagir rapidamente a incidentes em potencial, destacando sua capacidade de identificar ataques maliciosos antes que ocorram. Essa inteligência pode reduzir os custos associados a grandes violações de segurança, evitando danos financeiros e de reputação significativos para as organizações.

Muitas organizações regulamentadas, como bancos, instituições financeiras, seguradoras e empresas de saúde, são obrigadas a implementar um SIEM, seja localmente ou na nuvem. Os SIEMs fornecem evidências de monitoramento contínuo dos sistemas, revisão e conformidade com políticas de retenção de logs, cumprindo padrões como ISO e HIPAA.



































