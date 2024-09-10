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














































