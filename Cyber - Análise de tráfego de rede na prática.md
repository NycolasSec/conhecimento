## Análise descritiva

Ela serve para descrever um conjunto de dados com base em características individuais. Ela ajuda a detectar possíveis erros na coleta de dados e/ou outliers no conjunto de dados.

1. `What is the issue?`
    - Suspeita de violação? Problema de rede?
2. `Define our scope and the goal. (what are we looking for? which time period?)`
    - Alvo: vários hosts potencialmente baixando um arquivo malicioso de bad.example.com
    - Quando: nas últimas 48 horas + 2 horas a partir de agora.
    - Informações de suporte: nomes de arquivo/tipos 'superbad.exe' 'new-crypto-miner.exe'
3. `Define our target(s) (net / host(s) / protocol)`
    - Escopo: rede 192.168.100.0/24, os protocolos utilizados foram HTTP e FTP.

Usando nosso fluxo de trabalho, determinaremos nosso problema, o que estamos procurando, quando e onde encontrá-lo. A análise descritiva abrange esses conceitos críticos para nossa análise.

## Análise diagnóstica
A análise diagnóstica esclarece as causas, efeitos e interações das condições. Ao fazer isso, ela fornece insights que são obtidos por meio de correlações e interpretações. 

Característica aqui é uma visão retrospectiva, como na análise descritiva intimamente relacionada, com a sutil diferença de que ela tenta encontrar razões para eventos e desenvolvimentos.

4. `Capture network traffic`
    - Conecte-se a um link com acesso à rede 192.168.100.0/24 para capturar tráfego ao vivo e tentar pegar um dos executáveis ​​em transferência. Veja se um administrador pode extrair dados PCAP e/ou netflow do nosso SIEM para os dados históricos.
5. `Identification of required network traffic components (filtering)`
    - Assim que tivermos tráfego, filtre quaisquer pacotes não necessários para esta investigação para incluir; qualquer tráfego que corresponda à nossa linha de base comum e mantenha qualquer coisa relevante para o escopo da investigação. Por exemplo, HTTP e FTP da sub-rede, qualquer coisa transferindo ou contendo uma solicitação GET para os arquivos executáveis ​​suspeitos.
6. `An understanding of captured network traffic`
    - Depois de filtrar o ruído, é hora de cavar para nossos alvos — filtrar coisas como `ftp-data`encontrar quaisquer arquivos transferidos e reconstruí-los. Para HTTP, podemos filtrar `http.request.method == "GET"`para ver quaisquer solicitações GET que correspondam aos nomes de arquivo que estamos procurando. Isso pode nos mostrar quem adquiriu os arquivos e potencialmente outras transferências internas para a rede nos mesmos protocolos.

## Análise preditiva
Avalia dados históricos e atuais para criar um modelo preditivo de probabilidades futuras. Com base nos resultados de análises descritivas e diagnósticas, esse método de análise de dados torna possível identificar tendências, detectar desvios de valores esperados em um estágio inicial e prever ocorrências futuras com a maior precisão possível.

7. `Note-taking and mind mapping of the found results`
    
    - Anotar tudo o que fazemos, vemos ou encontramos ao longo da investigação é crucial. Garanta que estamos tomando notas amplas, incluindo:
    
    - Períodos em que capturamos o tráfego.
    - Hosts suspeitos na rede.
    - Conversas contendo os arquivos em questão. (para incluir carimbos de data/hora e números de pacotes)
8. `Summary of the analysis (what did we find?)`
    - Por fim, resuma o que descobrimos, explicando os detalhes relevantes para que os superiores possam decidir colocar os hosts afetados em quarentena ou executar uma resposta a incidentes mais significativa.
    - Nossa análise afetará as decisões tomadas, por isso é essencial ser o mais claro e conciso possível.

Ao avaliar os dados que encontramos, comparando-os com nosso tráfego de base e dados ruins conhecidos, como marcadores de infiltração ou exploração, estamos realizando a Análise Preditiva. Nesse processo, pintamos um quadro claro para que ações apropriadas possam ser tomadas em resposta.

## Análise prescritiva
A análise prescritiva foca em identificar ações para prevenir problemas futuros ou desencadear processos específicos. Com base nos resultados de um fluxo de trabalho, decisões são tomadas para resolver e evitar a recorrência do problema. Após a solução, é importante refletir e documentar as lições aprendidas, fortalecendo processos futuros ao identificar o que funcionou, o que não ajudou e o que pode ser melhorado.

1. `What is the issue?`
    - Suspeita de violação? Problema de rede?
