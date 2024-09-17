O protocolo de autenticação NTLM é um protocolo herdado que era o padrão para o Windows NT 4.0. Desde o Windows Server 2000, o padrão tem sido o protocolo de autenticação Kerberos. O Kerberos fornece alguns aprimoramentos significativos em relação à autenticação NTLM.

Algumas diferenças significativas entre os dois protocolos incluem:

- Sem autenticação mútua: O Kerberos permite autenticação mútua, o que significa que o cliente e o servidor podem verificar a identidade uns dos outros. O NTLM não permite a verificação do cliente da identidade do servidor.
- Vulnerabilidade para ataques de reprodução: O Kerberos inclui recursos como o autenticador para evitar ataques de reprodução, aos quais o NTLM é suscetível.
- Dependência da confiança: Em um ambiente de vários domínios, o Kerberos não exige que todos os domínios confiem uns nos outros, usando relações de confiança existentes para encontrar um caminho para o destino. O modelo de confiança do NTLM é menos flexível.
- Delegação de credenciais: O Kerberos introduziu a delegação de credenciais, permitindo um acesso mais seguro e eficiente aos recursos. O NTLM não tem esse recurso.

Esses pontos destacam os avanços na segurança e eficiência que o Kerberos oferece sobre o NTLM. Como o protocolo de autenticação NTLM é menos seguro do que o protocolo de autenticação Kerberos, você deve bloquear o uso do NTLM para autenticação e usar o Kerberos.

## Auditar o tráfego NTLM

Antes de bloquear o NTLM, você precisa garantir que os aplicativos existentes não usem mais o protocolo. Você pode auditar o tráfego NTLM definindo as seguintes configurações de Política de Grupo em Configuração do Computador\Configurações do Windows\Configurações de Segurança\Políticas Locais\Opções de Segurança:

- **Segurança de rede: restringir o NTLM: tráfego NTLM de saída para servidores remotos**. Defina essa política com a configuração Auditar tudo.
- **Segurança de rede: restringir o NTLM: auditar o tráfego NTLM de entrada**. Defina essa política com a configuração Habilitar auditoria para todas as contas.
- **Segurança de rede: Restringir NTLM: Audite a autenticação NTLM neste domínio**. Defina essa política com a configuração Habilitar para contas de domínio para servidores de domínio em controladores de domínio. Você não deve configurar essa política em todos os computadores.

## Bloquear o NTLM

Depois de determinar que é possível bloquear o NTLM na organização, você precisará configurar a política Restringir NTLM: autenticação NTLM neste domínio no nó Política de Grupo anterior. As opções de configuração são:

- **Negar contas de domínio para servidores de domínio**. Essa opção nega todas as tentativas de entrada com a autenticação NTLM de todos os servidores no domínio que usam contas de domínio, a menos que o nome do servidor esteja listado na configuração **Segurança de rede: restringir o NTLM: adicionar exceções de servidor à autenticação NTLM** nesta política de domínio.
- **Negar para contas de domínio**. Essa opção nega todas as tentativas de autenticação NTLM de contas de domínio, a menos que o nome do servidor esteja listado na configuração **Segurança de rede: restringir o NTLM: adicionar exceções de servidor para autenticação NTLM** nesta política de domínio.
- **Negar para servidores de domínio**. Essa opção nega as solicitações de autenticação NTLM a todos os servidores no domínio, a menos que o nome do servidor esteja listado na configuração **Segurança de rede: restringir o NTLM: adicionar exceções de servidor para autenticação NTLM** nesta política de domínio.
- **Negar tudo**. Essa opção garante que todas as solicitações de autenticação de passagem NTLM de servidores e contas sejam negadas, a menos que o nome do servidor esteja listado na configuração **Segurança de rede: restringir o NTLM: adicionar exceções de servidor para autenticação NTLM** nesta política de domínio.

Para desabilitar o NTLM em controladores de domínio usando o Console de Gerenciamento de Política de Grupo, siga estas etapas:

1. Abra o **GPMC (Console de Gerenciamento de Política de Grupo)**.
2. Crie um novo **GPO** e dê a ele um nome descritivo.
3. Edite o GPO e navegue até Configuração do Computador\Políticas\Configurações do Windows\Configurações de Segurança\Políticas Locais\Opções de Segurança.
4. Localize a política **Segurança de rede: Restringir NTLM: Autenticação NTLM neste domínio** e defina-a como **Negar todos**.
5. Localize a política **Segurança de rede: Restringir NTLM: Adicione exceções de servidor neste domínio** e configure-o com as exceções necessárias, se for preciso.
6. Vincule o GPO à UO (Unidade Organizacional) dos controladores de domínio.
7. Verifique se a política é imposta executando o comando **gpupdate /force** nos controladores de domínio ou aguardando o próximo ciclo de atualização da Política de Grupo.