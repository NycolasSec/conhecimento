No Active Directory Domain Services (AD DS), você pode criar e gerenciar Objetos de Política de Grupo (GPOs) para controlar configurações em seu domínio. Após a instalação do AD DS, o Windows Server cria automaticamente dois GPOs padrão:

### Política de Domínio Padrão

- **Vinculação**: Este GPO é vinculado ao nível do domínio e aplica-se a todos os **Usuários Autenticados**.
- **Escopo**: Afeta todos os usuários e computadores dentro do domínio.
- **Conteúdo**: Inclui configurações importantes como políticas de senha, bloqueio de conta e o protocolo de autenticação Kerberos versão 5.
- **Observação**: É crucial para o ambiente AD DS e deve manter apenas configurações básicas relacionadas a segurança e autenticação. Configurações adicionais, que não se relacionam diretamente a essas políticas básicas, devem ser implementadas em GPOs separados para evitar sobrecarga e complexidade desnecessária.

### Política de Controladores de Domínio Padrão

- **Vinculação**: Este GPO é vinculado especificamente à Unidade Organizacional (UO) dos controladores de domínio.
- **Escopo**: Afeta exclusivamente os controladores de domínio e outros objetos de computador que estão na UO Controladores de Domínio.
- **Conteúdo**: Deve ser usado para definir políticas relacionadas à auditoria e direitos dos usuários necessários para o funcionamento e a segurança dos controladores de domínio.
- **Observação**: É importante ajustar as configurações deste GPO para atender às necessidades específicas de gerenciamento e segurança dos controladores de domínio, garantindo que eles tenham as políticas apropriadas aplicadas.