2. `Define our scope and the goal (what are we looking for? which time period?)`
    - alvo: vários hosts potencialmente baixando um arquivo malicioso de bad.example.com
    - quando: nas últimas 48 horas + 2 horas a partir de agora.
    - informações de suporte: nomes de arquivo/tipos 'superbad.exe' 'new-crypto-miner.exe'
3. `Define our target(s) (net / host(s) / protocol)`
    - escopo: 192.168.100.0/24 os protocolos de rede utilizados foram HTTP e FTP.
4. `Capture network traffic`
    - conecte-se a um link com acesso à rede 192.168.100.0/24 para capturar tráfego ao vivo para tentar pegar um dos executáveis ​​em transferência. Veja se um administrador pode extrair dados PCAP e/ou netflow do nosso SIEM para os dados históricos.
5. `Identification of required network traffic components (filtering)`
    - assim que tivermos tráfego, filtre qualquer tráfego que não seja necessário para esta investigação para incluir; qualquer tráfego que corresponda à nossa linha de base comum e mantenha qualquer coisa relevante para o escopo. `HTTP e FTP da sub-rede, qualquer coisa transferindo ou contendo uma solicitação GET para os arquivos executáveis ​​suspeitos.
6. `An understanding of captured network traffic`
    - Depois de filtrar o ruído, é hora de cavar nossos alvos — filtrar coisas como `ftp-data`encontrar quaisquer arquivos transferidos e reconstruí-los. Para HTTP, podemos filtrar `http.request.method == "GET"`para ver quaisquer solicitações GET que correspondam aos nomes de arquivo que estamos procurando. Isso pode nos mostrar quem adquiriu os arquivos e outras transferências potenciais internas para a rede nos mesmos protocolos.
7. `Note-taking and mind mapping of the found results.`
    - Anotar tudo o que fazemos, vemos ou encontramos ao longo da investigação é crucial. Garanta que estamos tomando notas amplas, incluindo:
    - Períodos em que capturamos o tráfego.
    - Hosts suspeitos na rede.
    - Conversas contendo os arquivos em questão. (para incluir carimbos de data/hora e números de pacotes)
8. `Summary of the analysis (what did we find?)`
    - Por fim, resuma o que foi encontrado, explicando os detalhes relevantes para que os superiores possam tomar uma decisão informada sobre colocar os hosts afetados em quarentena ou executar uma resposta a incidentes mais significativa.
    - Nossa análise afetará as decisões tomadas, por isso é essencial ser o mais claro e conciso possível.

Esse processo costuma ser cíclico e executado várias vezes com base na análise original da captura para construir uma imagem maior. 

Suponha que uma resposta a incidentes em larga escala seja considerada necessária. Nesse caso, podemos ter que reanalisar o PCAP capturado anteriormente para verificar quaisquer conversas que envolvam os hosts afetados dentro de vários minutos da transferência executável para garantir que ele não se espalhou por outra rota, como um exemplo.


#### Principais componentes de uma análise eficaz

1. **Conheça o seu ambiente**: Manter inventários de ativos e mapas de rede é fundamental para identificar se um host pertence à rede ou é uma ameaça potencial.
   
2. **Posicionamento adequado**: Colocar as ferramentas de captura de tráfego perto da fonte do problema, seja na entrada da rede ou no mesmo segmento que o host suspeito, é crucial para uma análise precisa.

3. **Persistência**: Problemas podem ser intermitentes e difíceis de detectar. A persistência é necessária para capturar atividades maliciosas que ocorrem esporadicamente, como conexões de Comando e Controle.

## Abordagem de análise

Na abordagem de análise, comece verificando protocolos padrão como HTTP/S, FTP e tráfego TCP/UDP, eliminando o que não for relevante para a investigação. Em seguida, analise protocolos de comunicação entre redes, como SSH, RDP ou Telnet, e compare com as políticas de segurança da organização.

Procure por **padrões** de comportamento, como hosts realizando check-ins diários em horários específicos, e fique atento a comunicações entre hosts na rede interna, que são incomuns em uma configuração padrão.

Identifique eventos **únicos** ou anômalos, como mudanças nos padrões de acesso ou tráfego incomum, que podem indicar comportamento malicioso.

Por fim, **não hesite em pedir ajuda** e utilizar ferramentas adicionais como Snort, Security Onion, Firewalls e SIEMs para enriquecer sua análise e melhorar a detecção de ameaças.

Esse processo requer aprendizado contínuo e o uso de diversas ferramentas para melhor proteção do ambiente.


























































































































































