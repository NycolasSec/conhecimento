## Criar uma Unidade organizacional

A criação de uma UO (unidade organizacional) no Active Directory fornece uma estrutura hierárquica para organizar objetos, como usuários, grupos e computadores. Em seguida, você pode usar essa estrutura para aplicar políticas de grupo e delegar controle administrativo sobre esses recursos.

Para criar uma UO (Unidade Organizacional) no Active Directory:

1. Abra o Centro Administrativo do Active Directory.
2. No painel de navegação, clique com o botão direito do mouse no domínio ou na pasta em que você deseja adicionar a nova UO.
3. Selecione **Novo** e selecione **Unidade Organizacional**.
4. Na caixa de diálogo exibida, digite o nome da nova UO.
5. Selecione **OK** para criar a UO.

## Criar uma nova conta de usuário

Ao criar uma nova conta de usuário no Active Directory, você fornece a um indivíduo permissões de acesso e os recursos necessários para sua função dentro da organização.

Para criar uma nova conta de usuário em uma UO no Active Directory:

1. No Centro Administrativo do Active Directory, navegue até a UO desejada, onde você deseja criar a nova conta de usuário.
2. Clique com o botão direito do mouse na UO, selecione **Novo** e selecione **Usuário**.
3. Quando o assistente for aberto, insira os dados do usuário. Isso inclui o nome completo do novo usuário, o nome de logon do usuário e quaisquer outros detalhes relevantes.
4. Depois de inserir todas as informações necessárias, clique em **Avançar**.
5. Em seguida, você será solicitado a inserir uma senha para a nova conta de usuário e confirmá-la.  
    Você também pode definir várias opções de conta de usuário, como expiração de senha ou a necessidade do usuário alterar sua senha no próximo logon.
6. Depois de definir a senha e outras opções, clique em **Próximo**.
7. Analise as informações e clique em **Concluir**.

## Criar um novo grupo

A criação de um grupo no Active Directory permite gerenciar uma coleção de usuários, computadores, contatos e outros grupos como uma única entidade. Os grupos simplificam a atribuição de permissões e direitos em várias contas.

Para criar um novo grupo em uma UO específica no Active Directory:

1. No Centro Administrativo do Active Directory, navegue até a UO desejada, onde você deseja criar o novo grupo.
2. Clique com o botão direito do mouse na UO, selecione **Novo** e selecione **Grupo**.
3. Na caixa de diálogo **Grupo**, insira o nome do grupo e escolha o escopo e o tipo do grupo.
4. Clique em **OK** para criar o novo grupo.

### Para adicionar um usuário a um grupo

Para adicionar uma conta de usuário a um grupo em uma UO no Active Directory:

1. No Centro Administrativo do Active Directory, navegue até a UO em que o grupo está localizado.
2. Localize e selecione o grupo ao qual você deseja adicionar o usuário.
3. No painel de ações do lado direito, selecione **Membros**.
4. Na seção Membros, clique em **Adicionar**.
5. Na caixa de diálogo **Selecionar Usuários, Contatos, Computadores, Contas de Serviço ou Grupos**, digite o nome do usuário que você deseja adicionar ao grupo e clique em **OK**.  
    O usuário agora será listado como um membro do grupo.
6. Clique em **OK** para confirmar e fechar a caixa de diálogo.