Ferramentas externas são essenciais para uma análise de triagem rápida e completa. Eric Zimmerman desenvolveu um conjunto de ferramentas forenses voltadas para ajudar analistas a extrair informações cruciais de dispositivos e artefatos digitais. Para acessar a lista completa dessas ferramentas, é possível visitar o site [Eric Zimmerman's Toolset](https://ericzimmerman.github.io/#!index.md).

Para agilizar o processo de download, podemos visitar o site oficial e selecionar o link .net 4 ou .net 6. Esta ação iniciará o download de todas as ferramentas em um formato compactado.
![[Pasted image 20240909110418.png]]

Podemos aproveitar o script do PowerShell fornecido, conforme descrito na etapa 2 da captura de tela acima, para baixar todas as ferramentas.
```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools> .\Get-ZimmermanTools.ps1
```
![[Pasted image 20240909110601.png]]

Embora estejamos utilizando um subconjunto dessas ferramentas para analisar os dados de saída do KAPE, é prudente que nos familiarizemos com todo o kit de ferramentas. Entender todos os recursos de cada ferramenta pode melhorar significativamente nossa capacidade investigativa.

Nesta seção, trabalharemos com certas ferramentas e evidências que residem nos seguintes diretórios do alvo desta seção.
- **Localização da evidência** :`C:\Users\johndoe\Desktop\forensic_data`
    - **Local de saída do KAPE** :`C:\Users\johndoe\Desktop\forensic_data\kape_output`
- **Localização das ferramentas de Eric Zimmerman** :`C:\Users\johndoe\Desktop\Get-ZimmermanTools`
- **Localização do Active@ Disk Editor** :`C:\Program Files\LSoft Technologies\Active@ Disk Editor`
- **Localização da EQL** :`C:\Users\johndoe\Desktop\eqllib-master`
- **Localização do RegRipper** :`C:\Users\johndoe\Desktop\RegRipper3.0-master`

#### Tempos MAC(b) em NTFS

O termo `MAC(b) times` refere-se a carimbos de data e hora associados a arquivos ou objetos, que são cruciais para entender a cronologia de eventos em um sistema de arquivos. A sigla `MAC(b)` significa `Modified, Accessed, Changed, and (b) Birth` times. O carimbo de nascimento (`Birth timestamp`) nem sempre está presente em todos os sistemas de arquivos ou acessível através de funções padrão da API do Windows.

Esses carimbos de data/hora ajudam a detalhar as mudanças e o histórico de acessos de arquivos, sendo fundamentais em investigações forenses.

Os carimbos de data/hora `MACB` são essenciais para rastrear a cronologia de eventos em arquivos. Eles incluem:

- **Modified Time (M)**: Última modificação do conteúdo do arquivo.
- **Accessed Time (A)**: Último acesso ou leitura do arquivo.
- **Changed Time (C)**: Reflete mudanças no registro MFT, como criação, movimentação ou cópia do arquivo.
- **Birth Time (b)**: Marca o momento da criação original do arquivo.

Esses timestamps são críticos em investigações forenses para entender a sequência de eventos relacionados aos arquivos.

#### Regras gerais para carimbos de data/hora no sistema de arquivos NTFS do Windows
A tabela abaixo descreve as regras gerais que regem como várias operações de arquivo influenciam os registros de data e hora no Windows NTFS (New Technology File System).

|Operação|Modificado|Acessado|Nascimento (Criado)|
|---|---|---|---|
|Criar arquivo|Sim|Sim|Sim|
|Modificar arquivo|Sim|Não|Não|
|Cópia de arquivo|Não (herdado)|Sim|Sim|
|Acesso a arquivos|Não|Não*|Não|

1. **Criação de arquivo** :
    - `Modified Timestamp (M)`: O registro de data e hora modificado é atualizado para refletir a hora da criação do arquivo.
    - `Accessed Timestamp (A)`: O registro de data e hora acessado é atualizado para refletir que o arquivo foi acessado no momento da criação.
    - `Birth (Created) Timestamp (b)`: O registro de data e hora de nascimento é definido como o horário de criação do arquivo.
2. **Modificar arquivo** :
    - `Modified Timestamp (M)`: O registro de data e hora Modificado é atualizado para refletir a hora em que o conteúdo ou os atributos do arquivo foram modificados pela última vez.
    - `Accessed Timestamp (A)`: O registro de data e hora acessado não é atualizado quando o arquivo é modificado.
    - `Birth (Created) Timestamp (b)`: O registro de data e hora de nascimento não é atualizado quando o arquivo é modificado.
3. **Cópia do arquivo** :
    - `Modified Timestamp (M)`: O timestamp Modificado normalmente não é atualizado quando um arquivo é copiado. Ele geralmente herda o timestamp do arquivo de origem.
    - `Accessed Timestamp (A)`: O registro de data e hora acessado é atualizado para refletir que o arquivo foi acessado no momento da cópia.
    - `Birth (Created) Timestamp (b)`: O registro de data e hora de nascimento é atualizado para o horário da cópia, indicando quando a cópia foi criada.
4. **Acesso ao arquivo** :
    - `Modified Timestamp (M)`: O registro de data e hora modificado não é atualizado quando o arquivo é acessado.
    - `Accessed Timestamp (A)`: O registro de data e hora acessado é atualizado para refletir o horário do acesso.
    - `Birth (Created) Timestamp (b)`: O registro de data e hora de nascimento não é atualizado quando o arquivo é acessado.

Todos esses timestamps residem no `$MFT`arquivo, localizado na raiz da unidade do sistema.

Esses registros de data e hora são armazenados em `$MFT`dois atributos distintos:
- `$STANDARD_INFORMATION`
- `$FILE_NAME`

Os registros de data e hora visíveis no explorador de arquivos do Windows são derivados do `$STANDARD_INFORMATION`atributo.

#### Investigação de Timestomping

**Timestomping** ([T1070.006](https://attack.mitre.org/techniques/T1070/006/])) refere-se à manipulação de timestamps de arquivos, uma técnica usada para esconder a verdadeira sequência de atividades em uma máquina. Isso representa um grande desafio na perícia digital, pois é comumente usado por ferramentas para evitar a detecção. O MITRE ATT&CK destaca o uso dessa técnica em suas descrições de ataques, demonstrando sua prevalência em ofuscação de dados.

![[Pasted image 20240909112846.png]]

Quando adversários manipulam horários de criação de arquivos ou implantam ferramentas para tais propósitos, o registro de data e hora exibido no explorador de arquivos sofre modificações.

Por exemplo, se carregarmos `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`em `MFT Explorer`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\MFTExplorer`), notaremos que o horário de criação do arquivo `ChangedFileTime.txt`foi adulterado, sendo exibido `03-01-2022`no explorador de arquivos, o que diverge do horário real de criação.

**Observação** : `MFT Explorer` levará de 15 a 25 minutos para carregar o arquivo.

![[Pasted image 20240909113028.png]]

Entretanto, dado nosso conhecimento de que os carimbos de data/hora no explorador de arquivos se originam do `$STANDARD_INFORMATION`atributo, podemos verificar esses dados com os carimbos de data/hora do `$FILE_NAME`atributo por meio de `MFTEcmd`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6`) da seguinte forma.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --de 0x16169
```
![[Pasted image 20240909113310.png]]

![[Pasted image 20240909113317.png]]

![[Pasted image 20240909113319.png]]

Em sistemas de arquivos padrão do Windows, como NTFS, os usuários comuns geralmente não têm permissão para alterar diretamente os timestamps dos nomes de arquivos em `$FILE_NAME`, pois tais alterações são restritas ao kernel do sistema.

Para iniciar a exploração, começaremos com a análise de artefatos baseados em sistema de arquivos, começando pelo arquivo `$MFT`, que está localizado no diretório raiz da saída do KAPE.

#### Arquivo MFT

O `$MFT` (Master File Table) é crucial no NTFS, o sistema de arquivos usado pelos Windows, atuando como um banco de dados que organiza e catalogam arquivos e diretórios. Cada item em um volume NTFS tem uma entrada correspondente no `$MFT`, que registra metadados detalhados sobre criação, modificação, exclusão e acesso a arquivos e diretórios. 

Para perícia digital, o `$MFT` é valioso, pois permite a reconstrução de uma linha do tempo detalhada de eventos e ações no sistema. Um recurso importante é que o `$MFT` pode manter metadados de arquivos mesmo após sua exclusão, o que é essencial para análises forenses e recuperação de dados.

O arquivo `$MFT` foi extraído e salvo em `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`. Utilizaremos o MFT Explorer, uma ferramenta desenvolvida por Eric Zimmerman, para inspecionar e analisar os metadados do `$MFT`, proporcionando uma visão detalhada dos arquivos e diretórios, com uma interface gráfica semelhante ao Windows Explorer.

![[Pasted image 20240909114354.png]]

>[!NOTE] Os registros na Master File Table (MFT) não são removidos após a criação. Em vez disso, novos registros são adicionados conforme novos arquivos e diretórios são criados. Registros de arquivos excluídos são marcados como "livres" e ficam disponíveis para reutilização.

#### Estrutura do registro de arquivo MFT

Cada arquivo ou diretório em um volume **NTFS** é representado por um registro na ``Master File Table`` (``MFT``), que contém atributos e detalhes estruturados sobre o item. Compreender a estrutura do MFT é crucial para análise forense, gerenciamento de sistema e recuperação de dados em Windows, ajudando especialistas a identificar atributos importantes.

![[Pasted image 20240909114547.png]]

Aqui está um instantâneo dos componentes:

- **`File Record Header`**: Contém metadados administrativos sobre o registro do arquivo, como assinatura e número de sequência.
- **`Standard Information Attribute Header`**: Armazena dados padrão do arquivo, como carimbos de data/hora e identificadores de segurança.
- **`File Name Attribute Header`**: Inclui informações sobre o nome do arquivo, como comprimento e caracteres Unicode.
- **`Data Attribute Header`**: Descreve se os dados do arquivo são armazenados no registro MFT (`resident`) ou em clusters externos (`non-resident`).
	- **`File Data`** (``File content``): Dados reais podem estar no registro MFT para arquivos pequenos ou referenciar clusters no disco para arquivos grandes.
- **`Additional Attributes (optional)`**: Pode incluir atributos adicionais como descritores de segurança, IDs de objeto e informações de índice.

Esses atributos podem variar dependendo das características do arquivo. Podemos ver o tipo comum de informação que é armazenada dentro desses cabeçalhos e atributos na imagem abaixo.
![[Pasted image 20240909115335.png]]

#### Cabeçalho do registro de arquivo
Contém metadados sobre o registro do arquivo em si. Inclui campos como assinatura, número de sequência e outros dados administrativos.

![[Pasted image 20240909115902.png]]

O registro de arquivo começa com um cabeçalho que contém metadados sobre o próprio registro de arquivo. Esse cabeçalho normalmente inclui as seguintes informações:

- `Signature`: Uma assinatura de quatro bytes, geralmente "FILE" ou "BAAD", indicando se o registro está em uso ou foi desalocado.
- `Offset to Update Sequence Array`: Um deslocamento para o Update Sequence Array (USA) que ajuda a manter a integridade do registro durante as atualizações.
- `Size of Update Sequence Array`: O tamanho do Update Sequence Array em palavras.
- `Log File Sequence Number`: Um número que identifica a última atualização do registro do arquivo.
- `Sequence Number`: Um número que identifica o registro do arquivo. Os registros MFT são numerados sequencialmente, começando em 0.
- `Hard Link Count`: O número de hard links para o arquivo. Isso indica quantas entradas de diretório apontam para esse registro de arquivo.
- `Offset to First Attribute`: Um deslocamento para o primeiro atributo no registro do arquivo.

Quando examinamos o arquivo MFT `MFTECmd`e extraímos detalhes sobre um registro, as informações do registro do arquivo são apresentadas conforme mostrado na captura de tela subsequente.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --de 27142
```
![[Pasted image 20240909120050.png]]

Cada atributo significa alguma informação de entrada, identificada pelo tipo.

.

| Type        | Attribute              | Description                                                                                    |
| ----------- | ---------------------- | ---------------------------------------------------------------------------------------------- |
| 0x10 (16)   | $STANDARD_INFORMATION  | Informações gerais - sinalizadores, horários MAC, proprietário e ID de segurança.              |
| 0x20 (32)   | $ATTRIBUTE_LIST        | Ponteiros para outros atributos e uma lista de atributos não residentes.                       |
| 0x30 (48)   | $FILE_NAME             | Nome do arquivo - (Unicode) e horários MAC desatualizados                                      |
| 0x40 (64)   | $VOLUME_VERSION        | Informações de volume - somente NTFS v1.2 e Windows NT, não mais usado                         |
| 0x40 (64)   | $OBJECT_ID             | Identificador exclusivo 16B - para arquivo ou diretório (NTFS 3.0+; Windows 2000+)             |
| 0x50 (80)   | $SECURITY_DESCRIPTOR   | Lista de controle de acesso e propriedades de segurança do arquivo                             |
| 0x60 (96)   | $VOLUME_NAME           | Nome do volume                                                                                 |
| 0x70 (112)  | $VOLUME_INFORMATION    | Versão do sistema de arquivos e outras informações                                             |
| 0x80 (128)  | $DATA                  | Conteúdo do arquivo                                                                            |
| 0x90 (144)  | $INDEX_ROOT            | Nó raiz de uma árvore de índice                                                                |
| 0xA0 (160)  | $INDEX_ALLOCATION      | Nós de uma árvore de índice - com uma raiz em $INDEX_ROOT                                      |
| 0xB0 (176)  | $BITMAP                | Bitmap - para o arquivo $MFT e para índices (diretórios)                                       |
| 0xC0 (192)  | $SYMBOLIC_LINK         | Informações de soft link - (somente NTFS v1.2 e Windows NT)                                    |
| 0xC0 (192)  | $REPARSE_POINT         | Dados sobre um ponto de nova análise - usado para um link simbólico (NTFS 3.0+; Windows 2000+) |
| 0xD0 (208)  | $EA_INFORMATION        | Usado para compatibilidade com versões anteriores de aplicativos OS/2 (HPFS)                   |
| 0xE0 (224)  | $EA                    | Usado para compatibilidade com versões anteriores de aplicativos OS/2 (HPFS)                   |
| 0x100 (256) | $LOGGED_UTILITY_STREAM | Chaves e outras informações sobre atributos criptografados (NTFS 3.0+; Windows 2000+)          |

Para desmistificar a estrutura de um registro de arquivo NTFS MFT, estamos aproveitando os recursos do [Active@ Disk Editor](https://www.disk-editor.org/index.html) . Esta potente ferramenta de edição de disco freeware está disponível em `C:\Program Files\LSoft Technologies\Active@ Disk Editor`e facilita a visualização e modificação de dados brutos de disco, incluindo a Master File Table de um sistema NTFS. Os mesmos insights podem ser obtidos de outras ferramentas de análise MFT, como `MFT Explorer`.

Podemos dar uma olhada mais de perto abrindo `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`e `Active@ Disk Editor`pressionando `Inspect File Record`.

![[Pasted image 20240909190553.png]]

![[Pasted image 20240909190559.png]]

No Disk Editor, temos acesso aos dados brutos das entradas MFT. Isso inclui uma representação hexadecimal do registro MFT, completo com seu cabeçalho e atributos.

**Bandeira de não residente**
![[Pasted image 20240909190614.png]]

Ao analisar a entrada em `MFTECmd`, é assim que o cabeçalho de dados não residentes aparece.
![[Pasted image 20240909190620.png]]

**Bandeira Residente**
![[Pasted image 20240909190637.png]]

Ao analisar a entrada em `MFTECmd`, é assim que o cabeçalho de dados residente aparece.
![[Pasted image 20240909190736.png]]

#### Dados do Zone.Identifier no registro de arquivo MFT
O `Zone.Identifier` é um atributo de metadados no Windows que indica a zona de segurança de origem de um arquivo. Faz parte do Windows Attachment Execution Service (AES) e ajuda o sistema a determinar como deve processar arquivos provenientes da internet ou de outras fontes potencialmente não confiáveis.

Quando um arquivo é obtido da internet, o Windows atribui a ele um Zone Identifier ( `ZoneId`). Este ZoneId, incorporado nos metadados do arquivo, significa a fonte ou zona de segurança da origem do arquivo. Por exemplo, arquivos de origem da internet normalmente têm um `ZoneId`de `3`, denotando a Internet Zone.

Por exemplo, baixamos várias ferramentas dentro do `C:\Users\johndoe\Downloads`diretório do alvo desta seção. Após o download, um `ZoneID`repleto com o Zone.Identifier (ou seja, a URL de origem) foi atribuído a elas.
```powershell
PS C:\Users\johndoe\Downloads> Get-Item * -Stream Zone.Identifier -ErrorAction SilentlyContinue
```
![[Pasted image 20240909190920.png]]

Para revelar o conteúdo de um arquivo `Zone.Identifier`, o seguinte comando pode ser executado no PowerShell.

```powershell
PS C:\Users\johndoe\Downloads> Get-Content * -Stream Zone.Identifier -ErrorAction SilentlyContinue
```
![[Pasted image 20240909191046.png]]

O **Mark of the Web (MotW)** utiliza o `Zone.Identifier` para identificar arquivos provenientes da internet ou de fontes potencialmente não confiáveis. O MotW ajuda a reforçar a segurança em aplicativos, como o Microsoft Word, aplicando medidas específicas ao abrir esses arquivos. Por exemplo, um documento do Word com um MotW pode ser aberto em **Protected View**, um modo restrito que protege o sistema contra possíveis ameaças de segurança, isolando o documento do resto do sistema.

O `Zone.Identifier` é um atributo de metadados no Windows que ajuda a reforçar a segurança de arquivos baixados da web, indicando sua origem. Para analistas forenses, esse atributo é útil para investigar a forma como um arquivo foi baixado, revelando se ele veio de uma fonte potencialmente não confiável, como a internet.

Embora sua função principal seja reforçar a segurança de arquivos baixados da web, analistas forenses podem utilizá-lo para fins investigativos. Ao examinar esse atributo, eles podem determinar o método de download do arquivo. Veja um exemplo abaixo.

![[Pasted image 20240909191506.png]]

#### Analisando com o Timeline Explorer
O **Timeline Explorer**, desenvolvido por Eric Zimmerman, é uma ferramenta forense digital que auxilia na criação e análise de linhas do tempo a partir de vários artefatos. Ele fornece uma visão cronológica de eventos e atividades do sistema, facilitando a reconstrução de sequências de eventos durante investigações. A ferramenta permite filtrar dados com base em critérios específicos, como intervalos de tempo e palavras-chave, o que ajuda a focar em informações relevantes.

Para carregar arquivos CSV com dados de eventos no Timeline Explorer, basta arrastar e soltar o arquivo na janela do aplicativo, facilitando a análise de registros de data e hora.

Uma vez ingerido, o Timeline Explorer processará e exibirá os dados. A duração desse processo depende do tamanho do arquivo.
![[Pasted image 20240909192013.png]]

Após carregar os dados no Timeline Explorer, a linha do tempo será exibida com os eventos do arquivo CSV em ordem cronológica. Com os dados carregados, você pode explorar e analisar os eventos usando recursos como:

- **Ampliação de Intervalos de Tempo:** Focar em períodos específicos.
- **Filtragem de Eventos:** Refinar a visualização com base em critérios específicos.
- **Pesquisa de Palavras-chave:** Localizar eventos relacionados a termos específicos.
- **Correlação de Atividades:** Relacionar eventos e identificar padrões ou conexões.

![[Pasted image 20240909193521.png]]

Forneceremos vários exemplos de uso do Timeline Explorer nesta seção.

#### Jornal da USN

`USN`, ou `Update Sequence Number`, é um componente vital do sistema de arquivos NTFS no Windows. O USN Journal é essencialmente um recurso de diário de alterações que registra meticulosamente alterações em arquivos e diretórios em um volume NTFS.

Para aqueles em forense digital, o USN Journal é uma mina de ouro. Ele nos permite monitorar operações como Criação de Arquivos, Renomeação, Exclusão e Sobreposição de Dados.

No ambiente Windows, o arquivo USN Journal é designado como `$J`. O diretório KAPE Output abriga o USN Journal coletado no seguinte diretório:`<KAPE_output_folder>\<Drive>\$Extend`

Aqui está como fica na saída do nosso KAPE ( `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend`)

![[Pasted image 20240909193633.png]]

#### Analisando o USN Journal usando MFTECmd

O **MFTECmd** é uma ferramenta desenvolvida por Eric Zimmerman que, além de analisar o Master File Table (MFT), também é útil na análise do USN Journal. O USN Journal documenta alterações em arquivos e diretórios, e suas entradas frequentemente referenciam modificações que também estão registradas no MFT.

Portanto, o MFTECmd pode ser usado para examinar e dissecar o USN Journal, ajudando na análise de alterações e eventos relacionados a arquivos e diretórios.

Para facilitar a análise do USN Journal usando `MFTECmd`, execute um comando semelhante ao abaixo:
```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J' --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT-J.csv
```
![[Pasted image 20240909193952.png]]

O arquivo de saída resultante é salvo como `MFT-J.csv`dentro do `C:\Users\johndoe\Desktop\forensic_data\mft_analysis`diretório. Vamos importá-lo para `Timeline Explorer`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\TimelineExplorer`).

**Observação** : remova o filtro no número da entrada para ver a imagem completa.

![[Pasted image 20240909194020.png]]

Após inspeção, podemos discernir uma linha do tempo de eventos cronologicamente ordenada. Notavelmente, a entrada para `uninstall.exe`é evidente.

Ao aplicar um filtro no Número de Entrada `93866`, que corresponde ao `Entry ID`for `uninstall.exe`, podemos obter a natureza das modificações executadas neste arquivo específico.

![[Pasted image 20240909194130.png]]

Arquivos com a extensão `.crdownload` são arquivos parcialmente baixados por navegadores como Microsoft Edge, Google Chrome ou Chromium. O atributo `Zone.Identifier` pode fornecer informações adicionais sobre a origem do arquivo, como o IP ou domínio de onde ele foi baixado.

Para investigar esta suposição, devemos:
1. Crie um arquivo CSV para `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`usar `MFTECmd`como fizemos para `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J`.
2. Importe o CSV relacionado ao $MFT para `Timeline Explorer`.
3. Aplique um filtro na entrada Número `93866`.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT.csv
```
![[Pasted image 20240909194246.png]]

![[Pasted image 20240909194255.png]]

#### Investigação de logs de eventos do Windows
Investigar os Logs de Eventos do Windows é crucial em perícia digital e resposta a incidentes. Esses logs registram atividades do sistema, comportamentos do usuário e incidentes de segurança. Ao usar o KAPE, os logs de eventos são duplicados para preservar o estado original como evidência.

O diretório KAPE Output abriga esses logs de eventos no seguinte diretório: `<KAPE_output_folder>\Windows\System32\winevt\logs`
![[Pasted image 20240909194412.png]]

Este diretório é preenchido com arquivos `.evtx` que encapsulam uma infinidade de logs de eventos do Windows, incluindo, mas não se limitando a Segurança, Aplicativo, Sistema e Sysmon (se ativado).

Nossa missão é examinar os logs de eventos em busca de anomalias, padrões ou indicadores de comprometimento (IOCs). Devemos prestar atenção a IDs de eventos, carimbos de data/hora, IPs de origem, nomes de usuários e outros detalhes. Ferramentas forenses, como utilitários de análise de logs e sistemas SIEM, são essenciais para essa tarefa. Identificar táticas, técnicas e procedimentos (TTPs) em atividades suspeitas e correlacionar eventos de diferentes fontes ajuda a construir uma linha do tempo abrangente dos eventos.

A análise dos Logs de Eventos do Windows foi abordada nos módulos intitulados `Windows Event Logs & Finding Evil`e `YARA & Sigma for SOC Analysts`.

#### Análise de logs de eventos do Windows usando EvtxECmd (EZ-Tool)

`EvtxECmd`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd`) é outra ideia de Eric Zimmerman, adaptada para arquivos de log de eventos do Windows (arquivos EVTX).

Com essa ferramenta à nossa disposição, podemos extrair logs de eventos específicos ou uma gama de eventos de um arquivo EVTX, convertendo-os em formatos mais digeríveis, como JSON, XML ou CSV.

Vamos iniciar o menu de ajuda do EvtxECmd para nos familiarizarmos com as várias opções.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe -h
```
![[Pasted image 20241018083750.png]]

![[win_dfir_winevt4_.webp]]

#### Mapas em EvtxECmd

Esses mapas metamorfoseiam dados personalizados em campos padronizados nos dados CSV (e JSON). Essa granularidade e precisão são indispensáveis ​​em investigações forenses.

Campos padronizados em mapas:
- `UserName`: Contém informações sobre o usuário e/ou domínio encontradas em vários logs de eventos
- `ExecutableInfo`: Contém informações sobre a linha de comando do processo, tarefas agendadas, etc.
- `PayloadData1,2,3,4,5,6`: Campos adicionais para extrair e colocar dados contextuais de logs de eventos
- `RemoteHost`: Contém informações sobre o endereço IP

`EvtxECmd` desempenha um papel significativo em:
- Converter a parte única de um evento, conhecida como EventData, em um formato mais padronizado e legível.
- Garantir que os arquivos de mapa sejam adaptados a logs de eventos específicos, como Segurança, Aplicativo ou logs personalizados, para lidar com diferenças em estruturas de eventos e dados.
- Usando um identificador exclusivo, o elemento Canal, para especificar para qual log de eventos um arquivo de mapa específico foi criado, evitando confusão quando IDs de eventos são reutilizados em logs diferentes.

Para garantir que os mapas mais recentes estejam disponíveis antes de converter os arquivos EVTX para CSV/JSON, use o comando abaixo.
```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe --sync
```
![[Pasted image 20241018084442.png]]
...
![[Pasted image 20241018084458.png]]

Com os mapas mais recentes integrados, estamos equipados para infundir informações contextuais em campos distintos, simplificando o processo de análise de log. Com ``EvtxECmd`` podemos converter arquivos de log em arquivos CSV e de JSON.

Por exemplo, o comando abaixo facilita a conversão do arquivo `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx` para um arquivo CSV:

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe -f "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx" --csv "C:\Users\johndoe\Desktop\forensic_data\event_logs\csv_timeline" --csvf kape_event_log.csv
```
![[Pasted image 20241018085012.png]]

Depois de importar o CSV resultante para o `Timeline Explorer`, devemos ver o seguinte.

![[win_dfir_winevt6.webp]]

**Informações executáveis** :
![[win_dfir_winevt7.webp]]

#### Investigando logs de eventos do Windows com EQL
[O Event Query Language (EQL) do Endgame](https://github.com/endgameinc/eqllib) oferece uma linguagem estruturada que facilita a consulta e a correlação de eventos em várias fontes de log, incluindo os Logs de Eventos do Windows.

Atualmente, o módulo EQL é compatível com as versões 2.7 e 3.5+ do Python. Se você tiver uma versão suportada do Python instalada, execute o comando a seguir.
```sh
pip install eql
```

Se o Python estiver configurado corretamente e incluído no seu PATH, ``eql`` deve estar acessível. Para verificar isso, execute o comando abaixo.
```sh
eql --version
# eql 0.9.18
```

No repositório do EQL (disponível em `C:\Users\johndoe\Desktop\eqllib-master`), há um módulo PowerShell repleto de funções essenciais adaptadas para analisar eventos Sysmon de Logs de Eventos do Windows. Este módulo reside no diretório `utils` de `eqllib`, e é chamado `scrape-events.ps1`.

No diretório EQL, inicie o módulo scrape-events.ps1 com o seguinte comando:
```powershell
PS C:\Users\johndoe\Desktop\eqllib-master\utils> import-module .\scrape-events.ps1 
```

Para transformar, por exemplo, `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx` em um formato JSON adequado para consultas EQL, execute o comando abaixo.
```powershell
PS C:\Users\johndoe\Desktop\eqllib-master\utils> Get-WinEvent -Path C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx -Oldest | Get-EventProps | ConvertTo-Json | Out-File -Encoding ASCII -FilePath C:\Users\johndoe\Desktop\forensic_data\event_logs\eql_format_json\eql-sysmon-data-kape.json
```
Esta ação produzirá um arquivo JSON, preparado para consultas EQL.

Vamos agora ver como poderíamos ter identificado a enumeração de usuários/grupos por meio de uma consulta EQL no arquivo JSON que criamos.
```powershell
C:\Users\johndoe>eql query -f C:\Users\johndoe\Desktop\forensic_data\event_logs\eql_format_json\eql-sysmon-data-kape.json "EventId=1 and (Image='*net.exe' and (wildcard(CommandLine, '* user*', '*localgroup *', '*group *')))"
```
![[Pasted image 20241018090621.png]]

![[win_dfir_winevt11.webp]]

#### Registro do Windows
Uma análise profunda dos hives do registro pode nos fornecer informações valiosas. Os arquivos relacionados ao registro coletados do KAPE geralmente são armazenados em `<KAPE_output_folder>\Windows\System32\config`

![[img10.webp]]

Além disso, há seções de registro específicas do usuário localizadas em diretórios de usuários individuais, conforme exemplificado na captura de tela a seguir.

![[win_dfir_registry1_.webp]]

Para uma análise abrangente, podemos empregar `Registry Explorer`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\RegistryExplorer`) uma ferramenta baseada em GUI idealizada por Eric Zimmerman.

Simplesmente arrastando e soltando esses arquivos no Registry Explorer, a ferramenta processa os dados, apresentando-os dentro de sua GUI. O painel esquerdo exibe os hives do registro, enquanto o painel direito revela seus valores correspondentes.

Na captura de tela abaixo, carregamos o hive `SYSTEM`, que pode ser encontrado dentro do diretório `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config` de destino desta seção.

![[Pasted image 20241018091541.png]]

O Registry Explorer ostenta um conjunto de recursos, incluindo análise de hive, recursos de pesquisa, opções de filtragem, visualização de timestamp e bookmarking.

Na captura de tela abaixo, carregamos o hive no `SOFTWARE`, que pode ser encontrado dentro do diretório `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config` do alvo desta seção. Observe os favoritos disponíveis dentro do Registry Explorer.

![[Pasted image 20241018092001.png]]

#### RegRipper
Outra ferramenta poderosa em nosso arsenal é `RegRipper`(disponível em `C:\Users\johndoe\Desktop\RegRipper3.0-master`), um utilitário de linha de comando hábil em extrair rapidamente informações do Registro.

Podemos ver a seção de ajuda com o parâmetro `-h`.
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -h
```

Para uma experiência perfeita com o RegRipper, é essencial nos familiarizarmos com seus plugins. Para enumerar todos os plugins disponíveis e catalogá-los em um arquivo CSV , use o comando abaixo.
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -l -c > rip_plugins.csv
```

Esta ação compila uma lista abrangente de plugins, detalhando as hives associadas, e salva como um arquivo CSV.

A captura de tela abaixo esclarece o conteúdo deste arquivo, destacando o nome do plugin, seu hive de registro correspondente e uma breve descrição.

![[win_dfir_regripper_5.webp]]

Para começar, vamos executar o `compname`comando no hive SYSTEM (localizado em `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`), que recupera o nome do computador.

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p compname
```
![[Pasted image 20241018092955.png]]

**Fuso horário**
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p timezone
```

**Informações de rede**
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p nic2
```
As mesmas informações podem ser extraídas usando o plugin `ips`.

**Execução do instalador**
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SOFTWARE" -p installer
```

**Pastas/Documentos Acessados ​​Recentemente**
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Users\John Doe\NTUSER.DAT" -p recentdocs
```

**Início automático - Executar entradas de teclas**
```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Users\John Doe\NTUSER.DAT" -p run
```

#### Artefatos de execução do programa
Quando falamos `execution artifacts` em perícia digital, estamos nos referindo aos rastros e evidências deixados para trás em um sistema de computador ou dispositivo quando um programa é executado. Se quisermos juntar o que aconteceu em um computador, mergulhar nesses artefatos de execução é essencial.

Você pode se deparar com alguns artefatos de execução bem conhecidos nestes componentes do Windows:
- `Prefetch`
- `ShimCache`
- `Amcache`
- `BAM (Background Activity Moderator)`

Vamos nos aprofundar em cada um deles para entender melhor o tipo de detalhes de execução do programa que eles capturam.

#### Investigação de Prefetch
O Prefetch é um recurso do Windows que otimiza o carregamento de aplicativos ao pré-carregar componentes e dados necessários. Cada programa executado gera um arquivo Prefetch, que é nomeado com base no executável original, seguido de um código hexadecimal do caminho do arquivo e com a extensão ``.pf``.

Na forense digital, a pasta Prefetch e seus arquivos oferecem informações valiosas sobre quais aplicativos foram usados em um sistema Windows. Analistas forenses podem investigar esses arquivos para descobrir quais programas foram executados, sua frequência e a última vez que foram acessados.

Em geral, os arquivos de pré-busca são armazenados no `C:\Windows\Prefetch\`diretório.

Arquivos relacionados à pré-busca coletados do KAPE normalmente são armazenados em `<KAPE_output_folder>\Windows\prefetch`.

![[win_dfir_prefetch_.webp]]

Eric Zimmerman fornece uma ferramenta para pré-busca de arquivos: `PECmd`(disponível em `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6`).

Aqui está um exemplo de como iniciar o menu de ajuda do PECmd a partir do diretório de ferramentas do EricZimmerman.

```powershell-session
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -h
```

![[win_dfir_prefetch1_.webp]]

O PECmd analisará o arquivo prefetch ( `.pf`) e exibirá várias informações sobre a execução do aplicativo. Isso geralmente inclui detalhes como:

- Primeiro e último carimbo de data/hora de execução.
- Número de vezes que o aplicativo foi executado.
- Informações de volume e diretório.
- Nome e caminho do aplicativo.
- Informações do arquivo, como tamanho do arquivo e valores de hash.

amos ver fornecendo um caminho para um único arquivo de pré-busca, por exemplo, o arquivo de pré-busca relacionado a `discord.exe`(ou seja, DISCORD.EXE-7191FAD6.pf localizado em `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch`).

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\DISCORD.EXE-7191FAD6.pf
```
![[Pasted image 20241018094922.png]]

Ao rolar a saída para baixo, podemos ver os diretórios referenciados por este executável.
![[win_dfir_prefetch3.webp]]

Rolando mais para baixo na saída, são revelados os arquivos referenciados por este executável.
![[win_dfir_prefetch3 1.webp]]

#### Atividade suspeita em arquivos referenciados
Também devemos considerar o diretório de onde o aplicativo foi executado. Se ele foi executado de um local incomum ou inesperado, pode ser suspeito. Por exemplo, a captura de tela abaixo mostra alguns locais e arquivos suspeitos.

![[win_dfir_prefetch5_.webp]]

#### Converter arquivos de pré-busca para CSV
Para facilitar a análise, podemos converter os dados de pré-busca em CSV da seguinte maneira.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -d C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch --csv C:\Users\johndoe\Desktop\forensic_data\prefetch_analysis
```

O diretório de destino contém a saída analisada no formato CSV.
![[win_dfir_prefetch7_.webp]]

Agora podemos analisar facilmente a saída no Timeline Explorer. Vamos carregar ambos os arquivos.

![[win_dfir_prefetch9.webp]]

O segundo arquivo de saída é o arquivo de linha do tempo, que mostra os detalhes do executável classificados pelo tempo de execução.

![[win_dfir_prefetch8.webp]]

#### Investigação do ShimCache (Cache de compatibilidade de aplicativos)

**ShimCache (AppCompatCache)** é um mecanismo do Windows que ajuda a identificar problemas de compatibilidade de aplicativos. Ele registra informações sobre programas executados no sistema e armazena esses dados no Registro do Windows. Esse banco de dados é útil para os desenvolvedores rastrearem possíveis problemas de compatibilidade de software.

Nas entradas de cache `AppCompatCache`, podemos ver informações como:
- Caminhos de arquivo completos
- Carimbos de tempo
    - Última hora modificada ($Standard_Information)
    - Última hora de atualização (**Shimcache**)
- Sinalizador de execução do processo
- Posição de entrada do cache

Investigadores forenses podem usar essas informações para detectar a execução de arquivos potencialmente maliciosos.

A chave `AppCompatCache` está localizada no local do registro `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\ControlSet001\Control\Session Manager\AppCompatCache`.

Vamos carregar o `SYSTEM`hive do registro (disponível em `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`) `Registry Explorer`e ver que tipo de informação ele contém.

Podemos fazer isso abrindo o Registry Explorer e soltando os arquivos do hive do registro nele. Então, precisaremos ir para favoritos e selecionar `AppCompatCache`. No canto inferior direito, devemos ver a evidência da execução do aplicativo, conforme mostrado na captura de tela.

![[Pasted image 20241018100022.png]]

#### Investigação de Amcache
**AmCache** é um arquivo de registro do Windows que armazena evidências sobre a execução de programas, sendo uma ferramenta valiosa em investigações forenses e de segurança digital. Ele registra dados como o caminho de execução, o primeiro tempo de execução, a exclusão, a instalação e fornece o hash dos executáveis, ajudando a detectar execuções suspeitas.

No sistema operacional Windows, o hive ``AmCache`` está localizado em`C:\Windows\AppCompat\Programs\AmCache.hve`

Os arquivos relacionados ao ``AmCache`` coletados do KAPE geralmente são armazenados em `<KAPE_output_folder>\Windows\AppCompat\Programs`.

Vamos carregar `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve` no Explorador do Registro para ver que tipo de informação ele contém.

![[Pasted image 20241018100329.png]]

Usando [o AmcacheParser](https://github.com/EricZimmerman/AmcacheParser) de Eric Zimmerman , podemos analisar e converter esse arquivo em um CSV e analisá-lo em detalhes dentro do Timeline Explorer.

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\AmcacheParser.exe -f "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve" --csv C:\Users\johndoe\Desktop\forensic_data\amcache-analysis
```

#### Investigação do Windows BAM (Background Activity Moderator)
O `Background Activity Moderator`(BAM) é um componente no sistema operacional Windows que rastreia e registra a execução de certos tipos de tarefas de segundo plano ou agendadas. O BAM é, na verdade, um driver de dispositivo do kernel, conforme mostrado na captura de tela abaixo.

![[win_dfir_bamdrv.webp]]

Ele é o principal responsável por controlar a atividade de aplicativos em segundo plano, mas pode nos ajudar a fornecer a evidência da execução do programa que ele lista sob o hive do registro bam. A chave BAM está localizada no local do registro abaixo. `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\bam\State\UserSettings\{USER-SID}`

Usando o Registry Explorer, podemos navegar por isso dentro do hive SYSTEM para ver os nomes dos executáveis. O Registry Explorer já tem um marcador para `bam`.

![[Pasted image 20241018100609.png]]

Também podemos usar o plugin `RegRipper` para obter informações semelhantes do `bam`.

#### Analisando dados de chamadas de API capturados ( `.apmx64`)

**Arquivos .apmx64** são gerados pelo **API Monitor**, uma ferramenta que registra dados de chamadas de API feitas por aplicativos e serviços. Esses arquivos podem ser abertos e analisados no próprio software, que é usado principalmente para depuração e monitoramento, mas também pode ser valioso em investigações forenses. No exemplo, o arquivo `discord.apmx64` será carregado no API Monitor para análise em busca de informações úteis.

Iniciar o API Monitor iniciará determinados arquivos necessários.
![[Pasted image 20241018100945.png]]

Ao abrir o aplicativo API Monitor, vamos até o menu `File` e escolher `Open`. A partir daí, vamos navegar até o local do arquivo `.apmx64` e selecioná-lo.

![[win_dfir_apimon2.webp]]

Após abrir o arquivo, uma lista de chamadas de API gravadas feitas pelo aplicativo monitorado será exibida. A captura de tela abaixo oferece uma visão abrangente da interface do usuário do API Monitor e suas várias seções.

![[win_dfir_apimon3_.webp]]

Clicar nos processos monitorados à esquerda exibirá os dados de chamada de API gravados para o processo escolhido na visualização de resumo à direita. Para ilustrar, considere selecionar o processo `discord.exe`. Na visualização de resumo, observaremos as chamadas de API iniciadas por `discord.exe`.

![[win_dfir_apimon4.webp]]

Uma observação notável da captura de tela é a chamada para a [função getenv](https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/getenv-wgetenv?view=msvc-170) . Aqui está a sintaxe desta função.

```shell-session
char *getenv(
   const char *varname
);
```

Esta função recupera o valor de uma variável de ambiente especificada. Ela requer um parâmetro `varname`, representando um nome de variável de ambiente válido, e retorna um ponteiro apontando para a entrada da tabela contendo o valor da respectiva variável de ambiente

O API Monitor ostenta uma infinidade de recursos de filtragem e pesquisa. Isso nos permite aprimorar chamadas de API específicas com base em funções ou períodos de tempo.

**Persistência do Registro por meio de Chaves de Execução**
Uma estratégia frequentemente empregada por adversários para manter acesso não autorizado a um sistema comprometido é inserir uma entrada no Registro `run keys` do Windows.

Vamos investigar se há alguma referência à `RegOpenKeyExA`função, que acessa a chave de registro designada. Para executar essa pesquisa, basta digitar `RegOpenKey`na caixa de pesquisa, geralmente situada no topo da janela do Monitor de API, e pressionar `Enter`.

![[win_dfir_apimon6.webp]]

A partir dos resultados exibidos, é evidente que a chave de registro `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` corresponde à chave de registro Run, que aciona o programa designado em cada login de usuário. Entidades maliciosas frequentemente exploram essa chave para incorporar entradas apontando para seu backdoor, uma tarefa que pode ser realizada por meio da função API do registro `RegSetValueExA`.

Para explorar mais, vamos procurar qualquer menção à função `RegSetValueExA`, que define dados e tipo para um valor especificado dentro de uma chave de registro. Abra a caixa de pesquisa, digite `RegSet` e pressione `Enter`.

![[win_dfir_apimon7.webp]]

Uma observação notável é a invocação `RegSetValueExA`. Antes de mergulhar mais fundo, vamos nos familiarizar com a documentação desta função.

```shell-session
LSTATUS RegSetValueExA(
  [in]           HKEY       hKey,
  [in, optional] LPCSTR     lpValueName,
                 DWORD      Reserved,
  [in]           DWORD      dwType,
  [in]           const BYTE *lpData,
  [in]           DWORD      cbData
);
```

- `hKey1` é um identificador para a chave de registro onde você deseja definir um valor de registro.
- `lpValueName` é um ponteiro para uma string terminada em nulo que especifica o nome do valor do registro que você deseja definir. Neste caso, ele é nomeado como `DiscordUpdate`.
- O parâmetro `Reserved` é reservado e deve ser zero.
- `dwType` especifica o tipo de dados do valor do registro. É provável que seja uma constante inteira que representa o tipo de dados (por exemplo, `REG_SZ`para um valor de string).
- `(BYTE*)lpData` é um cast de tipo que converte a variável `_lpData_` em um ponteiro para um byte ( `BYTE*`). Isso é feito para garantir que os dados apontados por `_lpData_` sejam tratados como uma matriz de bytes, que é o formato esperado para dados binários no Registro do Windows. No nosso caso, isso é mostrado na exibição do buffer como `C:\Windows\Tasks\update.exe`.
- `cbData` é um inteiro que especifica o tamanho, em bytes, dos dados apontados por `_lpData_`.

![[win_dfir_apimon9.webp]]

Um ponto crítico dessa chamada de API é o parâmetro `lpData`, que revela a localização do backdoor, `C:\Windows\Tasks\update.exe`.

**Injeção de Processo**
Para examinar a criação do processo, vamos procurar pela função `CreateProcessA`. Vamos digitar `CreateProcess` na caixa de pesquisa e pressionar `Enter`.

![[win_dfir_apimon5_.webp]]

Apresentada abaixo está a sintaxe da função da API do Windows, `CreateProcessA`.

```shell-session
BOOL CreateProcessA(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,
  [in]                BOOL                  bInheritHandles,
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);
```

Um elemento intrigante dentro desta API é o parâmetro `lpCommandLine`. Ele divulga a linha de comando executada, que, neste contexto, é `C:\Windows\System32\comp.exe`. Notavelmente, o `lpCommandLine`pode ser especificado sem delinear o caminho executável completo no valor `lpApplicationName`.

Outro parâmetro essencial que vale a pena notar é `dwCreationFlags`, definido como `CREATE_SUSPENDED`. Isso indica que o thread primário do novo processo inicia em um estado suspenso e permanece inativo até que a função `ResumeThread` seja invocada.

O parâmetro `lpCommandLine` desta chamada de API esclarece o processo filho que foi iniciado, ou seja, `C:\Windows\System32\comp.exe`.

Mais abaixo também notamos funções relacionadas à injeção de processo sendo utilizadas por `discord.exe`.

![[disc_inj.webp]]
Todos os itens acima são fortes indicadores de injeção de processo.

#### Atividade do PowerShell
As transcrições do PowerShell registram meticulosamente tanto os comandos emitidos quanto suas respectivas saídas durante uma sessão do PowerShell. Ocasionalmente, dentro do diretório de documentos de um usuário, podemos tropeçar em arquivos de transcrição do PowerShell. Esses arquivos nos dão uma janela para as atividades do PowerShell registradas no sistema.

A captura de tela a seguir mostra os arquivos de transcrição do PowerShell aninhados no diretório de documentos do usuário em uma imagem forense montada.

![[win_dfir_accessdata_ps1.webp]]

Analisar detalhadamente as atividades relacionadas ao PowerShell pode ser fundamental durante as investigações.

Aqui estão algumas diretrizes recomendadas ao manipular dados do PowerShell.
- `Unusual Commands`: Procure por comandos do PowerShell que não sejam típicos em seu ambiente ou que sejam comumente associados a atividades maliciosas. Por exemplo, comandos para baixar arquivos da internet (``Invoke-WebRequest`` ou ``wget``), comandos que manipulam o registro ou aqueles que envolvem a criação de tarefas agendadas.
- `Script Execution`: Verifique a execução de scripts do PowerShell, especialmente se eles não forem assinados ou vierem de fontes não confiáveis. Scripts podem ser usados ​​para automatizar ações maliciosas.
- Comandos codificados: Atores maliciosos geralmente usam comandos PowerShell codificados ou ofuscados para evitar a detecção. Procure sinais de comandos codificados em transcrições.
- `Privilege Escalation`: Comandos que tentam aumentar privilégios, alterar permissões de usuários ou executar ações normalmente restritas a administradores podem ser suspeitos.
- `File Operations`: Verifique se há comandos do PowerShell que envolvam criar, mover ou excluir arquivos, especialmente em locais confidenciais do sistema.
- `Network Activity`: Procure por comandos relacionados à atividade de rede, como fazer solicitações HTTP ou iniciar conexões de rede. Eles podem ser indicativos de comunicações de comando e controle (C2).
- `Registry Manipulation`: Verifique se há comandos que envolvam a modificação do Registro do Windows, pois essa pode ser uma tática comum para persistência de malware.
- `Use of Uncommon Modules`: Se um script ou comando do PowerShell usar módulos incomuns ou fora do padrão, isso pode ser um sinal de atividade suspeita.
- `User Account Activity`: Procure por alterações em contas de usuários, incluindo criação, modificação ou exclusão. Atores maliciosos podem tentar criar ou manipular contas de usuários para persistência.
- `Scheduled Tasks`: Investigue a criação ou modificação de tarefas agendadas por meio do PowerShell. Este pode ser um método comum para persistência.
- `Repeated or Unusual Patterns`: Analise os padrões de comandos do PowerShell. Comandos repetidos e idênticos ou sequências incomuns de comandos podem indicar automação ou comportamento malicioso.
- `Execution of Unsigned Scripts`: A execução de scripts não assinados pode ser um sinal de atividade suspeita, especialmente se as políticas de execução de scripts estiverem definidas para restringir isso.












































































