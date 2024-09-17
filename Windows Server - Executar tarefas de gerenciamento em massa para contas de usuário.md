Gerenciar múltiplos objetos do Active Directory (AD) de forma eficiente é essencial para administrar a rede. O gerenciamento em massa simplifica a execução de tarefas complexas, como mover ou atualizar contas de usuário, e é crucial para alterações em larga escala.

Utilizando ferramentas como o Windows PowerShell e o Centro Administrativo do Active Directory, você pode automatizar tarefas repetitivas, economizando tempo e reduzindo erros. Ambos os métodos permitem realizar as mesmas tarefas, mas com diferentes abordagens para atender às suas necessidades administrativas.

#### Mover Contas de Usuário para uma Nova Unidade Organizacional (UO):

1. No Centro Administrativo do Active Directory, acesse o domínio.
2. Clique com o botão direito na raiz do domínio e selecione **Localizar**, depois vá para a guia **Avançado**.
3. Escolha **Campo**, selecione **Usuário** e depois **É exatamente**.
4. No **Valor**, selecione o atributo desejado (exemplo: **City** com valor **Londres**).
5. Clique em **Adicionar** e depois em **Localizar agora** para ver as contas com o atributo selecionado.
6. Selecione as contas e, no menu de contexto, escolha **Mover** para transferi-las para a nova UO.
7. Na caixa de diálogo **Mover**, selecione a UO de destino e clique em **OK**.
8. Com as contas ainda selecionadas, clique com o botão direito e escolha **Desabilitar**.

#### Habilitar Contas e Forçar Redefinição de Senha:

1. Reabilite as contas desabilitadas clicando com o botão direito e selecionando **Habilitar**.
2. Vá para **Propriedades**, na guia **Conta**, marque **O usuário deve alterar a senha no próximo logon** e aplique as mudanças.

Essas etapas ajudam a gerenciar várias contas de usuário eficientemente, refletindo mudanças organizacionais no Active Directory.