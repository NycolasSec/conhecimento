O Active Directory Domain Services (AD DS) é a base das redes corporativas que utilizam Windows. Ele armazena informações de todos os objetos do domínio, como contas de usuário e computador, além de grupos, em um banco de dados central. O AD DS oferece um diretório pesquisável e hierárquico, permitindo aplicar configurações de segurança aos objetos.

Ele possui componentes lógicos e físicos que devem ser compreendidos para gerenciar a infraestrutura de forma eficiente. O AD DS permite realizar ações como instalar e configurar aplicativos, gerenciar segurança, habilitar acesso remoto e DirectAccess, além de emitir certificados digitais.

## O que são os componentes lógicos?
Os componentes lógicos do AD DS são estruturas que você usa para implementar um design do AD DS apropriado em uma organização.

#### Partição
   - Segmentos do banco de dados AD DS (`Ntds.dit`) que armazenam diferentes tipos de dados, como esquema, configuração e domínio.
   - As partições são replicadas entre múltiplos controladores de domínio através da replicação de diretório.

#### Esquema
   - Conjunto de definições dos tipos de objetos e atributos utilizados no AD DS.

#### Domínio
   - Contêiner administrativo lógico para objetos como usuários, computadores e grupos.
   - Mapeado para uma partição específica e pode estabelecer relações hierárquicas com outros domínios.

#### Árvore de Domínio
   - Coleção hierárquica de domínios que compartilham um domínio raiz comum e um namespace DNS contíguo.

#### Floresta
   - Conjunto de um ou mais domínios que possuem uma raiz AD DS comum, esquema e catálogo global compartilhados.

#### Unidade Organizacional (UO)
   - Contêiner para usuários, grupos e computadores.
   - Facilita a delegação de direitos administrativos e a aplicação de Objetos de Política de Grupo (GPOs).

#### Contêiner
   - Objeto organizacional utilizado no AD DS para estruturar a organização.
   - Pode ser padrão ou personalizado, mas não permite a vinculação de GPOs.

## Quais são os componentes físicos?
 Os componentes físicos no AD DS são aqueles que são tangíveis ou que descrevam componentes tangíveis no mundo real.
 
![[Pasted image 20240905165155.png]]


#### Controlador de Domínio
   - Contém uma cópia do banco de dados do AD DS.
   - Cada controlador pode processar e replicar alterações em outros controladores do domínio.

#### Armazenamento de Dados
   - Cada controlador de domínio possui uma cópia do banco de dados do AD DS.
   - Os dados são armazenados no arquivo `Ntds.dit` e arquivos de log associados, localizados na pasta `C:\Windows\NTDS`.

#### Servidor de Catálogo Global
   - Um controlador de domínio que contém uma cópia parcial e somente leitura dos objetos em toda a floresta.
   - Acelera buscas por objetos armazenados em outros controladores de domínio na floresta.

#### RODC (Controlador de Domínio Somente Leitura)
   - Instalação do AD DS usada em locais com segurança física limitada ou suporte técnico menor.
   - Comum em filiais de empresas.

#### Site
   - Representa um local físico para agrupar objetos do AD DS, como computadores e serviços.

#### Sub-rede
   - Parte dos endereços IP atribuídos a computadores em um site.
   - Um site pode conter várias sub-redes.





















































































