Além dos componentes e objetos de alto nível, o AD DS contém outros objetos, como usuários, grupos e computadores.

## Criar objetos de usuário

No **Active Directory Domain Services (AD DS)**, cada usuário que precisa acessar os recursos da rede deve ter uma **conta de usuário**. Essa conta permite que os usuários se autentiquem no domínio do AD DS e acessem os recursos da rede.

No **Windows Server**, uma conta de usuário é um **objeto** que inclui:
- Nome de usuário
- Senha
- Associações a grupos

Além disso, uma conta de usuário pode ser configurada com diferentes definições baseadas nos requisitos organizacionais.

![[Pasted image 20240905165632.png]]


O **nome de usuário** e a **senha** de uma conta de usuário são as **credenciais** usadas para a autenticação. Além disso, o objeto de usuário no AD DS contém vários outros **atributos** que ajudam a descrever e gerenciar o usuário. Existem diversas ferramentas e opções para **criar** e **gerenciar** esses objetos de usuário no AD DS. Como :

- Centro Administrativo do Active Directory.
- Usuários e Computadores do Active Directory.
- Windows Admin Center.
- Windows PowerShell.
- A ferramenta de linha de comando `dsadd`.

### O que são contas de serviço gerenciado?
Muitos aplicativos instalam serviços no servidor que iniciam com o sistema ou são ativados por eventos específicos. Esses serviços operam geralmente em segundo plano e não requerem interação direta do usuário. Para iniciar e autenticar um serviço, você usa uma **conta de serviço**, que pode ser:

- **Conta local**: como as contas internas **Serviço Local**, **Serviço de Rede** ou **Sistema Local**.
- **Conta baseada em domínio**: uma conta local no Active Directory Domain Services (AD DS).

Embora as contas baseadas em domínio ofereçam benefícios, como centralização da administração, elas também apresentam desafios:

- **Gerenciamento de senha**: Pode exigir esforço adicional para garantir a segurança das senhas das contas de serviço.
- **Identificação de uso**: Pode ser difícil rastrear onde essas contas estão sendo utilizadas.
- **Gerenciamento de SPN**: A administração dos Nomes da Entidade de Serviço (SPN) pode ser complexa.

Para facilitar o gerenciamento dessas contas, o **Windows Server** oferece **contas de serviço gerenciado**. Essas contas:

- Simplificam o gerenciamento de senhas, automatizando as atualizações e garantindo que as senhas sejam seguras.
- Facilitam o gerenciamento de SPNs, reduzindo a complexidade na configuração e manutenção dos mesmos.

### O que são contas de serviço gerenciado de grupo?
**Contas de Serviço Gerenciado de Grupo** são uma extensão das contas de serviço gerenciado padrão que permitem o uso de uma única conta de serviço em múltiplos servidores dentro de um domínio. 

**Benefícios principais**
- **Execução em Múltiplos Servidores**: Ideal para ambientes com clusters ou múltiplos servidores, como NLB ou IIS.
- **Manutenção Automática de Senhas**: As senhas são atualizadas automaticamente, facilitando a segurança.
- **Gerenciamento Simplificado de SPNs**: Reduz a complexidade na configuração e manutenção dos SPNs.

Essas contas permitem que serviços sejam executados de forma consistente e segura em vários servidores, simplificando a administração e aumentando a eficiência.

Para dar suporte à funcionalidade de conta de serviço gerenciado de grupo, seu ambiente deve atender ao seguinte requisito:
- Você deve criar uma chave raiz do **KDS** em um controlador de domínio do Windows Server.

Para criar a chave raiz do KDS, execute o seguinte comando no Módulo do Active Directory para o Windows PowerShell em um controlador de domínio do Windows Server

```powershell
Add-KdsRootKey -EffectiveImmediately
```

Crie contas de serviço gerenciado de grupo usando o cmdlet `New-ADServiceAccount` do Windows PowerShell com o parâmetro `–PrinicipalsAllowedToRetrieveManagedPassword`.

**Por exemplo:**
```powershell
New-ADServiceAccount -Name LondonSQLFarm -PrincipalsAllowedToRetrieveManagedPassword SEA-SQL1, SEA-SQL2, SEA-SQL3
```

## O que são objetos de grupo?
Em redes pequenas, é prático atribuir permissões e direitos a contas de usuário individuais. No entanto, em grandes redes corporativas, isso se torna impraticável e ineficiente. 

