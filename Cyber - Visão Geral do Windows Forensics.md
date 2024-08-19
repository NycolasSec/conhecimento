## NTFS

- `File Metadata`: O NTFS armazena metadados extensivos para cada arquivo, incluindo hora de criação, hora de modificação, hora de acesso e informações de atributos (como atributos de arquivo somente leitura, ocultos ou de sistema). Analisar esses carimbos de data/hora pode ajudar a estabelecer cronogramas e reconstruir atividades do usuário.
- `MFT Entries`: A Master File Table (MFT) é um componente crucial do NTFS que armazena metadados para todos os arquivos e diretórios em um volume. Examinar entradas MFT fornece insights sobre nomes de arquivos, tamanhos, carimbos de data/hora e locais de armazenamento de dados. Quando os arquivos são excluídos, suas entradas MFT são marcadas como disponíveis, mas os dados podem permanecer no disco até serem substituídos.

- `File Slack and Unallocated Space`: Espaço não alocado em um volume NTFS pode conter resquícios de arquivos excluídos ou fragmentos de dados. A folga de arquivo se refere à parte não utilizada de um cluster que pode conter dados de um arquivo anterior. Ferramentas forenses digitais podem ajudar a recuperar e analisar dados dessas áreas.
- `File Signatures`: Cabeçalhos e assinaturas de arquivo podem ser úteis para identificar tipos de arquivo mesmo quando as extensões de arquivo foram alteradas ou obscurecidas. Essas informações são críticas para reconstruir os tipos de arquivos presentes em um sistema.
- `USN Journal`: O Update Sequence Number (USN) Journal é um log mantido pelo NTFS para registrar alterações feitas em arquivos e diretórios. Investigadores forenses podem analisar o USN Journal para rastrear modificações, exclusões e renomeações de arquivos.
- `LNK Files`: Os arquivos de atalho do Windows (arquivos LNK) contêm informações sobre o arquivo ou programa de destino, bem como carimbos de data/hora e metadados. Esses arquivos podem fornecer insights sobre arquivos acessados ​​recentemente ou programas executados.
- `Prefetch Files`: Arquivos de pré-busca são gerados pelo Windows para melhorar o desempenho de inicialização de aplicativos. Esses arquivos podem indicar quais programas foram executados no sistema e quando foram executados pela última vez.
- `Registry Hives`: Embora não estejam diretamente relacionados ao sistema de arquivos, os hives do Registro do Windows contêm informações importantes sobre configuração e sistema. Atividades maliciosas ou alterações não autorizadas podem deixar rastros no registro, que os investigadores forenses analisam para entender as modificações do sistema.
- `Shellbags`: Shellbags são entradas de registro que armazenam configurações de visualização de pastas, como posições de janelas e preferências de classificação. Analisar shellbags pode revelar padrões de navegação do usuário e potencialmente identificar pastas acessadas.
- `Thumbnail Cache`: Caches de miniaturas armazenam visualizações em miniatura de imagens e documentos. Esses caches podem revelar arquivos que foram visualizados recentemente, mesmo que os arquivos originais tenham sido excluídos.
- `Recycle Bin`: A Lixeira contém arquivos que foram excluídos do sistema de arquivos. Analisar a Lixeira pode ajudar a recuperar arquivos excluídos e fornecer insights sobre as ações do usuário.
- `Alternate Data Streams (ADS)`: ADS são fluxos adicionais de dados associados a arquivos. Atores maliciosos podem usar ADS para ocultar dados, e investigadores forenses precisam examinar esses fluxos para garantir uma análise abrangente.
- `Volume Shadow Copies`: O NTFS suporta Cópias de Sombra de Volume, que são instantâneos do sistema de arquivos em diferentes pontos no tempo. Essas cópias podem ser valiosas para recuperação de dados e análise de alterações feitas ao longo do tempo.
- `Security Descriptors and ACLs`: Listas de Controle de Acesso (ACLs) e descritores de segurança determinam permissões de arquivo e pasta. Analisar esses artefatos ajuda a entender os direitos de acesso do usuário e potenciais violações de segurança.

## Logs de eventos do Windows

Os **Windows Event Logs** são uma parte essencial do sistema operacional Windows, registrando atividades de diferentes componentes, como o sistema, aplicativos, serviços e outros. Esses logs fornecem informações valiosas sobre erros de aplicativos, eventos de segurança e diagnósticos, sendo amplamente utilizados por profissionais de segurança cibernética para análise e detecção de intrusões.

