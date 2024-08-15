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

















































