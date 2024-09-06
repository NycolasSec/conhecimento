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

## A estrutura do Volatility

A ferramenta preferida para conduzir análises forenses de memória é [o Volatility](https://www.volatilityfoundation.org/releases) . O Volatility é uma estrutura forense de memória de código aberto líder. No centro dessa estrutura está o script Python Volatility. Esse script aproveita uma infinidade de plugins, permitindo que ele disseque imagens de memória com precisão. Dada sua base Python, podemos executar o Volatility em qualquer plataforma compatível com Python.

Módulos ou plugins do Volatility são extensões ou complementos que aprimoram a funcionalidade do Volatility Framework extraindo informações específicas ou executando tarefas de análise específicas em imagens de memória.

**Alguns módulos comumente usados ​​incluem:**
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

#### Fundamentos do Volatility v2

Vamos agora ver uma demonstração de utilização `Volatility v2` para analisar um despejo de memória `Win7-2515534d.vmem` salvo dentro do diretório `/home/htb-student/MemoryDumps` .

A sessão `help` e `available plugins pode ser vista a seguir`.

```shell-session
NycolasRamos@htb[/htb]$ vol.py --help
```
![[Pasted image 20240906102035.png]]
\<SNIP>\
![[Pasted image 20240906102056.png]]

#### Identificando o Perfil
Os perfis são essenciais para que o Volatility v2 interprete os dados de memória corretamente (a identificação do perfil foi aprimorada na v3). Para determinar o perfil que corresponde ao sistema operacional do dump de memória, podemos usar o `imageinfo`plugin da seguinte forma.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem imageinfo
```
![[Pasted image 20240906102328.png]]

Vamos ver se o `Win7SP1x64`perfil sugerido está correto tentando listar os processos em execução por meio do `pslist`plugin.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 pslist
```
![[Pasted image 20240906102411.png]]

>[!NOTE] Vale ressaltar que, mesmo se especificarmos outro perfil da lista sugerida, o Volatility ainda poderá nos fornecer a saída correta.

#### Identificando artefatos de rede
O plugin `netscan` pode ser usado para escanear artefatos de rede da seguinte maneira.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 netscan
```
![[Pasted image 20240906102634.png]]

Para encontrar estruturas `_TCPT_OBJECT` usando a varredura de tag de pool, use o comando `connscan` . Isso pode encontrar artefatos de conexões anteriores que foram encerradas desde então, além das ativas.

#### Identificando código injetado
O plugin `malfind` pode ser usado para identificar e extrair código injetado e cargas maliciosas da memória de um processo em execução da seguinte maneira.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 malfind --pid=608
```
![[Pasted image 20240906102752.png]]

#### Identificando Handles
O `handles`plugin no Volatility é usado para analisar os handles (referências de arquivo e objeto) mantidos por um processo específico dentro de um dump de memória.

Entender os handles associados a um processo pode fornecer insights valiosos durante a resposta a incidentes e investigações forenses digitais, pois revela os recursos e objetos com os quais um processo está interagindo. Veja como usar o plugin handles.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 handles -p 1512 --object-type=Key
```
![[Pasted image 20240906103026.png]]

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 handles -p 1512 --object-type=File
```
![[Pasted image 20240906103049.png]]

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 handles -p 1512 --object-type=Process
```
![[Pasted image 20240906103124.png]]

#### Identificando serviços do Windows
O `svcscan`plugin no Volatility é usado para listar e analisar serviços do Windows em execução em um sistema dentro de um dump de memória. Veja como usar o `svcscan`plugin.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 svcscan | more
```
![[Pasted image 20240906103357.png]]

#### Identificando DLLs carregadas
O plugin `dlllist` no Volatility é usado para listar as bibliotecas de vínculo dinâmico (DLLs) carregadas no espaço de endereço de um processo específico dentro de um dump de memória. Veja como usar o plugin  `dlllist`.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 dlllist -p 1512
```
![[Pasted image 20240906103532.png]]

#### Identificando Hives
O plugin `hivelist` no Volatility é usado para listar os hives (arquivos de registro) presentes no dump de memória de um sistema Windows. Veja como usar o plugin hivelist.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/Win7-2515534d.vmem --profile=Win7SP1x64 hivelist
```
![[Pasted image 20240906103736.png]]

## Análise de Rootkit com Volatility v2
Vamos agora ver uma demonstração de utilização `Volatility v2`para analisar um despejo de memória salvo `rootkit.vmem` dentro do `/home/htb-student/MemoryDumps` diretório de destino desta seção.

#### Compreendendo a estrutura do EPROCESS
[EPROCESS](https://www.nirsoft.net/kernel_struct/vista/EPROCESS.html) é uma estrutura de dados no kernel do Windows que representa um processo. Cada processo em execução no sistema operacional Windows tem um bloco `EPROCESS` correspondente na memória do kernel.

 Durante a análise de memória, o exame de estruturas `EPROCESS` é crucial para entender os processos em execução em um sistema, identificar relacionamentos ``pai-filho`` e determinar quais processos estavam ativos no momento da captura de memória.
![[Pasted image 20240906104736.png]]

#### FLINK e BLINK
Uma lista duplamente encadeada é uma estrutura de dados fundamental em ciência da computação e programação. É um tipo de lista encadeada onde cada nó (registro) contém duas referências ou ponteiros:

- **Próximo Ponteiro** : Aponta para o próximo nó na lista, permitindo-nos percorrer a lista em uma direção para frente.
- **Ponteiro Anterior** : Aponta para o nó anterior na lista, permitindo-nos percorrer a lista em uma direção para trás.

Dentro da estrutura `EPROCESS`, temos a lista `ActiveProcessLinks` duplamente encadeada que tem o campo `flink` e o campo `blink`.

- **flink** : É um ponteiro de avanço que aponta para a entrada `ActiveProcessLinks` da estrutura `_next_ EPROCESS` na lista de processos ativos.
- **blink** : É um ponteiro para trás dentro da estrutura `EPROCESS` que aponta para a entrada `ActiveProcessLinks` da estrutura `_previous_ EPROCESS` na lista de processos ativos.

Essas listas vinculadas de estruturas EPROCESS são usadas pelo kernel do Windows para iterar rapidamente por todos os processos em execução no sistema. O diagrama abaixo mostra como essa lista vinculada se parece.

![[Pasted image 20240906105236.png]]

#### Identificando sinais de rootkit
**Direct Kernel Object Manipulation (DKOM)** é uma técnica avançada usada por rootkits e malware para manipular diretamente as estruturas de dados do kernel do Windows. Isso permite que processos maliciosos, drivers e arquivos ocultem sua presença da detecção por ferramentas de segurança que operam no modo de usuário.

Por exemplo, se uma ferramenta de monitoramento usa a estrutura ``EPROCESS`` para listar processos, um rootkit que modifica diretamente essa estrutura pode ocultar processos maliciosos, tornando-os invisíveis para a ferramenta.

A captura de tela abaixo mostra uma representação gráfica de como essa desvinculação realmente funciona.
![[Pasted image 20240906105926.png]]


O plugin `psscan` é usado para enumerar processos em execução. Ele verifica as tags do pool de memória associadas à estrutura `EPROCESS` de cada processo.

Essa técnica pode ajudar a identificar processos que podem ter sido ocultados ou desvinculados por rootkits, bem como processos que foram encerrados, mas ainda não foram removidos da memória. Este plugin pode ser usado da seguinte forma.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/rootkit.vmem psscan
```
![[Pasted image 20240906110138.png]]
Na saída abaixo, podemos ver que o plugin `pslist` não conseguiu encontrar o `test.exe` que estava oculto por um rootkit, mas o `psscan`conseguiu.

```shell-session
NycolasRamos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/rootkit.vmem pslist
```
![[Pasted image 20240906110303.png]]

## Análise de memória usando strings
Analisar strings em dumps de memória é uma técnica valiosa em perícia de memória e resposta a incidentes. Strings geralmente contêm informações legíveis por humanos, como mensagens de texto, caminhos de arquivo, endereços IP e até mesmo senhas.

Podemos usar a ferramenta [Strings](https://learn.microsoft.com/en-us/sysinternals/downloads/strings) do pacote ``Sysinternals`` se nosso sistema for baseado em Windows, ou o comando `strings` de `Binutils`, se nosso sistema for baseado em Linux.

Vamos ver alguns exemplos de um despejo de memória chamado `Win7-2515534d.vmem`, que reside no diretório de destino `/home/htb-student/MemoryDumps` desta seção.

#### Identificando endereços IPv4
```shell-session
NycolasRamos@htb[/htb]$ strings /home/htb-student/MemoryDumps/Win7-2515534d.vmem | grep -E "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b"
```
![[Pasted image 20240906110558.png]]

#### Identificando endereços de e-mail
```shell-session
NycolasRamos@htb[/htb]$ strings /home/htb-student/MemoryDumps/Win7-2515534d.vmem | grep -oE "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}\b"
```

#### Identificando artefatos de prompt de comando ou PowerShell
```shell-session
NycolasRamos@htb[/htb]$ strings /home/htb-student/MemoryDumps/Win7-2515534d.vmem | grep -E "(cmd|powershell|bash)[^\s]+"
```
![[Pasted image 20240906110647.png]]


---

```sh
sudo vol.py -f ./Win7-2515534d.vmem  --profile=Win2008R2SP1x64_24000 psscan | grep "1060"
```
![[Pasted image 20240906122828.png]]
A coluna **``PPID``** indica qual o processo pai.

```sh
sudo vol.py -f ./Win7-2515534d.vmem  --profile=Win2008R2SP1x64_24000 psscan | grep "1792"
```
![[Pasted image 20240906122917.png]]




















































































































