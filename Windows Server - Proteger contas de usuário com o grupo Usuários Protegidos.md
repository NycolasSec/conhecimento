Os Usuários Protegidos do grupo de segurança do AD DS (Active Directory Domain Services) ajudam a proteger contas de usuário altamente privilegiadas contra o comprometimento. Os membros do grupo Usuários Protegidos têm várias definições de configuração relacionadas à segurança aplicadas que não podem ser modificadas, exceto quando o membro sai do grupo.

## Pré-requisitos do grupo Usuários Protegidos

Para dar proteção aos membros do grupo de Usuários Protegidos:

- O grupo precisa ser replicado para todos os controladores de domínio.
- O usuário precisa entrar em um dispositivo que executa o Windows 8.1 ou o Windows Server 2012 R2 ou posterior.
- A proteção do controlador de domínio requer que os domínios estejam em execução em um nível funcional de domínio do Windows Server 2012 R2 ou superior. Os níveis funcionais inferiores ainda dão suporte à proteção em dispositivos cliente.

## Proteções do grupo Usuários Protegidos

Quando um usuário é membro do grupo Usuários Protegidos, na estação de trabalho ou no dispositivo local:

- As credenciais do usuário não são armazenadas em cache local.
- A delegação de credenciais (CredSSP) não armazenará as credenciais de usuário em cache.
- O Digest do Windows não armazenará as credenciais de usuário em cache.
- O NTLM não armazenará em cache as credenciais do usuário.
- O Kerberos não criará chaves DES (padrão de criptografia de dados) ou RC4 nem credenciais de cache ou chaves de longo prazo.
- O usuário não pode mais entrar offline.

Nos controladores de domínio que executam o Windows Server 2012 R2 ou posterior:

- A autenticação NTLM não é permitida.
- Não é possível usar a criptografia DES e RC4 na pré-autenticação Kerberos.
- As credenciais não podem ser delegadas usando a delegação restrita.
- Elas não podem ser delegadas usando a delegação restrita.
- Os TGTs (tíquetes de concessão de tíquete) não podem ser renovados após o tempo de vida inicial.

Para adicionar usuários a grupos privilegiados e ao grupo Usuários Protegidos usando o ADAC (Centro Administrativo do Active Directory), siga estas etapas:

1. Abra o **Centro Administrativo do Active Directory (ADAC)**.
2. No painel de navegação esquerdo, expanda seu domínio e navegue até o contêiner Usuários ou a UO (Unidade Organizacional) específica em que o grupo privilegiado ou o grupo Usuários Protegidos está localizado.
3. No painel principal, localize e selecione o grupo ao qual você deseja adicionar usuários.
4. No painel Tarefas à direita, clique em **Propriedades**.
5. Na janela de propriedades do grupo, navegue até a guia **Membros**.
6. Clique em **Adicionar** para abrir a caixa de diálogo **Selecionar Usuários, Contatos, Computadores, Contas de Serviço ou Grupos**.
7. Pesquise as contas de usuário que você deseja adicionar ao grupo, selecione-as e clique em **OK**.
8. Confirme as adições clicando em **OK** e, em seguida, **OK** novamente na janela de propriedades do grupo.

## Políticas de autenticação
As políticas de autenticação permitem configurar o tempo de vida do TGT e as condições de controle de acesso de um usuário, serviço ou conta de computador. Para contas de usuário, você pode configurar o tempo de vida do TGT do usuário, até o tempo de vida máximo de 600 minutos do grupo Usuários Protegidos. Você também pode restringir em quais dispositivos o usuário pode entrar e os critérios que os dispositivos precisam atender.

### Silos de política de autenticação
Os **silos de política de autenticação** permitem que os administradores apliquem políticas específicas a contas de usuário, computador e serviço. Eles trabalham junto com o grupo **Usuários Protegidos**, adicionando restrições configuráveis às já existentes.

Um silo de política de autenticação assegura que as contas estejam vinculadas a um único silo, controlando o acesso a recursos críticos. Quando uma conta associada a um silo entra no sistema, ela recebe uma declaração de autenticação que determina se tem autorização para acessar recursos específicos, como servidores confidenciais.