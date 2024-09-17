Delegar permissões no Active Directory permite atribuir tarefas administrativas específicas a usuários ou grupos sem conceder direitos administrativos completos, suportando o princípio de privilégios mínimos.

Em vez de adicionar usuários a grupos privilegiados, você pode permitir que eles realizem funções específicas, como gerenciar contas de usuário. Isso reduz o risco de uso indevido e melhora a eficiência.

Para delegar permissões de redefinição de senha:
1. Abra **Usuários e Computadores do Active Directory (ADUC)**.
2. Clique com o botão direito na UO desejada e selecione **Delegar Controle**.
3. No **Assistente de Delegação de Controle**, clique em **Avançar**.
4. Clique em **Adicionar** e selecione o usuário ou grupo.
5. Clique em **Avançar**.
6. Selecione **Redefinir senhas de usuário e forçar a alteração de senha no próximo logon**.
7. Clique em **Avançar**, revise e clique em **Concluir**.

---

Essas etapas permitem que usuários designados ajudem a administrar senhas, mantendo o controle sobre quem pode realizar essa tarefa.