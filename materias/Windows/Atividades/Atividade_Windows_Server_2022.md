
# Atividade Prática: Gerenciamento de Usuários e Grupos no Windows Server 2022

## **Objetivo:**
Configurar usuários e grupos no Windows Server 2022, incluindo criação, modificação e atribuição de permissões básicas.

---

## **Passos:**

### 1. **Criar um novo grupo de segurança:**

- Abra o **Gerenciador do Servidor**.
- Vá para **Ferramentas** e selecione **Usuários e Computadores do Active Directory**.
- Navegue até a unidade organizacional (OU) onde deseja criar o grupo (ou crie uma nova OU, se necessário).
- Clique com o botão direito do mouse na OU, selecione **Novo** e, em seguida, **Grupo**.
- Nomeie o grupo como **"Grupo_Projeto_X"** e selecione **Global** como o escopo do grupo e **Segurança** como tipo de grupo.
- Clique em **OK** para criar o grupo.

### 2. **Criar novos usuários:**

- No mesmo console **Usuários e Computadores do Active Directory**, clique com o botão direito na OU onde deseja criar os usuários.
- Selecione **Novo** e depois **Usuário**.
- Crie dois usuários: **"Usuario01"** e **"Usuario02"**.
- Preencha os campos obrigatórios (Nome, Nome de Login), configure uma senha e desmarque a opção "O usuário deve alterar a senha no próximo logon".
- Finalize o processo para ambos os usuários.

### 3. **Adicionar os usuários ao grupo:**

- Selecione o grupo **"Grupo_Projeto_X"** que você criou.
- Clique com o botão direito do mouse no grupo e escolha **Propriedades**.
- Vá até a aba **Membros** e clique em **Adicionar**.
- Pesquise por **"Usuario01"** e **"Usuario02"** e adicione-os ao grupo.

### 4. **Configurar permissões básicas para o grupo:**

- Crie uma pasta em **C:\\** chamada **"Projeto_X"**.
- Clique com o botão direito na pasta, selecione **Propriedades**, e vá até a aba **Segurança**.
- Clique em **Editar** e depois em **Adicionar**.
- Digite **"Grupo_Projeto_X"** e clique em **OK**.
- Defina as permissões de **Controle Total** para o grupo **"Grupo_Projeto_X"** e aplique as alterações.

### 5. **Verificar as configurações:**

- Faça login no sistema usando as credenciais de **"Usuario01"** ou **"Usuario02"** e verifique se eles têm acesso à pasta **"Projeto_X"** com as permissões configuradas.

---

## **Resultados Esperados:**

- **Grupo de Segurança:** Deve ser criado corretamente na OU selecionada.
- **Usuários:** Devem ser adicionados ao grupo de segurança sem problemas.
- **Permissões:** Os usuários do grupo devem ter acesso à pasta **"Projeto_X"** com as permissões definidas.