**Solução mais eficiente:**
- **Criação de Grupos**: Em vez de atribuir permissões a cada usuário individualmente, você cria um grupo com os usuários que precisam do mesmo nível de acesso.
- **Atribuição de Permissões ao Grupo**: Defina as permissões para o grupo e todos os membros do grupo herdarão essas permissões automaticamente.

Isso simplifica a gestão de acesso e melhora a eficiência na administração de grandes redes. Mas antes de implementar um grupo temos de entender o escopo dos vários tipos de grupos do AD DS.

![[Pasted image 20240905171522.png]]

### Tipos de grupo
Em uma rede empresarial do Windows Server, há dois tipos de grupos, descritos na tabela a seguir.

#### Grupos de Segurança:
   - **Uso**: Atribuir permissões e controlar o acesso a recursos.
   - **Função**: Podem ser usados em entradas de permissão em ACLs (listas de controle de acesso) para gerenciar segurança.
   - **Habilitação**: Habilitados para segurança, permitindo o controle efetivo de acesso.

#### Grupos de Distribuição:
   - **Uso**: Gerenciar a distribuição de e-mails e comunicação em massa.
   - **Função**: Não são habilitados para segurança e são usados exclusivamente para distribuir mensagens.
   - **Nota**: Grupos de segurança também podem ser utilizados para distribuição em aplicativos de e-mail, além de seu propósito de segurança.

### Escopos de grupo
O Windows Server dá suporte ao escopo de grupo. O escopo de um grupo determina a gama de habilidades ou permissões de um grupo e a associação de grupo. Há quatro escopos de grupo.

#### Grupo Local:
   - **Uso**: Em servidores autônomos ou estações de trabalho.
   - **Escopo**: Disponível apenas no computador local.
   - **Permissões**: Atribui permissões somente para recursos locais.
   - **Membros**: Podem ser de qualquer lugar da floresta do AD DS.

#### Grupo Local de Domínio:
   - **Uso**: Para gerenciar acesso a recursos e atribuir direitos de gerenciamento.
   - **Escopo**: Local para o domínio em que reside.
   - **Permissões**: Atribui permissões para recursos locais de domínio.
   - **Membros**: Podem ser de qualquer lugar da floresta do AD DS.

#### Grupo Global:
   - **Uso**: Para consolidar usuários com características semelhantes.
   - **Escopo**: Permissões podem ser atribuídas em qualquer lugar da floresta.
   - **Permissões**: Atribui permissões em qualquer lugar da floresta.
   - **Membros**: Somente do domínio local.

#### Grupo Universal:
   - **Uso**: Ideal para redes multidomínio, combinando características de grupos locais de domínio e globais.
   - **Escopo**: Permissões podem ser atribuídas em qualquer lugar da floresta.
   - **Permissões**: Atribui permissões em qualquer lugar da floresta.
   - **Membros**: Podem ser de qualquer lugar da floresta do AD DS.

## O que são objetos de computador?
Os computadores, assim como os usuários, são entidades de segurança, porque:

- Eles têm uma conta com um nome e uma senha de entrada que o Windows altera de forma automática periodicamente.
- Eles se autenticam no domínio.
- Eles podem pertencer a grupos e ter acesso aos recursos, e você pode configurá-los usando a Política de Grupo.

Uma conta de computador inicia seu ciclo de vida quando você cria o objeto de computador e o ingressa em seu domínio. Depois que você ingressar a conta de computador no domínio, as tarefas administrativas diárias incluirão:
- Configurar propriedades do computador.
- Movimentar o computador entre as UOs.
- Gerenciar o próprio computador.
- Renomear, redefinir, desabilitar, habilitar e, eventualmente, excluir o objeto de computador.

![[Pasted image 20240905172744.png]]

### Contêiner Computadores
Antes de criar um objeto de computador no Active Directory Domain Services (AD DS), você deve escolher um local para armazená-lo. O contêiner **Computadores** é o local padrão para as contas de computador quando um computador ingressa no domínio.

Aqui estão algumas características importantes:

- **Tipo de Objeto**: O contêiner Computadores é um objeto da classe **Container** e tem o nome comum CN=Computers.
- **Limitações**:
  - Não é uma Unidade Organizacional (UO), portanto, você não pode criar UOs dentro do contêiner Computadores.
  - Não é possível vincular um Objeto de Política de Grupo (GPO) ao contêiner Computadores.
- **Recomendação**: É melhor criar UOs personalizadas para armazenar objetos de computador, pois elas oferecem mais flexibilidade e permitem a aplicação de GPOs.















































