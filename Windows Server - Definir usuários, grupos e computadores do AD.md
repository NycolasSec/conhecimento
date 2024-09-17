No AD DS, você precisa providenciar uma conta para todos os usuários que precisam de acesso aos recursos da rede. Com essa conta, os usuários podem se autenticar no domínio do AD DS e acessar os recursos da rede.

No Windows Server, uma conta de usuário é um objeto que contém todas as informações que definem um usuário. Esse tipo de conta inclui:

- O nome de usuário.
- Uma senha de usuário.
- Associações de grupo.

Uma conta de usuário também contém configurações que você pode definir com base em seus requisitos organizacionais.

![[Pasted image 20240917154033.png]]

O nome de usuário e a senha de uma conta de usuário servem como as credenciais de entrada do usuário. Um objeto de usuário também inclui vários outros atributos que descrevem e gerenciam o usuário. Você pode usar as seguintes ferramentas para criar e gerenciar objetos de usuário no AD DS:

- Centro Administrativo do Active Directory.
- Usuários e Computadores do Active Directory.
- Windows Admin Center.
- Windows PowerShell.
- A ferramenta de linha de comando dsadd.

## O que são contas de serviço gerenciado?
Muitos aplicativos instalam serviços que são executados no servidor que hospeda o programa. Esses serviços geralmente iniciam com o servidor ou são ativados por eventos e operam em segundo plano sem interação do usuário. Para iniciar e autenticar um serviço, usa-se uma **conta de serviço**, que pode ser uma conta local do computador (como Serviço Local, Serviço de Rede ou Sistema Local) ou uma conta baseada em domínio localizada no **AD DS**.

Embora usar uma conta baseada em domínio para executar serviços ofereça vantagens em termos de centralização e administração, também apresenta vários desafios associados, como os seguintes:

- Pode ser necessário um esforço administrativo extra para gerenciar a senha da conta de serviço com segurança.
- Pode ser difícil determinar onde uma conta baseada em domínio está sendo usada como uma conta de serviço.
- Pode ser necessário um esforço administrativo extra para gerenciar o SPN (nome da entidade de serviço).

O Windows Server dá suporte a um objeto do AD DS, chamado de conta de serviço gerenciado, que você usa para facilitar o gerenciamento da conta de serviço. Uma conta de serviço gerenciado é uma classe de objeto do AD DS que permite:

- Gerenciamento simplificado de senhas.
- Gerenciamento simplificado de SPN.

### O que são contas de serviço gerenciado de grupo?
**Contas de Serviço Gerenciado de Grupo** oferecem uma extensão das **contas de serviço gerenciado padrão** para suportar serviços em múltiplos servidores dentro de um domínio. Em cenários com clusters de **Balanceamento de Carga de Rede (NLB)** ou servidores **IIS**, pode ser necessário que serviços em vários servidores utilizem a mesma conta de serviço.

Enquanto as contas de serviço gerenciado padrão não suportam essa funcionalidade em múltiplos servidores, as contas de serviço gerenciado de grupo permitem que vários servidores usem a mesma conta, mantendo benefícios como **manutenção automática de senhas** e **gerenciamento simplificado de SPNs (Service Principal Names)**.

Para dar suporte à funcionalidade de conta de serviço gerenciado de grupo, seu ambiente deve atender ao seguinte requisito:

- Você deve criar uma chave raiz do KDS em um controlador do domínio.

Para criar a chave raiz do KDS, execute o seguinte comando no Módulo do Active Directory para o Windows PowerShell em um controlador de domínio do Windows Server

```powershell
Add-KdsRootKey –EffectiveImmediately
```

Crie contas de serviço gerenciado de grupo usando o cmdlet `New-ADServiceAccount` do Windows PowerShell com o parâmetro `–PrinicipalsAllowedToRetrieveManagedPassword`.

