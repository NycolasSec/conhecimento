Em um sistema operacional Windows, o diretório raiz é `<letra_da_unidade>:\` (`C:\`). O diretório raiz é onde o sistema é instalado. Outras unidade recebem outras letras, por exemplo, Dados(E:). A estrutura de diretório da partição de inicialização é a seguinte:

| Diretório                   | Função                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Perflogs                    | Pode armazenar logs de desempenho do Windows, mas está vazio por padrão.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Arquivos de Programas       | Em sistemas de 32 bits, todos os programas de 16 bits e 32 bits são instalados aqui. Em sistemas de 64 bits, apenas programas de 64 bits são instalados aqui.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Arquivos de Programas (x86) | Programas de 32 e 16 bits são instalados aqui em edições de 64 bits do Windows.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Dados do Programa           | Esta é uma pasta oculta que contém dados essenciais para a execução de certos programas instalados. Esses dados são acessíveis pelo programa, não importa qual usuário o esteja executando.                                                                                                                                                                                                                                                                                                                                                                                                               |
| Usuários                    | Esta pasta contém perfis de usuário para cada usuário que efetua login no sistema e contém as duas pastas Pública e Padrão.                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Padrão                      | Este é o modelo de perfil de usuário padrão para todos os usuários criados. Sempre que um novo usuário é adicionado ao sistema, seu perfil é baseado no perfil Padrão.                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Público                     | Esta pasta é destinada a usuários de computador para compartilhar arquivos e é acessível a todos os usuários por padrão. Esta pasta é compartilhada pela rede por padrão, mas requer uma conta de rede válida para acessar.                                                                                                                                                                                                                                                                                                                                                                               |
| Dados do aplicativo         | Os dados e configurações do aplicativo por usuário são armazenados em uma subpasta oculta do usuário (ou seja, cliff.moore\AppData). Cada uma dessas pastas contém três subpastas. A pasta Roaming contém dados independentes da máquina que devem seguir o perfil do usuário, como dicionários personalizados. A pasta Local é específica para o próprio computador e nunca é sincronizada pela rede. LocalLow é semelhante à pasta Local, mas tem um nível de integridade de dados mais baixo. Portanto, pode ser usada, por exemplo, por um navegador da web definido para o modo protegido ou seguro. |
| janelas                     | A maioria dos arquivos necessários para o sistema operacional Windows estão contidos aqui.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Sistema, System32, SysWOW64 | Contém todas as DLLs necessárias para os principais recursos do Windows e a API do Windows. O sistema operacional pesquisa essas pastas sempre que um programa solicita o carregamento de uma DLL sem especificar um caminho absoluto.                                                                                                                                                                                                                                                                                                                                                                    |
| WinSxS                      | O Windows Component Store contém uma cópia de todos os componentes, atualizações e service packs do Windows.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |


























































































































