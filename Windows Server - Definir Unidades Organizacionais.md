Uma **Unidade Organizacional (UO)** é um objeto de contêiner em um domínio que agrupa usuários, computadores, grupos e outros objetos. As principais características das UOs incluem:

- **Vinculação de GPOs**: É possível vincular Objetos de Política de Grupo (GPOs) diretamente a uma UO para gerenciar os usuários e computadores nela contidos.
- **Gerenciamento**: Você pode atribuir um gerenciador à UO.
- **Partição COM+**: É possível associar uma partição COM+ a uma UO.

Crie UOs no AD DS usando:

- Centro Administrativo do Active Directory.
- Usuários e Computadores do Active Directory.
- Windows Admin Center.
- Windows PowerShell com o módulo Active Directory para PowerShell.

## Por que criar UOs?
Há dois motivos para criar uma UO:

1. **Consolidação para gerenciamento e aplicação de GPOs**:
    - As UOs permitem agrupar objetos para aplicar configurações em massa.
    - As **GPOs** vinculadas a uma UO afetam todos os objetos dentro dela. Essas políticas ajudam a gerenciar e definir configurações para computadores e usuários.

1. **Delegação de controle administrativo**:
    - Permite atribuir permissões e delegar o controle administrativo de objetos dentro da UO a usuários ou grupos específicos, além do grupo Administradores de Domínio.

**Uso das UOs:**
- Representam a estrutura hierárquica e lógica da organização.
- Podem ser criadas para representar departamentos, regiões geográficas, ou uma combinação de ambos, facilitando o gerenciamento de configurações e políticas baseadas no modelo organizacional.

## O que são os contêineres genéricos?
No **Active Directory Domain Services (AD DS)**, existem vários **contêineres internos** (ou contêineres genéricos), como os contêineres de **usuários** e **computadores**. Estes contêineres:

- **Armazenam objetos do sistema** ou atuam como objetos pai padrão para objetos que você cria.
- **Não devem ser confundidos com UOs**.

**Diferenças principais entre contêineres e UOs:**

- **Recursos de Gerenciamento**: Contêineres têm recursos de gerenciamento limitados. Por exemplo, não é possível aplicar um GPO diretamente a um contêiner, ao contrário das UOs.

![[Pasted image 20240917162856.png]]

A instalação do AD DS cria a UO de Controladores de Domínio e vários objetos de contêiner genéricos por padrão. O AD DS usa principalmente alguns desses objetos padrão, que também ficam ocultos por padrão. Os seguintes objetos são exibidos por padrão:

- Domínio. O nível superior da hierarquia organizacional do domínio.
- Contêiner interno. Um contêiner que armazena vários grupos padrão.
- Contêiner de computadores. O local padrão para contas de computador que você cria no domínio.
- Contêiner de Entidades de Segurança externas. O local padrão para objetos confiáveis de domínios fora do domínio do AD DS local que você adiciona a um grupo no domínio do AD DS local.
- Contêiner Contas de Serviço Gerenciado. O local padrão para contas de serviço gerenciado. O AD DS fornece gerenciamento automático de senhas em contas de serviço gerenciado.
- Contêiner de usuários. O local padrão para contas de usuário e grupos que você cria no domínio. O contêiner Usuários também inclui o administrador, as contas de convidado para o domínio e alguns grupos padrão.
- UO de Controladores de Domínio. O local padrão das contas de computador dos controladores de domínio. Essa é a única UO que está presente em uma nova instalação do AD DS.

Há vários contêineres que você pode examinar ao selecionar Recursos Avançados. A tabela a seguir descreve os objetos que estão ocultos por padrão.

|**Objeto**|**Descrição**|
|---|---|
|LostAndFound|Esse contêiner possui objetos órfãos.|
|Dados de programa|Esse contêiner possui dados do Active Directory para aplicativos da Microsoft, como Serviços de Federação do Active Directory (AD FS).|
|Sistema|Esse contêiner possui as configurações internas do sistema.|
|Cotas NTDS|Esse contêiner possui os dados de cota do serviço de diretório.|
|Dispositivos TPM|Esse contêiner armazena as informações de recuperação para dispositivos TPM (Trusted Platform Module).|

>[!info]
>Os contêineres em um domínio do AD DS não podem ter GPOs vinculados a eles. Para vincular GPOs a fim de aplicar configurações e restrições, crie uma hierarquia de UOs e vincule os GPOs a elas.

## Usar um design hierárquico

#### Design da Hierarquia de UOs:
- **Influências no Design**: A hierarquia de UOs deve ser projetada para refletir as necessidades administrativas da organização. Pode ser influenciada por classificações como geográficas, funcionais, de recursos ou de usuários.
- **Objetivo**: A hierarquia deve permitir a administração flexível e eficiente dos recursos do AD DS.

#### Exemplo de Implementação:
- **Configuração de Computadores**: Se for necessário configurar todos os computadores de administradores de TI de maneira específica, esses computadores podem ser agrupados em uma UO e gerenciados por um GPO associado a essa UO.
- **Estrutura Hierárquica**: É possível criar UOs dentro de outras UOs. Por exemplo, para uma organização com vários escritórios, você pode criar uma UO para cada escritório e, dentro de cada escritório, criar UOs para administradores de TI e departamentos específicos.

#### Recomendações para Profundidade:
- **Limite de Níveis**: Embora não haja um limite estrito para a profundidade da hierarquia de UOs, recomenda-se restringi-la a no máximo dez níveis para garantir capacidade de gerenciamento. A maioria das organizações opta por cinco níveis ou menos para simplificar a administração.
- **Considerações Adicionais**: Alguns aplicativos que interagem com o AD DS podem ter restrições adicionais quanto à profundidade da hierarquia.