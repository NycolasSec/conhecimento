O gerenciamento do ambiente do AD DS é uma das tarefas mais comuns que um profissional de TI realiza. Existem várias ferramentas que você pode usar para gerenciar o AD DS.

## Centro Administrativo do Active Directory

O Centro Administrativo do Active Directory fornece uma GUI baseada no Windows PowerShell. Essa interface aprimorada permite que você execute o gerenciamento de objetos do AD DS usando a navegação orientada a tarefas e substitui a funcionalidade Usuários e Computadores do Active Directory.

![[Pasted image 20240917163458.png]]

As tarefas que você pode executar usando o Centro Administrativo do Active Directory incluem:

- Criar e gerenciar contas de usuário, computador e grupo.
- Criar e gerenciar UOs.
- Conectar-se a vários domínios e gerenciá-los em uma única instância do Centro Administrativo do Active Directory.
- Pesquisar e filtrar dados do AD DS criando consultas.
- Criar e gerenciar políticas de senha refinadas.
- Recuperar objetos da Lixeira do Active Directory.
- Gerenciar objetos que o recurso Controle de Acesso Dinâmico requer.

## Windows Admin Center

- **Descrição**: Console baseado na Web para gerenciamento de servidores e computadores com Windows 10.
- **Uso**: Ideal para gerenciar servidores, oferecendo uma alternativa às Ferramentas de Administração de Servidor Remoto (RSAT).
- **Compatibilidade**: Funciona com qualquer navegador moderno e pode ser instalado em computadores com Windows 10 e Windows Server com Experiência Desktop.

>[!info]
>Não é possível instalar o Windows Admin Center em um computador servidor configurado como um controlador de domínio do AD DS.

- **Funcionalidade**: Suporta a maioria das funcionalidades administrativas atuais para Windows Server e Windows 10.
- **Objetivo Futuro**: A Microsoft planeja expandir o suporte para incluir toda a funcionalidade administrativa atualmente disponível nas RSAT (Ferramentas de Administração de Servidor Remoto).
- **Exceções**: Embora o Windows Admin Center cubra a maior parte das funcionalidades administrativas, algumas exceções podem existir.

![[Pasted image 20240917163820.png]]

Para usar o Windows Admin Center, você deverá primeiro baixá-lo e instalá-lo. Depois de baixar e instalar o Windows Admin Center, habilite a porta TCP apropriada no firewall local. Em um computador com Windows 10 (no modo autônomo), o padrão é a 6516. No Windows Server (no modo de gateway), o padrão é TCP 443. Nos dois casos, você pode alterar isso durante a instalação.

## Ferramentas de Administração de Servidor Remoto
As RSAT são uma coleção de ferramentas que permitem que você gerencie funções e recursos do Windows Server remotamente.

![[Pasted image 20240917163905.png]]

>[!info]
>Não é mais preciso baixar as RSAT. Em vez disso, habilite-o no aplicativo Configurações. Em Configurações, pesquise Gerenciar recursos opcionais, selecione Adicionar um recurso e escolha as ferramentas RSAT apropriadas na lista retornada. Selecione Instalar para adicionar o recurso.

## Outras ferramentas de gerenciamento do AD DS

Outras ferramentas de gerenciamento que você usará para executar a administração do AD DS são descritas na tabela a seguir.

|Ferramenta de gerenciamento|**Descrição**|
|---|---|
|Módulo do Active Directory para o Windows PowerShell|O módulo do Active Directory para o Windows PowerShell dá suporte à administração do AD DS e é um dos componentes de gerenciamento mais importantes. O Gerenciador do Servidor e o Centro de Administração do Active Directory são baseados no Windows PowerShell e usam cmdlets para executar suas tarefas.|
|Usuários e computadores do Active Directory|Usuários e Computadores do Active Directory é um snap-in do MMC (Console de Gerenciamento Microsoft) que gerencia os recursos mais comuns, incluindo usuários, grupos e computadores. Embora muitos administradores estejam familiarizados com esse snap-in, o Centro Administrativo do Active Directory o substitui e fornece mais recursos.|
|Sites e Serviços do Active Directory|O snap-in Sites e Serviços do Active Directory do MMC gerencia a replicação, a topologia de rede e os serviços relacionados.|
|Domínios e Relações de Confiança do Active Directory|O snap-in Domínios e Relações de Confiança do Active Directory do MMC configura e mantém relações de confiança nos níveis funcionais de domínio e floresta.|
|Snap-in Esquema do Active Directory|O snap-in Esquema do Active Directory do MMC examina e modifica as definições de atributos e classes de objeto do AD DS. Você não precisa examiná-lo nem alterá-lo com frequência. Portanto, por padrão, o snap-in Esquema do Active Directory não está registrado.|


















