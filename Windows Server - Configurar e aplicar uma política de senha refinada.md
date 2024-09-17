Se uma organização precisar de diferentes políticas de senha para grupos distintos de usuários, como atualizações mais frequentes para fornecedores e senhas mais seguras para administradores, a política de senha padrão do domínio não é suficiente.

Nesse caso, você pode usar uma FGPP (política de senha refinada) no Active Directory para definir e aplicar políticas de senha específicas para diferentes grupos de usuários.

## Configurar uma FGPP

1. Clique em **ADAC (Centro Administrativo do Active Directory)**.
2. Expanda o domínio e clique no contêiner **Sistema**.
3. Localize o Contêiner de **Configuração de Senha**.
4. Clique com o botão direito do mouse no Contêiner de Configuração de Senha.
5. Selecione **Novo** e, em seguida, **Configurações de senha**.
6. Configure os requisitos de senha.
7. Selecione **OK**.

A FGPP foi criada e o procedimento pode ser concluído várias vezes para criar diferentes variações ou versões de uma FGPP. Depois que o PSO (objeto Configuração de Senha) for criado, ele precisará ser associado a usuários ou grupos a serem impostos.

## Aplicar uma FGPPv

1. Ainda no **Contêiner de Configuração de Senha**, selecione o PSO recém-criado.
2. No modo de exibição Estendido, em **Aplica-se diretamente a**, selecione **Adicionar**.
3. Insira o nome do usuário ou grupo e selecione **OK**.

Esse processo pode ser repetido várias vezes para criar um domínio mais seguro e personalizado que restringe o acesso para maximizar a segurança e reduzir o risco de senhas comprometidas.