**Por exemplo:**
```powershell
New-ADServiceAccount -Name LondonSQLFarm -PrincipalsAllowedToRetrieveManagedPassword SEA-SQL1, SEA-SQL2, SEA-SQL3
```

## O que são objetos de grupo?
Embora possa ser prático atribuir permissões e direitos a contas de usuário individuais em redes pequenas, isso se torna impraticável e ineficiente em grandes redes corporativas.

Por exemplo, se vários usuários precisarem do mesmo nível de acesso a uma pasta, será mais eficiente criar um grupo que contenha as contas de usuário necessárias e, em seguida, atribuir as permissões necessárias ao grupo.

>[!note]
>Como um benefício adicional, você pode alterar as permissões de arquivo dos usuários adicionando-os ou removendo-os de grupos em vez de editar as permissões de arquivo diretamente.

Antes de implementar grupos em sua organização, você deverá entender o escopo de vários tipos de grupo do AD DS.

![[Pasted image 20240917154707.png]]

### Tipos de grupo
Em uma rede empresarial do Windows Server, há dois tipos de grupos, descritos na tabela a seguir.

Expandir a tabela

|**Tipo de grupo**|**Descrição**|
|---|---|
|Segurança|Os grupos de segurança são habilitados para segurança, e você os utiliza para atribuir permissões a vários recursos. Você pode usar grupos de segurança em entradas de permissão em ACLs (listas de controle de acesso) para ajudar a controlar a segurança do acesso aos recursos. Se você quiser usar um grupo para gerenciar a segurança, ele deverá ser um grupo de segurança.|
|Distribuição|Os aplicativos de email normalmente usam grupos de distribuição, que não são habilitados para segurança. Você também pode usar grupos de segurança como um meio de distribuição para aplicativos de email.|

### Escopos de grupo
O Windows Server dá suporte ao escopo de grupo. O escopo de um grupo determina a gama de habilidades ou permissões de um grupo e a associação de grupo. Há quatro escopos de grupo.

1. **Local**:
   - Usado para servidores autônomos, estações de trabalho ou servidores membros de domínio que não são controladores de domínio.
   - Disponível apenas no computador onde foi criado.
   - Permite atribuir permissões e habilidades somente a recursos locais.
   - Membros podem vir de qualquer lugar na floresta do AD DS.

2. **Local de Domínio**:
   - Usado para gerenciar acesso a recursos e atribuir direitos dentro de um domínio específico.
   - Disponível somente em controladores de domínio do domínio onde reside.
   - Permite atribuir permissões somente a recursos no domínio local.
   - Membros podem vir de qualquer lugar na floresta do AD DS.

3. **Global**:
   - Usado para agrupar usuários com características semelhantes, como aqueles de um departamento ou localização geográfica.
   - Permite atribuir permissões e habilidades em qualquer lugar da floresta.
   - Membros podem ser apenas do domínio local e incluir usuários, computadores e grupos globais do mesmo domínio.

4. **Universal**:
   - Ideal para redes multidomínio, combinando características de grupos locais de domínio e globais.
   - Permite atribuir permissões e habilidades em qualquer lugar da floresta.
   - Membros podem vir de qualquer lugar na floresta do AD DS.

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

![[Pasted image 20240917155052.png]]

### Contêiner Computadores

Antes de criar um objeto de computador no **AD DS**, é necessário definir um local para o mesmo. O **contêiner Computadores** é o local padrão para as contas de computador quando um computador ingressa no domínio. No entanto, o contêiner Computadores é um **objeto da classe Container** e não uma Unidade Organizacional (UO), identificado como **CN=Computers**.

Diferenças importantes entre um contêiner e uma UO incluem:

- **Subdivisão**: Não é possível criar UOs dentro de um contêiner, então o contêiner Computadores não pode ser subdividido.
- **Política de Grupo**: Não é possível vincular um Objeto de Política de Grupo a um contêiner.

Portanto, recomenda-se criar UOs personalizadas para hospedar objetos de computador, ao invés de usar o contêiner Computadores.








































