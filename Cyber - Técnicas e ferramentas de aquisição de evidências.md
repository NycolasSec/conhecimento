`Evidence acquisition` é uma fase crítica na perícia digital, envolvendo a coleta de artefatos e dados digitais de várias fontes para preservar evidências potenciais para análise.

Este processo requer ferramentas e técnicas especializadas para garantir a integridade, autenticidade e admissibilidade das evidências coletadas. Aqui está uma visão geral das técnicas de aquisição de evidências comumente usadas na perícia digital:

- Imagem Forense
- Extração de evidências baseadas em host e triagem rápida
- Extraindo evidências de rede

## Imagem Forense
A imagem forense é um processo essencial na perícia digital, onde se cria uma cópia exata, bit a bit, de mídias de armazenamento digital, como discos rígidos e unidades USB.

Esse processo é vital para preservar o estado original dos dados, garantir sua integridade e manter a admissibilidade das evidências em processos judiciais. A imagem forense permite que analistas examinem as evidências sem alterar ou comprometer os dados originais, desempenhando um papel crucial em investigações.

Abaixo estão algumas ferramentas e soluções de imagem forense:
- [FTK Imager](https://www.exterro.com/ftk-imager) : Desenvolvido pela AccessData (agora adquirida pela Exterro), o FTK Imager é uma das ferramentas de imagem de disco mais amplamente utilizadas no campo da segurança cibernética. Ele nos permite criar cópias perfeitas (ou imagens) de discos de computador para análise, preservando a integridade das evidências. Ele também nos permite visualizar e analisar o conteúdo de dispositivos de armazenamento de dados sem alterar os dados.
- [AFF4 Imager](https://github.com/Velocidex/c-aff4) : Uma ferramenta gratuita e de código aberto criada para criar e duplicar imagens de disco forenses. É fácil de usar e compatível com vários sistemas de arquivos. Um benefício do AFF4 Imager é sua capacidade de extrair arquivos com base no tempo de criação, volumes de segmento e reduzir o tempo gasto para geração de imagens por meio da compactação.
- `DD and DCFLDD`: Ambos são utilitários de linha de comando disponíveis em sistemas baseados em Unix (incluindo Linux e MacOS). DD é uma ferramenta versátil incluída na maioria dos sistemas baseados em Unix por padrão, enquanto DCFLDD é uma versão aprimorada do DD com recursos especificamente úteis para perícia forense, como hash.
- `Virtualization Tools`: Dado o uso prevalente da virtualização em sistemas modernos, os respondentes de incidentes frequentemente precisarão coletar evidências de ambientes virtuais. Dependendo da solução de virtualização específica, as evidências podem ser coletadas interrompendo temporariamente o sistema e transferindo o diretório que o abriga. Outro método é utilizar o recurso de snapshot presente em inúmeras ferramentas de software de virtualização.

**Exemplo 1: Imagem forense com FTK Imager**

Vamos agora ver uma demonstração de utilização `FTK Imager`para criar uma imagem de disco. Lembre-se de que você precisará de um meio de armazenamento auxiliar, como um disco rígido externo ou uma unidade flash USB, para salvar a imagem de disco resultante.

- Selecione `File`-> `Create Disk Image`.
	![[Pasted image 20240823111635.png]]
	
- Em seguida, selecione a fonte de mídia. Normalmente, é `Physical Drive`ou `Logical Drive`.
	![[Pasted image 20240823111707.png]]
	
- Escolha a unidade da qual você deseja criar uma imagem.
	![[Pasted image 20240823111802.png]]
	
- Especifique o destino da imagem.
	![[Pasted image 20240823111825.png]]
	
- Selecione o tipo de imagem desejado.
	![[Pasted image 20240823111926.png]]
	
- Insira detalhes da evidência.
	![[Pasted image 20240823111937.png]]
	
- Escolha a pasta de destino e o nome do arquivo para a imagem. Nesta etapa, você também pode ajustar as configurações para fragmentação e compactação de imagem.
	![[Pasted image 20240823111950.png]]
	
- Depois que todas as configurações forem confirmadas, clique em `Start`.
	![[Pasted image 20240823112007.png]]
	
- Você observará o progresso da imagem.
	![[Pasted image 20240823112016.png]]
	
- Se você optou por verificar a imagem, também verá o andamento da verificação.
	![[Pasted image 20240823112035.png]]
	
- Após a verificação da imagem, você receberá um resumo de imagens. Agora, você está preparado para analisar esse dump.
	![[Pasted image 20240823112046.png]]

**Exemplo 2: Montando uma imagem de disco com o Arsenal Image Mounter**

os agora ver outra demonstração de utilização do [Arsenal Image Mounter](https://arsenalrecon.com/downloads) para montar uma imagem de disco que criamos anteriormente (não a mencionada acima) de uma Máquina Virtual (VM) comprometida em execução no VMWare. O disco rígido virtual da VM foi armazenado como `HTBVM01-000003.vmdk`.

Depois de instalar o Arsenal Image Mounter, vamos garantir que o iniciamos com `administrative rights`. Na janela principal do Arsenal Image Mounter, vamos clicar no `Mount disk image`botão . De lá, navegaremos até o local do nosso `.VMDK`arquivo e o selecionaremos.

![[Pasted image 20240823112350.png]]

O Arsenal Image Mounter então iniciará sua análise do arquivo VMDK. Também teremos a opção de decidir se queremos montar o disco como `read-only`ou `read-write`, com base em nossos requisitos específicos.

_Escolher montar uma imagem de disco como somente leitura é um passo fundamental na perícia digital e resposta a incidentes. Essa abordagem é vital para preservar o estado original da evidência, garantindo que sua autenticidade e integridade permaneçam intactas._

Uma vez montada, a imagem aparecerá como uma unidade, atribuída a letra `D:\`.

![[Pasted image 20240823112444.png]]

## Extração de evidências baseadas em host e triagem rápida

Os sistemas operacionais modernos, como o Microsoft Windows, geram inúmeros artefatos de evidência decorrentes de ações como execução de aplicativos, modificações de arquivos e criação de contas de usuários. Esses artefatos deixam rastros que são valiosos para analistas de resposta a incidentes.

A natureza das evidências em um sistema host pode variar, e o termo "volatility" refere-se à persistência desses dados após eventos como logoffs ou desligamentos. Um tipo essencial de evidência volátil é a memória ativa do sistema.

Durante investigações, especialmente em casos de infecção por malware, a memória ativa se torna crítica, pois o malware frequentemente deixa rastros nela. Perder essa evidência pode comprometer a investigação. Ferramentas como o **FTK Imager** são comumente usadas para capturar a memória do sistema.

Algumas outras soluções de aquisição de memória são:
- [WinPmem](https://github.com/Velocidex/WinPmem) : WinPmem tem sido o driver de aquisição de memória de código aberto padrão para Windows por um longo tempo. Ele costumava viver no projeto Rekall, mas recentemente foi separado em seu próprio repositório.
- [DumpIt](https://www.magnetforensics.com/resources/magnet-dumpit-for-windows/) : Um utilitário simplista que gera um dump de memória física de máquinas Windows e Linux. No Windows, ele concatena memória física de sistema de 32 bits e 64 bits em um único arquivo de saída, tornando-o extremamente fácil de usar.
- [MemDump](http://www.nirsoft.net/utils/nircmd.html) : MemDump é um utilitário de linha de comando gratuito e direto que nos permite capturar o conteúdo da RAM de um sistema. É bastante benéfico em investigações forenses ou ao analisar um sistema para atividade maliciosa. Sua simplicidade e facilidade de uso o tornam uma escolha popular para aquisição de memória.
- [Belkasoft RAM Capturer](https://belkasoft.com/ram-capturer) : Esta é outra ferramenta poderosa que podemos usar para aquisição de memória, fornecida gratuitamente pela Belkasoft. Ela pode capturar a RAM de um computador Windows em execução, mesmo se houver proteção antidepuração ou antidumping ativa. Isso a torna uma ferramenta altamente eficaz para extrair o máximo de dados possível durante uma investigação forense ao vivo.
- [Magnet RAM Capture](https://www.magnetforensics.com/resources/magnet-ram-capture/) : Desenvolvida pela Magnet Forensics, esta ferramenta fornece uma maneira simples e gratuita de capturar a memória volátil de um sistema.
- [LiME (Linux Memory Extractor)](https://github.com/504ensicsLabs/LiME) : LiME é um Loadable Kernel Module (LKM) que permite a aquisição de memória volátil. LiME é único no sentido de que foi projetado para ser transparente ao sistema alvo, evitando muitas medidas anti-forenses comuns.

**Exemplo 1: Aquisição de memória com WinPmem**

Vamos agora ver uma demonstração de utilização `WinPmem`para aquisição de memória.

Para gerar um despejo de memória, basta executar o comando abaixo com privilégios de administrador.

```cmd-session
C:\Users\X\Downloads> winpmem_mini_x64_rc2.exe memdump.raw
```

![[Pasted image 20240823113135.png]]

**Exemplo 2: Aquisição de memória de VM**

Aqui estão as etapas para adquirir memória de uma máquina virtual (VM).

1. Abra as opções da VM em execução
2. Suspender a VM em execução
3. Localize o `.vmem`arquivo dentro do diretório da VM.

![[Pasted image 20240823113255.png]]
![[Pasted image 20240823113300.png]]

Por outro lado, dados não voláteis permanecem no disco rígido, geralmente persistindo durante desligamentos. Esta categoria inclui artefatos como:

- Registro
- Log de eventos do Windows
- Artefatos relacionados ao sistema (por exemplo, Prefetch, Amcache)
- Artefatos específicos do aplicativo (por exemplo, logs do IIS, histórico do navegador)

#### Triagem rápida
Essa abordagem enfatiza a coleta de dados de sistemas potencialmente comprometidos. O objetivo é centralizar dados de alto valor, simplificando sua indexação e análise.

Ao centralizar esses dados, os analistas podem implementar ferramentas e técnicas de forma mais eficaz, aprimorando os sistemas com o maior valor probatório. Essa abordagem direcionada permite um mergulho mais profundo na perícia digital, oferecendo uma imagem mais clara das ações do adversário.

Uma das melhores, se não a melhor, soluções rápidas de análise e extração de artefatos é [o KAPE (Kroll Artifact Parser and Extractor)](https://www.kroll.com/en/services/cyber-risk/incident-response-litigation-support/kroll-artifact-parser-extractor-kape) . Vamos ver como podemos empregar `KAPE`para recuperar dados forenses valiosos da imagem que montamos anteriormente com a ajuda do Arsenal Image Mounter ( `D:\` ).

`KAPE`é uma ferramenta poderosa no campo da perícia digital e resposta a incidentes. Projetado para auxiliar especialistas forenses e investigadores, o KAPE facilita a coleta e análise de evidências digitais de sistemas baseados em Windows.

Desenvolvido e mantido por `Kroll`(anteriormente conhecido como `Magnet Forensics`), o KAPE é celebrado por seus recursos abrangentes de coleta, adaptabilidade e interface intuitiva. O diagrama abaixo ilustra o fluxo operacional do KAPE.

![[Pasted image 20240823113521.png]]
Referência : [https://ericzimmerman.github.io/KapeDocs/#!index.md](https://ericzimmerman.github.io/KapeDocs/#!index.md)
O KAPE opera com base nos princípios de `Targets`e `Modules`. Esses elementos orientam a ferramenta no processamento de dados e na extração de artefatos forenses.

Quando alimentamos uma fonte para o KAPE, ele duplica arquivos específicos relacionados a forense para um diretório de saída designado, tudo isso enquanto mantém os metadados de cada arquivo.

![[Pasted image 20240823113950.png]]

Após o download, vamos descompactar o arquivo e iniciar o KAPE. Dentro do diretório KAPE, notamos dois arquivos executáveis: `gkape.exe`e `kape.exe`. O KAPE fornece aos usuários dois modos: CLI ( `kape.exe`) e GUI ( `gkape.exe`).

![[Pasted image 20240823114013.png]]

Vamos optar pela versão GUI para explorar as opções disponíveis de forma mais visual.

![[Pasted image 20240823115550.png]]

O ponto crucial do processo está na seleção das configurações de alvo apropriadas.

![[Pasted image 20240823115607.png]]

Na terminologia do KAPE, `Targets` refere-se aos artefatos específicos que pretendemos extrair de uma imagem ou sistema. Eles são então duplicados para o diretório de saída.

Os arquivos de destino do KAPE têm uma `.tkape`extensão e residem no diretório `<path to kape>\KAPE\Targets`. Por exemplo, o destino `RegistryHivesSystem.tkape`na captura de tela abaixo especifica os locais e máscaras de arquivo associados a hives de registro relacionados ao sistema.

Nesta configuração de destino, `RegistryHivesSystem.tkape`contém informações para coletar os arquivos com máscara de arquivo `SAM.LOG*` do diretório `C:\Windows\System32\config`.

![[Pasted image 20240823115833.png]]

O KAPE também oferece `Compound Targets`, que são essencialmente amálgamas de vários alvos. Esse recurso acelera o processo de coleta reunindo vários arquivos definidos em vários alvos em uma única execução. O arquivo `Compound`do diretório `KapeTriage`fornece uma visão geral do conteúdo desse alvo composto.

![[Pasted image 20240823115910.png]]

Vamos especificar nossa fonte (em nosso cenário, é `D:\`) e designar um local para armazenar os dados coletados. Também podemos determinar uma pasta de saída para abrigar os dados processados ​​do KAPE.

Depois de configurar nossas opções, vamos clicar no `Execute`botão para iniciar a coleta de dados.

![[Pasted image 20240823130345.png]]

Após a execução, o KAPE iniciará a coleta, armazenando os resultados no destino pré-determinado.

```shell-session
KAPE version 1.3.0.2, Author: Eric Zimmerman, Contact: https://www.kroll.com/kape (kape@kroll.com)

KAPE directory: C:\htb\dfir_module\data\kape\KAPE
Command line:   --tsource D: --tdest C:\htb\dfir_module\data\investigation\image --target !SANS_Triage --gui

System info: Machine name: REDACTED, 64-bit: True, User: REDACTED OS: Windows10 (10.0.22621)

Using Target operations
Found 18 targets. Expanding targets to file list...
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
<SNIP>
```

O diretório de saída do KAPE abriga os frutos da coleta e processamento de artefatos. O conteúdo exato desse diretório pode diferir com base nos artefatos selecionados e nas configurações definidas.

Em nossa demonstração, optamos pela configuração de destino da coleta `!SANS_Triage`. Vamos navegar até o diretório de saída do KAPE para inspecionar os dados coletados.

![[Pasted image 20240823132214.png]]

Pelos resultados exibidos, fica evidente que o arquivo `$MFT` foi coletado, junto com os diretórios `Users`e `Windows`.

Vale ressaltar que o KAPE também coletou os `Windows event logs`, que estão aninhados nas subpastas do diretório do Windows.

![[Pasted image 20240823132254.png]]

E se quiséssemos realizar coleta de artefatos remotamente e em massa? É aqui que as soluções EDR e [o Velociraptor](https://github.com/Velocidex/velociraptor) entram em cena.

Plataformas de Detecção e Resposta de Endpoint (EDR) oferecem vantagens significativas para analistas de resposta a incidentes ao permitir a aquisição e análise remotas de evidências digitais. Elas podem exibir binários executados ou arquivos adicionados recentemente, permitindo que analistas busquem indicadores em toda a rede em vez de verificar sistemas individuais. Além disso, as plataformas de EDR facilitam a coleta de evidências, sejam arquivos específicos ou pacotes forenses abrangentes, tornando a busca e coleta em larga escala mais eficiente.

[O Velociraptor](https://github.com/Velocidex/velociraptor) é uma ferramenta potente para reunir informações baseadas em host usando consultas Velociraptor Query Language (VQL). Além disso, o Velociraptor pode executar `Hunts` para acumular vários artefatos.

Um artefato frequentemente utilizado é o `Windows.KapeFiles.Targets`. Embora o KAPE (Kroll Artifact Parser and Extractor) em si não seja de código aberto, sua lógica de coleta de arquivos, codificada em YAML, é acessível por meio do [projeto KapeFiles](https://github.com/EricZimmerman/KapeFiles) . Essa abordagem é um grampo no Rapid Triage.


- Para utilizar o Velociraptor para artefatos KapeFiles:
Inicie uma nova Caçada.
	![[Pasted image 20240823132649.png]]
	![[Pasted image 20240823132658.png]]
	
- Escolha os artefatos `Windows.KapeFiles.Targets` para coleção.
	![[Pasted image 20240823132743.png]]
	
- Especifique a coleção a ser usada.
	![[Pasted image 20240823132849.png]]
	![[Pasted image 20240823132903.png]]
	
- Clique `Launch`para iniciar a caça.
	![[Pasted image 20240823132944.png]]
	
- Após a conclusão, baixe os resultados.
	![[Pasted image 20240823133038.png]]

Extrair o arquivo revelará arquivos relacionados aos artefatos coletados e todos os arquivos reunidos.

![[Pasted image 20240823133148.png]]

![[Pasted image 20240823133159.png]]

**Para coleta remota de despejo de memória usando Velociraptor:**
- Inicie uma nova Caçada, mas desta vez selecione o artefato `Windows.Memory.Acquisition`.
	![[Pasted image 20240823133312.png]]
	
- Após a conclusão da Hunt, baixe o arquivo resultante. Dentro dele, você encontrará um arquivo chamado `PhysicalMemory.raw`, contendo o dump de memória.
	![[Pasted image 20240823133418.png]]

## Extraindo evidências de rede
Ao longo de nossa exploração dos módulos no `SOC Analyst`caminho, nos aprofundamos extensivamente no campo da evidência de rede, um aspecto fundamental para qualquer analista de SOC.

- Primeiro, nossos módulos `Intro to Network Traffic Analysis`e `Intermediate Network Traffic Analysis`cobertos `traffic capture analysis`. Pense na captura de tráfego como um instantâneo de todas as conversas digitais acontecendo em nossa rede. Ferramentas como `Wireshark`ou `tcpdump`nos permitem capturar e dissecar esses pacotes, nos dando uma visão granular dos dados em trânsito.
- Então, nossos módulos `Working with IDS/IPS`e `Detecting Windows Attacks with Splunk`cobriram o uso de dados derivados de IDS/IPS. `Intrusion Detection Systems (IDS)`são nossos sentinelas vigilantes, monitorando constantemente o tráfego de rede em busca de sinais de atividade maliciosa. Quando eles detectam algo errado, eles nos alertam. Por outro lado, `Intrusion Prevention Systems (IPS)`vá um passo além. Eles não apenas detectam, mas também tomam ações predefinidas para bloquear ou prevenir essas atividades maliciosas.
- `Traffic flow`dados, geralmente obtidos de ferramentas como `NetFlow`ou `sFlow`, nos fornecem uma visão mais ampla do comportamento da nossa rede. Embora possam não nos dar os detalhes essenciais de cada pacote, eles oferecem uma visão geral de alto nível dos padrões de tráfego.
- Por fim, nossos confiáveis `firewalls`​​. Essas não são apenas barreiras que bloqueiam ou permitem tráfego com base em regras predefinidas. Firewalls modernos são feras inteligentes. Eles podem identificar aplicativos, usuários e até mesmo detectar e bloquear ameaças. Ao analisar logs de firewall, podemos descobrir tentativas de explorar vulnerabilidades, tentativas de acesso não autorizado e outras atividades maliciosas.




























