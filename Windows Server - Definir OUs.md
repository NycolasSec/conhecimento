Uma Unidade Organizacional (UO) é um contêiner em um domínio do Active Directory que agrupa usuários, computadores, grupos e outros objetos. É possível vincular Políticas de Grupo (GPOs) diretamente a uma UO para gerenciar os objetos nela contidos. Além disso, você pode designar um administrador para a UO e associar uma partição COM+ a ela.

#### Crie UOs no AD DS usando:

- Centro Administrativo do Active Directory.
- Usuários e Computadores do Active Directory.
- Windows Admin Center.
- Windows PowerShell com o módulo Active Directory para PowerShell.

## Por que criar UOs?
Uma Unidade Organizacional (UO) no AD DS serve para:

1. **Consolidar Objetos:** Facilita o gerenciamento de usuários, computadores e grupos ao aplicar Objetos de Política de Grupo (GPOs) a todos os objetos dentro da UO. GPOs permitem a configuração centralizada de políticas e configurações.
2. **Delegar Controle Administrativo:** Permite atribuir permissões para gerenciar objetos dentro da UO a usuários ou grupos específicos, além dos Administradores de Domínio.

Além disso, as UOs podem representar estruturas hierárquicas e lógicas da organização, como departamentos ou regiões geográficas, ajudando a gerenciar configurações e permissões com base na estrutura organizacional.

## O que são os contêineres genéricos?
Os contêineres internos no AD DS, como "Usuários" e "Computadores," armazenam objetos padrão e não permitem a aplicação direta de GPOs. Em contraste, as Unidades Organizacionais (UOs) têm recursos de gerenciamento mais avançados, como a vinculação de GPOs e a delegação de controle administrativo.

![[Pasted image 20240905175935.png]]

Ao instalar o AD DS, são criados automaticamente a UO de Controladores de Domínio e vários objetos de contêiner genéricos, que também ficam ocultos por padrão. Os objetos exibidos por padrão incluem:

- **Domínio**: Nível superior da hierarquia organizacional.
- **Contêiner interno**: Armazena grupos padrão.
- **Contêiner de computadores**: Local padrão para contas de computador.
- **Contêiner de Entidades de Segurança externas**: Armazena objetos confiáveis de domínios externos.
- **Contêiner Contas de Serviço Gerenciado**: Local padrão para contas de serviço gerenciado com gerenciamento automático de senhas.
- **Contêiner de usuários**: Armazena contas de usuário, grupos padrão, administrador e contas de convidado.
- **UO de Controladores de Domínio**: Local padrão para contas de computador dos controladores de domínio.

Há vários contêineres que você pode examinar ao selecionar Recursos Avançados. A tabela a seguir descreve os objetos que estão ocultos por padrão.

- **LostAndFound** - Esse contêiner possui objetos órfãos.
- **Dados de programa** - Esse contêiner possui dados do Active Directory para aplicativos da Microsoft, como Serviços de Federação do Active Directory (AD FS).
- **Sistema** - Esse contêiner possui as configurações internas do sistema.
- **Cotas NTDS** - Esse contêiner possui os dados de cota do serviço de diretório.
- **Dispositivos TPM** - Esse contêiner armazena as informações de recuperação para dispositivos TPM (Trusted Platform Module).

>[!NOTE] Os contêineres em um domínio do AD DS não podem ter GPOs vinculados a eles. Para vincular GPOs a fim de aplicar configurações e restrições, crie uma hierarquia de UOs e vincule os GPOs a elas.

## Usar um design hierárquico
As necessidades administrativas da organização determinam o design da hierarquia de Unidades Organizacionais (UOs) no Active Directory Domain Services (AD DS). A estrutura deve permitir uma administração eficiente e flexível dos recursos. UOs podem ser criadas com base em categorias como localização geográfica, função ou tipo de usuário. 

Você pode criar UOs dentro de outras UOs, por exemplo, uma UO para cada escritório e, dentro de cada escritório, UOs para diferentes departamentos e administradores de TI. Embora não haja um limite estrito para a profundidade da hierarquia, recomenda-se não exceder dez níveis para manter a gerenciabilidade, com a maioria das organizações usando cinco ou menos níveis. Aplicativos que interagem com o AD DS podem ter restrições adicionais sobre a profundidade hierárquica.








































































































































