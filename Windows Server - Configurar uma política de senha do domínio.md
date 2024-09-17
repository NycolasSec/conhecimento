Uma das tarefas mais básicas e importantes para manter um domínio seguro é configurar uma política de senha de domínio. A política de senha de domínio define os requisitos mínimos para senhas, incluindo elementos como:

- Comprimento da senha
- Requisitos de caractere especial
- Complexidade de senha
- Frequência de alteração de senha
- Memória de senha

Configurar a política de senha de domínio define o padrão e, em seguida, aplica-o a todos os usuários e computadores conectados ao domínio.

## Configurar uma política de senha de domínio

>[!info]
>Para configurar uma política de senha de domínio, você precisará de privilégios administrativos.

1. Faça logon no seu Controlador de Domínio.
2. Abra o **GPMC (Console de Gerenciamento de Política de Grupo)** (você pode inserir gpmc.msc na caixa de pesquisa para localizar o console).
3. No GPMC, navegue até seu domínio e localize a **Política de domínio padrão**.
4. Clique com o botão direito do mouse na Política de domínio padrão e selecione **Editar**.

A seleção da edição deve abrir o **Editor de Gerenciamento de Política de Grupo**. Neste editor, você pode definir a política de senha.

### Navegue até as configurações de política de senha
No Editor de Gerenciamento de Política de Grupo, vá para:

1. Configuração do Computador
2. Políticas
3. Configurações do Windows
4. Configurações de segurança
5. Políticas de conta
6. Política de senha

Uma vez na seção de política de senha, configurações específicas podem ser definidas para aplicar em todo o domínio.

### Definir as configurações de política de senha
Você verá uma lista de configurações, como Impor histórico de senha, Idade máxima da senha, comprimento mínimo da senha, etc.

Para atualizar as configurações de política de senha:

1. Clique duas vezes em uma configuração para habilitar a edição.
2. Defina a configuração.
3. Selecione **Aplicar**.
4. Selecione **OK**.

Embora as novas configurações de senha de domínio tenham sido estabelecidas, elas ainda não estão em vigor. Para aplicar as novas configurações, é necessário concluir uma atualização de política de grupo.

Em um prompt de comando com permissões de administrador, digite o seguinte comando:

**gpudate /force**

A política de senha de domínio agora está configurada e distribuída.