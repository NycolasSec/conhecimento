A **Análise de Tráfego de Rede** é um processo essencial que envolve examinar detalhadamente o tráfego de rede para identificar comportamentos anômalos ou maliciosos, como comunicações não autorizadas ou atividades que precedem problemas na rede. Esse processo é dinâmico e depende das ferramentas disponíveis, das permissões concedidas pela organização e da visibilidade da rede.

A análise de tráfego permite dividir os dados em partes compreensíveis e comparar o tráfego atual com uma linha de base típica, ajudando a identificar desvios que possam indicar problemas. Ela é fundamental para administradores e defensores de redes, pois oferece visibilidade do ambiente, permitindo a detecção e correção de problemas rapidamente.

Ferramentas avançadas de Análise de Tráfego de Rede (NTA), combinadas com IDS/IPS, firewalls e sistemas de monitoramento como Splunk ou ELK Stack, aumentam significativamente a capacidade de identificar ações maliciosas através de alertas rápidos. Além de melhorar a segurança, a NTA auxilia nas operações diárias, como a solução de problemas de conexão e o monitoramento da infraestrutura.

Apesar da utilidade das ferramentas automatizadas, a verificação manual é igualmente importante, pois o olho humano ainda é um recurso valioso na detecção de ameaças que podem passar despercebidas por ferramentas automatizadas.

Esse processo contínuo e dinâmico é crucial para a manutenção de uma rede segura e funcional.

## Dependências de Análise

A captura e análise de tráfego de rede podem ser realizadas de duas maneiras principais: **ativa** e **passiva**. Cada abordagem tem suas próprias características e dependências:

1. **Captura e Análise Passiva**:
    - Neste método, os dados são copiados e analisados sem interagir diretamente com os pacotes de rede. A captura passiva é menos invasiva, pois não interfere no fluxo de dados.
    - Ela é ideal para monitoramento discreto e não afeta o desempenho da rede.
2. **Captura e Análise Ativa**:
    - Também conhecida como captura de tráfego in-line, essa abordagem requer uma interação direta com os pacotes. A captura ativa envolve uma abordagem mais prática, onde o tráfego é analisado em tempo real, enquanto está ocorrendo.
    - A análise ativa pode afetar o desempenho da rede, pois envolve a intervenção direta no tráfego.

A escolha entre captura passiva ou ativa depende do cenário e das necessidades específicas da rede, como a urgência da análise ou o impacto permitido sobre o desempenho da rede.

#### Dependências de captura de tráfego

| **Dependências**                          | **Passiva** | **Ativo** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------- | ----------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Permission`                              | `☑`         | `☑`       | Dependendo da organização em que estamos trabalhando, capturar dados pode ser contra a política ou até mesmo contra a lei em algumas áreas sensíveis, como saúde ou bancos. Certifique-se de sempre obter permissão por escrito de alguém com a devida autoridade para concedê-la a você. Podemos nos autodenominar hackers, mas queremos permanecer na luz legal e eticamente.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Mirrored Port`                           | `☑`         | ☐         | Uma interface de rede de switch ou roteador configurada para copiar dados de outras fontes para essa interface específica, juntamente com a capacidade de colocar sua NIC em modo promíscuo. Ter pacotes copiados para nossa porta nos permite inspecionar qualquer tráfego destinado a outros links sobre os quais normalmente não poderíamos ter visibilidade. Como as VLANs e portas de switch não encaminharão tráfego para fora de seu domínio de transmissão, temos que estar conectados ao segmento ou ter esse tráfego copiado para nossa porta específica. Ao lidar com wireless, passivo pode ser um pouco mais complicado. Devemos estar conectados ao SSID do qual desejamos capturar o tráfego. Apenas ouvir passivamente as ondas de rádio ao nosso redor nos apresentará muitos anúncios de transmissão de SSID, mas não muito mais. |
| `Capture Tool`                            | `☑`         | `☑`       | Uma maneira de ingerir o tráfego. Um computador com acesso a ferramentas como TCPDump, Wireshark, Netminer ou outras é suficiente. Tenha em mente que, ao lidar com dados PCAP, esses arquivos podem ficar muito grandes rapidamente. Cada vez que aplicamos um filtro a eles em ferramentas como Wireshark, isso faz com que o aplicativo analise esses dados novamente. Este pode ser um processo intensivo em recursos, então certifique-se de que o host tenha recursos abundantes.                                                                                                                                                                                                                                                                                                                                                             |
| `In-line Placement`                       | ☐           | `☑`       | Colocar um Tap em linha requer uma mudança de topologia na rede em que você está trabalhando. Os hosts de origem e destino não notarão nenhuma diferença no tráfego, mas, para fins de roteamento e comutação, será um próximo salto invisível pelo qual o tráfego passará em seu caminho para o destino.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `Network Tap or Host With Multiple NIC's` | ☐           | `☑`       | Um computador com duas NICs, ou um dispositivo como um Network Tap é necessário para permitir que os dados que estamos inspecionando ainda fluam. Pense nisso como adicionar outro roteador no meio de um link. Para capturar ativamente o tráfego, duplicaremos os dados diretamente das fontes. O melhor posicionamento para um tap é em um link de camada três entre segmentos comutados. Ele permite a captura de qualquer roteamento de tráfego fora da rede local. Uma porta comutada ou segmentação de VLAN não filtra nossa visão aqui.                                                                                                                                                                                                                                                                                                     |
| `Storage and Processing Power`            | `☑`         | `☑`       | Você precisará de bastante espaço de armazenamento e poder de processamento para capturar tráfego de uma torneira. Muito mais tráfego está atravessando um link de camada três do que apenas dentro de uma LAN comutada. Pense assim; Quando capturamos tráfego passivamente dentro de uma LAN, é como despejar água em um copo de um bebedouro. É um fluxo constante, mas administrável. Capturar ativamente o tráfego de um link roteado é mais como usar uma mangueira de água para encher uma xícara de chá. Há muito mais pressão por trás do fluxo, e pode ser muito para o host processar e armazenar.                                                                                                                                                                                                                                       |

Sem uma **linha de base** clara do tráfego diário da rede, é difícil identificar o que é considerado normal ou suspeito. Durante a captura de tráfego, a quantidade de informações pode ser esmagadora, tornando a análise manual uma tarefa assustadora e demorada, pois cada comunicação precisa ser verificada para garantir que seja legítima.

Identificar rapidamente essas anomalias é crucial para mitigar possíveis violações. Entender como o tráfego flui na rede e como os protocolos se comportam é fundamental para uma detecção e resposta rápidas a intrusões. Quanto mais rápido a visibilidade é obtida, menor é o potencial de danos à rede.















































