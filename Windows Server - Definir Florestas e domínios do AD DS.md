Uma floresta do AD DS é uma coleção de uma ou mais árvores do AD DS que contêm um ou mais domínios do AD DS.

#### Domínios em um compartilhamento de floresta:

- Uma raiz comum.
- Um esquema comum.
- Um catálogo global.

#### Um domínio do AD DS é um contêiner administrativo lógico para objetos como:

- Usuários
- Grupos
- Computadores

## O que é uma floresta do AD DS?
Uma **floresta** no Active Directory Domain Services (AD DS) é um contêiner de nível superior que inclui uma ou mais árvores de domínio. Todas as árvores em uma floresta compartilham um esquema de diretório comum e um catálogo global.

Uma árvore de domínio, por sua vez, é composta por domínios que compartilham um namespace contíguo. O **domínio raiz da floresta** é o primeiro domínio criado e contém objetos exclusivos a ele. Uma floresta pode ter um único domínio e controlador ou vários domínios e controladores distribuídos em várias árvores.

O gráfico a seguir exibe `Contoso.com` como o domínio raiz da floresta. Abaixo estão dois domínios, `Adatum.com` em uma árvore separada e `Seattle.Contoso.com` como um filho de `Contoso.com`.

![[Pasted image 20240905173329.png]]

#### Os seguintes objetos existem no domínio raiz da floresta:

- A função de mestre de esquema.
- A função de mestre de nomeação de domínio.
- O grupo Administradores de Empresa.
- O grupo Administradores de Esquema.

#### Uma floresta do AD DS geralmente é descrita como:

- **Limite de Segurança**: Nenhum usuário de fora da floresta pode acessar recursos dentro dela, e todos os domínios dentro da floresta confiam automaticamente uns nos outros, facilitando o acesso a recursos para todos os usuários da floresta.

- **Limite de Replicação**: A floresta define os limites de replicação para configurações e esquemas no banco de dados do AD DS. Para implantar aplicativos com esquemas incompatíveis, são necessárias florestas adicionais. A floresta também é o limite de replicação para o catálogo global, permitindo a busca de objetos em qualquer domínio dentro da floresta.

#### Os seguintes objetos existem em cada domínio (incluindo a raiz da floresta):

- A função de mestre de RID.
- A função de mestre de infraestrutura.
- A função de mestre emulador PDC.
- O grupo Administradores de Domínio.

## O que é um domínio do AD DS?
Um **domínio do AD DS** é um contêiner lógico que gerencia usuários, computadores, grupos e outros objetos. O banco de dados do AD DS armazena todos os objetos de um domínio, e cada controlador de domínio mantém uma cópia desse banco de dados.

O gráfico a seguir exibe um domínio do AD DS. Ele contém usuários, computadores e grupos.
![[Pasted image 20240905173840.png]]

#### Os objetos usados com mais frequência são :

- **Contas de usuário**: Contêm informações do usuário necessárias para autenticação e criação do token de acesso.

- **Contas de computador**: Cada computador no domínio tem uma conta no AD DS, usada da mesma forma que contas de usuário.

- **Grupos**: Organizam usuários ou computadores para facilitar o gerenciamento de permissões e Políticas de Grupo no domínio.

#### Um domínio do AD DS geralmente é descrito como:

- **Limite de replicação**: As alterações feitas em um objeto em um controlador de domínio são replicadas para todos os outros controladores no mesmo domínio. Apenas subconjuntos de alterações são replicados para outros domínios na floresta. O modelo de replicação é de múltiplos mestres, permitindo que qualquer controlador faça alterações.

- **Unidade administrativa**: O domínio possui uma conta de administrador e um grupo Administradores de Domínio, que tem controle total sobre todos os objetos do domínio. Membros desse grupo têm também permissões administrativas nos computadores conectados ao domínio.

#### Um domínio do AD DS fornece

- **Autenticação**: O AD DS autentica computadores e usuários ao iniciar ou fazer login, verificando suas credenciais para garantir que eles tenham a identidade adequada no diretório.

- **Autorização**: Após a autenticação, o Windows utiliza tecnologias de controle de acesso para determinar se os usuários têm permissão para acessar recursos específicos.

## O que são relações de confiança?
As relações de confiança no AD DS permitem o acesso a recursos em ambientes com múltiplos domínios ou florestas.

Em uma floresta do AD DS com vários domínios, são criadas automaticamente relações de confiança transitivas bidirecionais entre todos os domínios. Isso garante que exista um caminho de confiança entre todos os domínios, facilitando o acesso a recursos em toda a floresta.

>[!NOTE] Todas as relações de confiança criadas automaticamente na floresta são transitivas, o que significa que, se o domínio A confiar no domínio B e o domínio B confiar no domínio C, o domínio A confiará no domínio C.

Você pode implantar outros tipos de relações de confiança. A tabela a seguir descreve os principais tipos de relações de confiança.


#### Tipo de relação de confiança

- **Pai e filho** - Transitiva e bidirecional. Ao adicionar um novo domínio do AD DS a uma árvore do AD DS existente, você cria relações de confiança pai e filho.
- **Árvore e raiz** - Transitiva e bidirecional. Ao criar uma árvore do AD DS em uma floresta do AD DS existente, você cria automaticamente uma relação de confiança entre raiz e árvore.
- **Externo** - Intransitiva e unidirecional ou bidirecional. As relações de confiança externas permitem o acesso a recursos com um domínio do Windows NT 4.0 ou um domínio do AD DS em outra floresta. Você também pode configurá-los para fornecer uma estrutura para uma migração.
- **Realm** - Transitiva ou intransitiva e unidirecional ou bidirecional. As relações de confiança de realm estabelecem um caminho de autenticação entre um domínio do Windows Server AD DS e um realm do protocolo Kerberos versão 5 (V5) que é implementado usando um serviço de diretório diferente do AD DS.
- **Floresta (completa ou seletiva)** - Transitiva e unidirecional ou bidirecional. As relações de confiança entre florestas do AD DS permitem que duas florestas compartilhem recursos.
- **Atalho** - Intransitiva e unidirecional ou bidirecional - As relações de confiança de atalho no AD DS melhoram a eficiência da autenticação entre domínios distantes na mesma floresta, reduzindo o tempo necessário para autenticar usuários e acessar recursos. Essas relações não são criadas por padrão e precisam ser configuradas manualmente por um administrador.

Quando você configura relações de confiança entre domínios dentro da mesma floresta, entre florestas ou com um realm externo, o Windows Server cria um objeto de domínio confiável para armazenar as informações de relações de confiança, como transitividade e tipo, no AD DS. O Windows Server armazena esse objeto de domínio confiável no contêiner do sistema no AD DS.




































































