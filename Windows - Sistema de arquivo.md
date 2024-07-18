Existem 5 tipos de sistemas de arquivos do Windows: FAT12, FAT16, FAT32, NTFS e exFAT. FAT12 e FAT16 não são mais usados ​​em sistemas Windows.

**FAT32** (File Allocation Table) é muito usado em tipos de dispositivos de armazenamento, como pen drives USB e cartões SD, mas também pode ser usado para formatar discos rígidos. Usa32 bits de dados para identificar clusters de dados em um dispositivo de armazenamento.

**`Pros of FAT32:`**

- Compatibilidade de dispositivos - pode ser usado em computadores, câmeras digitais, consoles de jogos, smartphones, tablets e muito mais.
- Compatibilidade entre sistemas operacionais - Funciona em todos os sistemas operacionais Windows a partir do Windows 95 e também é compatível com MacOS e Linux.

**`Cons of FAT32:`**

- Só pode ser usado com arquivos menores que 4 GB.
- Não há recursos integrados de proteção de dados ou compactação de arquivos.
- É necessário usar ferramentas de terceiros para criptografar arquivos.

**NTFS** (New Technology File System) é o sistema de arquivos padrão do Windows desde o Windows NT 3.1. NTFS tem melhor suporte para metadados e melhor desempenho devido á estruturação de dados aprimorada.

**`Pros of NTFS:`**

- O NTFS é confiável e pode restaurar a consistência do sistema de arquivos em caso de falha do sistema ou queda de energia.
- Fornece segurança ao nos permitir definir permissões granulares em arquivos e pastas.
- Suporta partições de tamanho muito grande.
- Possui registro integrado, o que significa que as modificações de arquivo (adição, modificação, exclusão) são registradas.

**`Cons of NTFS:`**

- A maioria dos dispositivos móveis não oferece suporte nativo ao NTFS.
- Dispositivos de mídia mais antigos, como TVs e câmeras digitais, não oferecem suporte para dispositivos de armazenamento NTFS.

## Permissões
Sistema de arquivos NTFS tem muitas permissões básicas e avançadas. Alguns dos principais tipos de permissão são :

|Tipo de permissão|Descrição|
|---|---|
|Controlo total|Permite ler, escrever, alterar e excluir arquivos/pastas.|
|Modificar|Permite ler, escrever e excluir arquivos/pastas.|
|Listar conteúdo da pasta|Permite visualizar e listar pastas e subpastas, bem como executar arquivos. Pastas somente herdam essa permissão.|
|Ler e executar|Permite visualizar e listar arquivos e subpastas, bem como executar arquivos. Arquivos e pastas herdam essa permissão.|
|Escrever|Permite adicionar arquivos a pastas e subpastas e gravar em um arquivo.|
|Ler|Permite visualizar e listar pastas e subpastas, além de visualizar o conteúdo de um arquivo.|
|Pasta transversal|Isso permite ou nega a capacidade de mover-se por pastas para alcançar outros arquivos ou pastas. Por exemplo, um usuário pode não ter permissão para listar o conteúdo do diretório ou visualizar arquivos no diretório de documentos ou aplicativos da web neste exemplo c:\users\bsmith\documents\webapps\backups\backup_02042020.zip, mas com as permissões Traverse Folder aplicadas, ele pode acessar o arquivo de backup.|
Arquivos e pastas herdam as permissões NTFS de sua pasta pai. Um Administrador pode desabilitar a herança para arquivos e pastas necessários e então definir as permissões diretamente em cada um.

### Lista de controle de acesso de controle de integridade (icacls)
Permissões NTFS podem ser gerenciadas usando GUI do File Explorer na aba de segurança, mas também podemos definir pela linha de comando usando o utilitário ``icacls``.

Podemos listar as permissões NTFS em um diretório específico executando `icacls` a partir do diretório de trabalho ou `icacls C:\Windows` em um diretório que não esteja nele no momento.

![[Pasted image 20240716161953.png]]

O nível de acesso ao recurso é listado após cada usuário na saída. As possíveis configurações de herança são:

- `(CI)`: recipiente herda
- `(OI)`: objeto herdar
- `(IO)`: herdar somente
- `(NP)`: não propagar herdar
- `(I)`: permissão herdada do contêiner pai

No exemplo acima, a conta `NT AUTHORITY\SYSTEM` tem permissões de object inherit, container inherit, inherit only e full access. Assim essa conta tem controle total sobre todos os objetos do sistema de arquivos neste diretório e subdiretórios.

As permissões básicas de acesso são as seguintes:

- `F` : acesso total
- `D` : apagar acesso
- `N` : sem acesso
- `M` : modificar acesso
- `RX` : acesso de leitura e execução
- `R` : acesso somente leitura
- `W`: acesso somente para gravação

Podemos adicionar e remover permissões com o comando `icacls`.

Aqui estamos executando `icacls` no contexto de uma conta de administrador local mostrando o diretório `C:\users` onde o usuário `joe` não tem nenhuma permissão de gravação.

![[Pasted image 20240716163326.png]]

Usando o comando, ``icacls c:\users /grant joe:f`` podemos conceder ao usuário joe controle total sobre o diretório, mas, dado que `(oi)` e `(ci)` não foram incluídos no comando, o usuário joe terá direitos apenas sobre a pasta `c:\users`, mas não sobre os subdiretórios do usuário e os arquivos contidos nelas.

![[Pasted image 20240716164317.png]]

![[Pasted image 20240716164332.png]]

Essas permissões podem ser revogadas usando o comando `icacls c:\users /remove joe`.

`icacls` é muito poderoso e pode ser usado em uma configuração de domínio para gerenciar permissões de arquivos e pastas.

Uma lista completa de `icacls`argumentos de linha de comando e configurações de permissão detalhadas podem ser encontradas [aqui](https://ss64.com/nt/icacls.html) .

---
## Referências
**`ss64`** : https://ss64.com/nt/icacls.html




































