Táticas adversárias, como compromissos iniciais, elevação de privilégios e movimentação lateral, são frequentemente registradas nesses logs. Investigadores podem acessá-los diretamente no caminho padrão `C:\Windows\System32\winevt\logs` para análise offline. A análise detalhada desses logs é essencial para entender atividades maliciosas no ambiente Windows.

## Artefatos de execução

**Windows execution artifacts** são evidências deixadas em um sistema Windows durante a execução de programas e processos. Eles oferecem informações importantes para investigações forenses, resposta a incidentes e análise de segurança, permitindo aos investigadores reconstruir cronogramas, identificar atividades maliciosas e entender padrões de comportamento.


- `Prefetch Files`: O Windows mantém uma pasta prefetch que contém metadados sobre a execução de vários aplicativos. Arquivos prefetch registram informações como caminhos de arquivo, contagens de execução e carimbos de data/hora de quando os aplicativos foram executados. Analisar arquivos prefetch pode revelar um histórico de programas executados e a ordem em que foram executados.
- `Shimcache`: Shimcache é um mecanismo do Windows que registra informações sobre a execução do programa para auxiliar com otimizações de compatibilidade e desempenho. Ele registra detalhes como caminhos de arquivo, carimbos de data/hora de execução e sinalizadores indicando se um programa foi executado. Shimcache pode ajudar investigadores a identificar programas executados recentemente e seus arquivos associados.
- `Amcache`: Amcache é um banco de dados introduzido no Windows 8 que armazena informações sobre aplicativos e executáveis ​​instalados. Ele inclui detalhes como caminhos de arquivo, tamanhos, assinaturas digitais e carimbos de data/hora de quando os aplicativos foram executados pela última vez. Analisar o Amcache pode fornecer insights sobre o histórico de execução do programa e identificar software potencialmente suspeito ou não autorizado.
- `UserAssist`: UserAssist é uma chave de registro que mantém informações sobre programas executados por usuários. Ela registra detalhes como nomes de aplicativos, contagens de execução e carimbos de data/hora. Analisar artefatos do UserAssist pode revelar um histórico de aplicativos executados e atividade do usuário.
- `RunMRU Lists`: As listas RunMRU (Most Recently Used) no Registro do Windows armazenam informações sobre programas executados recentemente de vários locais, como as chaves `Run`e `RunOnce`. Essas listas podem indicar quais programas foram executados, quando foram executados e potencialmente revelar a atividade do usuário.
- `Jump Lists`: As Jump Lists armazenam informações sobre arquivos, pastas e tarefas acessados ​​recentemente associados a aplicativos específicos. Elas podem fornecer insights sobre atividades do usuário e arquivos usados ​​recentemente.
- `Shortcut (LNK) Files`: Arquivos de atalho podem conter informações sobre o executável alvo, caminhos de arquivo, carimbos de data/hora e interações do usuário. Analisar arquivos LNK pode revelar detalhes sobre programas executados e o contexto em que foram executados.
- `Recent Items`: A pasta Itens Recentes mantém uma lista de arquivos abertos recentemente. Ela pode fornecer informações sobre documentos acessados ​​recentemente e atividade do usuário.
- `Windows Event Logs`: Vários logs de eventos do Windows, como os logs de Segurança, Aplicativo e Sistema, registram eventos relacionados à execução de programas, incluindo criação e encerramento de processos, travamentos de aplicativos e muito mais.

|Artifact|Location/Registry Key|Data Stored|
|---|---|---|
|Prefetch Files|C:\Windows\Prefetch|Metadata about executed applications (file paths, timestamps, execution count)|
|Shimcache|Registry: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache|Program execution details (file paths, timestamps, flags)|
|Amcache|C:\Windows\AppCompat\Programs\Amcache.hve (Binary Registry Hive)|Application details (file paths, sizes, digital signatures, timestamps)|
|UserAssist|Registry: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist|Executed program details (application names, execution counts, timestamps)|
|RunMRU Lists|Registry: HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU|Recently executed programs and their command lines|
|Jump Lists|User-specific folders (e.g., %AppData%\Microsoft\Windows\Recent)|Recently accessed files, folders, and tasks associated with applications|
|Shortcut (LNK) Files|Various locations (e.g., Desktop, Start Menu)|Target executable, file paths, timestamps, user interactions|
|Recent Items|User-specific folders (e.g., %AppData%\Microsoft\Windows\Recent)|Recently accessed files|
|Windows Event Logs|C:\Windows\System32\winevt\Logs|Various event logs containing process creation, termination, and other events|

## Artefatos de persistência do Windows




















































































































