`Memory forensics`, também conhecida como análise de memória volátil, é um ramo especializado da perícia digital que se concentra no exame e análise da memória volátil (RAM) de um computador ou dispositivo digital.

Aqui estão alguns tipos de dados encontrados na RAM que são valiosos para investigação de incidentes:
- `Network connections`
- `File handles and open Files`
- `Open registry keys`
- `Running processes on the system`
- `Loaded modules`
- `Loaded device drivers`
- `Command history and console sessions`
- `Kernel data structures`
- `User and credential information`
- `Malware artifacts`
- `System configuration`
- `Process memory regions`

Quando o malware opera, ele frequentemente deixa rastros ou pegadas na memória ativa de um sistema. Ao analisar essa memória, os investigadores podem descobrir processos maliciosos, identificar indicadores de comprometimento e reconstruir as ações do malware.

Deve-se notar que, em alguns casos, dados importantes ou chaves de criptografia podem residir na memória. A perícia de memória pode ajudar a recuperar esses dados, o que pode ser crucial para uma investigação.

A seguir, descrevemos uma abordagem sistemática para análise forense de memória, formulada para auxiliar em investigações na memória e inspirada na metodologia de análise forense de memória de seis etapas do [SANS .](https://www.sans.org/)

1. `Process Identification and Verification`: Vamos começar identificando todos os processos ativos. Softwares maliciosos frequentemente se disfarçam como processos legítimos, às vezes com variações sutis de nome para evitar a detecção. Precisamos:
    
    - Enumere todos os processos em execução.
    - Determine sua origem dentro do sistema operacional.
    - Referência cruzada com processos legítimos conhecidos.
    - Destaque quaisquer discrepâncias ou convenções de nomenclatura suspeitas.
2. `Deep Dive into Process Components`: Depois de sinalizarmos processos potencialmente desonestos, nossa próxima etapa é examinar as Dynamic Link Libraries (DLLs) e os handles associados. O malware frequentemente explora DLLs para ocultar suas atividades. Devemos:
    
    - Examine as DLLs vinculadas ao processo suspeito.
    - Verifique se há DLLs não autorizadas ou maliciosas.
    - Investigue quaisquer sinais de injeção ou sequestro de DLL.
3. `Network Activity Analysis`: Muitas cepas de malware, especialmente aquelas que operam em estágios, necessitam de conectividade com a internet. Elas podem fazer beacons para servidores de Comando e Controle (C2) ou exfiltrar dados. Para descobri-las:
    
    - Revise as conexões de rede ativas e passivas na memória do sistema.
    - Identifique e documente endereços IP externos e domínios associados.
    - Determine a natureza e o propósito da comunicação.
        - Validar a legitimidade do processo.
        - Avalie se o processo normalmente requer comunicação de rede.
        - Rastreie de volta ao processo pai.
        - Avalie seu comportamento e necessidade.
4. `Code Injection Detection`: Adversários avançados frequentemente empregam técnicas como process hollowing ou utilizam seções de memória não mapeadas. Para combater isso, devemos:
    
    - Use ferramentas de análise de memória para detectar anomalias ou sinais dessas técnicas.
    - Identifique quaisquer processos que pareçam ocupar espaços de memória incomuns ou exibir comportamentos inesperados.
5. `Rootkit Discovery`: Alcançar a furtividade e a persistência é um objetivo comum para os adversários. Os rootkits, que se incorporam profundamente no SO, concedem aos agentes de ameaças acesso contínuo, muitas vezes elevado, ao sistema, evitando a detecção. Para lidar com isso:
    
    - Verifique se há sinais de atividade de rootkit ou alterações profundas no sistema operacional.
    - Identifique quaisquer processos ou drivers operando com privilégios anormalmente altos ou exibindo comportamentos furtivos.
6. `Extraction of Suspicious Elements`: Após identificar processos, drivers ou executáveis ​​suspeitos, precisamos isolá-los para uma análise aprofundada. Isso envolve:
    
    - Despejando os componentes suspeitos da memória.
    - Armazená-los com segurança para exames posteriores usando ferramentas forenses especializadas.

## A estrutura de volatilidade

A ferramenta preferida para conduzir análises forenses de memória é [o Volatility](https://www.volatilityfoundation.org/releases) . O Volatility é uma estrutura forense de memória de código aberto líder. No centro dessa estrutura está o script Python Volatility. Esse script aproveita uma infinidade de plugins, permitindo que ele disseque imagens de memória com precisão. Dada sua base Python, podemos executar o Volatility em qualquer plataforma compatível com Python.

Módulos ou plugins de volatilidade são extensões ou complementos que aprimoram a funcionalidade do Volatility Framework extraindo informações específicas ou executando tarefas de análise específicas em imagens de memória.

Alguns módulos comumente usados ​​incluem:

- **`pslist`**: Lista os processos em execução.
- **`cmdline`**: Exibe argumentos de linha de comando do processo
- **`netscan`**: Verifica conexões de rede e portas abertas.
- **`malfind`**: Verifica se há código potencialmente malicioso injetado em processos.
- **`handles`**: Verifica se há alças abertas
- **`svcscan`**: Lista os serviços do Windows.
- **`dlllist`**: Lista DLLs (Bibliotecas de vínculo dinâmico) carregadas em um processo.
- **`hivelist`**: Lista os hives do registro na memória.

A Volatility oferece uma documentação extensa. Você pode encontrar módulos e suas documentações associadas usando os seguintes links:

- **Volatilidade v2** : [https://github.com/volatilityfoundation/volatility/wiki/Command-Reference](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference)
- **Volatilidade v3** : [https://volatility3.readthedocs.io/en/latest/index.html](https://volatility3.readthedocs.io/en/latest/index.html)

Um útil cheatsheet sobre Volatilidade (v2 e v3) pode ser encontrado aqui: [https://blog.onfvp.com/post/volatility-cheatsheet/](https://blog.onfvp.com/post/volatility-cheatsheet/)

Vamos agora ver uma demonstração de utilização `Volatility v2` para analisar um despejo de memória `Win7-2515534d.vmem` salvo dentro do diretório `/home/htb-student/MemoryDumps` .

A sessão `help`





































































































































































































