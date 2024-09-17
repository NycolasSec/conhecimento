Ao atribuir direitos de conta de usuário, é essencial considerar o maior risco de segurança associado a contas com privilégios elevados. Contas com privilégios internos no Active Directory e contas locais em estações de trabalho e servidores são particularmente arriscadas.

Para minimizar esse risco, aplique o princípio do privilégio mínimo, evitando que contas de Administrador local sejam usadas para serviços ou logons em computadores locais. Utilize GPOs para restringir o uso de contas de administrador em sistemas do domínio.

Por exemplo, você pode adicionar a conta de Administrador aos seguintes direitos de usuário:
- Negar acesso a este computador pela rede.
- Recusar logon como um trabalho em lote.
- Recusar logon como um serviço.
- Recusar logon por meio dos Serviços de Área de Trabalho Remota.

Negar a uma conta de usuário o direito de entrar como um serviço é uma prática de segurança recomendada porque limita o risco de abuso de contas de serviço. Contas de serviço, frequentemente configuradas com privilégios elevados e executadas em segundo plano, são alvos comuns de invasores. Restringir esse direito reduz a superfície de ataque e o risco de acesso não autorizado a sistemas e dados críticos.

## Impedir a entrada não autorizada por meio de contas de serviço
Para negar a uma conta de usuário a capacidade de entrar como um serviço usando a Política de Grupo, siga estas etapas:

1. Abra o Console de Gerenciamento de Diretiva de Grupo.
2. Crie um GPO (objeto de política de grupo) ou selecione um existente.
3. Edite o GPO e navegue até a **Atribuição de Direitos do Usuário**, que está localizada em Configuração do Computador\Políticas\Configurações do Windows\Configurações de Segurança\Atribuição de Direitos do Usuário.
4. Localize e selecione a configuração de política **Negar logon como um serviço**.
5. Selecione **Adicionar Usuário ou Grupo** e clique em **Localizar**.
6. Selecione a conta de usuário para a qual você deseja negar esse direito.
7. Verifique se a configuração de política está habilitada e selecione **OK** para aplicar as alterações.


















