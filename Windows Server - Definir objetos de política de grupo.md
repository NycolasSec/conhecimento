## O que é Política de Grupo?
A Política de Grupo é uma estrutura no Windows que permite gerenciar configurações em um domínio do Active Directory (AD DS). Ela é composta por componentes localizados no AD, controladores de domínio e em cada servidor e cliente Windows. As configurações de Política de Grupo são definidas em Objetos de Política de Grupo (GPOs). Um GPO contém uma ou mais configurações de política que se aplicam a usuários ou computadores, permitindo a administração centralizada de configurações e políticas dentro do domínio.

![[Pasted image 20240917171238.png]]

A Política de Grupo é uma ferramenta administrativa poderosa no Windows que permite aplicar configurações a muitos usuários e computadores. Com GPOs, você pode enviar configurações de forma precisa e em diferentes níveis, desde o computador local até o domínio. Ela é utilizada para definir configurações que não devem ser alteradas pelos usuários, padronizar ambientes de trabalho, fornecer segurança adicional e configurar aspectos avançados do sistema.

![[Pasted image 20240917171319.png]]

## O que são GPOs?
A Política de Grupo permite definir configurações individuais específicas, como desabilitar o acesso a ferramentas de edição do registro para um usuário. Essas configurações podem ser aplicadas a nível de usuário ou de computador. As configurações que afetam usuários são chamadas de **políticas de usuário**, enquanto as que afetam computadores são chamadas de **políticas de computador**.

>[!info]
>As configurações não afetam os grupos diretamente e se aplicam somente a objetos de usuário e computador.

A Política de Grupo gerencia várias configurações de política, e a estrutura da Política de Grupo é extensível. Você pode gerenciar quase todas as definições configuráveis com a Política de Grupo.

![[Pasted image 20240917171825.png]]

Para definir uma configuração de política:
1. No **Editor de Gerenciamento de Política de Grupo**, localize a configuração de política e selecione **Enter**. A caixa de diálogo da configuração de política Propriedades é exibida.
2. Altere o estado da política para **Habilitado** ou **Desabilitado**. A maioria das configurações de política pode ter três estados: Não Configurado, Habilitado e Desabilitado.
3. Se necessário, configure valores adicionais e, quando concluir, selecione **OK**.

Os GPOs armazenam as configurações de Política de Grupo. Em um novo GPO, cada configuração de política assume Não Configurado como padrão. Quando você habilita ou desabilita uma configuração de política, o Windows Server faz uma alteração na configuração de usuários e computadores aos quais o GPO é aplicado.

>[!info]
>Ao retornar uma configuração para o valor Não Configurado, você a retornará ao valor padrão.

Para criar um novo GPO em um domínio:
1. Em **Gerenciamento de Política de Grupo**, clique com o botão direito do mouse ou acesse o menu de contexto do contêiner **Objetos de Política de Grupo** e selecione **Novo**.
2. Para modificar as definições de configuração em um GPO, clique com o botão direito do mouse ou acesse o menu de contexto do GPO e selecione **Editar**. Isso abre o snap-in Editor de Gerenciamento de Política de Grupo.

O Editor de Gerenciamento de Política de Grupo organiza as configurações de política em uma hierarquia. Ela começa com dois principais nós:
1. **Configuração do Computador**  
2. **Configuração do Usuário**

Os GPOs são armazenados no contêiner **Objetos de Política de Grupo**. Dentro de cada GPO, as configurações são organizadas em dois níveis principais:
1. **Políticas**
2. **Preferências**

Esses níveis contêm pastas ou nós com as configurações específicas de política.

### O que são os GPOs iniciais?

Você pode usar um **GPO Inicial** como modelo para criar novos GPOs no Console de Gerenciamento de Política de Grupo. Esses GPOs Iniciais contêm apenas configurações de **Modelo Administrativo** e servem como um ponto de partida, refletindo melhores práticas para seu ambiente.

>[!info]
>O Console de Gerenciamento de Política de Grupo armazena os GPOs Iniciais em uma pasta chamada StarterGPOs, que fica em SYSVOL.

Para distribuir esses ``GPOs`` Iniciais para outros ambientes, você pode exportá-los e importá-los usando arquivos de gabinete (``.cab``). A Microsoft fornece ``GPOs`` Iniciais pré-configurados para sistemas operacionais Windows, que seguem as melhores práticas recomendadas.

>[!info]
>SYSVOL é uma pasta compartilhada nos controladores de domínio.














































