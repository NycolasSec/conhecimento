Uma floresta do Active Directory Domain Services (AD DS) é uma coleção de uma ou mais árvores do AD DS que contêm um ou mais domínios do AD DS. Domínios em uma floresta compartilham:

- Uma raiz comum.
- Um esquema comum.
- Um catálogo global.

Um domínio do AD DS é um contêiner administrativo lógico para objetos como:

- Usuários
- Grupos
- Computadores

O diagrama a seguir descreve o relacionamento entre florestas, domínios e árvores de domínio.

![[Pasted image 20240917144035.png]]

## O que é uma floresta do AD DS?

Uma **floresta** é o contêiner de nível mais alto no AD DS, contendo uma ou mais árvores de domínio que compartilham um esquema de diretório e um catálogo global. Uma **árvore de domínio** é composta por um ou mais domínios que compartilham um namespace contíguo, e o **domínio raiz** é o primeiro domínio criado na floresta.

O domínio raiz contém objetos exclusivos que não existem em outros domínios. Uma floresta pode ser composta por um único domínio com um controlador de domínio ou por vários domínios distribuídos em várias árvores de domínio.

O gráfico a seguir exibe **Contoso.com** como o domínio raiz da floresta. Abaixo estão dois domínios, Adatum.com em uma árvore separada e Seattle.Contoso.com como um filho de **Contoso.com**.

![[Pasted image 20240917144129.png]]

Os seguintes objetos existem no domínio raiz da floresta:
- A função de mestre de esquema.
- A função mestre de nomenclatura de domínio.
- O grupo de administradores corporativos.
- O grupo Schema Admins.

Uma **floresta do AD DS** é descrita como:
- Um **limite de segurança**: Usuários externos à floresta não têm acesso a seus recursos por padrão, e todos os domínios na floresta confiam automaticamente uns nos outros, facilitando o acesso aos recursos para todos os usuários da floresta.
- Um **limite de replicação**: A floresta define os limites de replicação para as partições de configuração e esquema do AD DS. Se uma organização precisar de esquemas incompatíveis, será necessário criar florestas adicionais. A floresta também define o limite de replicação para o **catálogo global**, que permite a busca de objetos em qualquer domínio da floresta.

Os seguintes objetos existem em cada domínio (incluindo a raiz da floresta):
- Função mestre de ID relativo (RID).
- Função de mestre de infraestrutura.
- Função mestre do emulador do controlador de domínio primário (PDC).
- Grupo de administradores de domínio.

## O que é um domínio do AD DS?
Um domínio do AD DS é um contêiner lógico para gerenciar usuários, computadores, grupos e outros objetos. O banco de dados do AD DS armazena todos os objetos de domínio, e cada controlador de domínio armazena uma cópia do banco de dados.

O gráfico a seguir exibe um domínio do AD DS. Ele contém usuários, computadores e grupos.

![[Pasted image 20240917144327.png]]

Os objetos mais comumente utilizados são descritos na tabela a seguir:

|**Objeto**|**Descrição**|
|---|---|
|Contas de usuário|As contas de usuário contêm informações sobre os usuários, incluindo as informações necessárias para autenticar um usuário durante o processo de login e criar o token de acesso do usuário.|
|Contas de computador|Cada computador ingressado no domínio tem uma conta no AD DS. Você pode usar contas de computador para computadores ingressados ​​no domínio da mesma forma que usa contas de usuário para usuários.|
|Grupos|Grupos organizam usuários ou computadores para simplificar o gerenciamento de permissões e Objetos de Política de Grupo no domínio.|

Um **domínio do AD DS** é descrito como:

- Um **limite de replicação**: Alterações feitas em qualquer objeto dentro de um domínio são replicadas para todos os controladores de domínio desse domínio. Em florestas com vários domínios, apenas subconjuntos das alterações são replicados entre domínios. O AD DS utiliza um modelo de replicação de vários mestres, permitindo que cada controlador de domínio faça alterações.
- Uma **unidade administrativa**: O domínio contém uma conta de administrador e um grupo de administradores de domínio que, por padrão, têm controle total sobre todos os objetos no domínio. Esses administradores também fazem parte de cada grupo de administradores locais dos computadores do domínio.

>[!info] A conta de administrador no domínio raiz da floresta tem direitos adicionais.

Um domínio do AD DS fornece:

- Autenticação. Sempre que um computador ingressado em um domínio é iniciado ou um usuário faz logon em um computador ingressado em um domínio, o AD DS o autentica. A autenticação verifica se o computador ou usuário tem a identidade adequada no AD DS verificando suas credenciais.
- Autorização. O Windows usa tecnologias de autorização e controle de acesso para determinar se deve permitir que usuários autenticados acessem recursos.

## O que são relações de confiança?

Os **trusts do AD DS** permitem acesso a recursos em ambientes complexos. Em um único domínio, usuários e grupos podem acessar recursos facilmente. No entanto, em ambientes com múltiplos domínios ou florestas, é necessário configurar os trusts adequados para garantir o mesmo nível de acesso.

Em uma floresta do AD DS com vários domínios, **relações de confiança transitivas bidirecionais** são geradas automaticamente, criando um caminho de confiança entre todos os domínios.

Você pode implementar outros tipos de trusts. A tabela a seguir descreve os principais tipos de trust.

| **Tipo de confiança**           | **Descrição**                | **Direção**                   | **Descrição**                                                                                                                                                                                                                                                   |
| ------------------------------- | ---------------------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pai e filho                     | Transitivo                   | Bidirecional                  | Ao adicionar um novo domínio do AD DS a uma árvore do AD DS existente, você cria novas relações de confiança pai e filho.                                                                                                                                       |
| Raiz de árvore                  | Transitivo                   | Bidirecional                  | Ao criar uma nova árvore do AD DS em uma floresta do AD DS existente, você cria automaticamente uma nova relação de confiança raiz de árvore.                                                                                                                   |
| Externo                         | Não transitivo               | Unidirecional ou bidirecional | Trusts externos permitem acesso a recursos com um domínio do Windows NT 4.0 ou um domínio do AD DS em outra floresta. Você também pode configurá-los para fornecer uma estrutura para uma migração.                                                             |
| Reino                           | Transitivo ou não transitivo | Unidirecional ou bidirecional | Os trusts de domínio estabelecem um caminho de autenticação entre um domínio AD DS do Windows Server e um domínio do protocolo Kerberos versão 5 (v5) que é implementado usando um serviço de diretório diferente do AD DS.                                     |
| Floresta (completa ou seletiva) | Transitivo                   | Unidirecional ou bidirecional | Relações de confiança entre florestas do AD DS permitem que duas florestas compartilhem recursos.                                                                                                                                                               |
| Atalho                          | Não transitivo               | Unidirecional ou bidirecional | Configure atalhos de confiança para reduzir o tempo gasto para autenticar entre domínios do AD DS que estão em diferentes partes de uma floresta do AD DS. Nenhum atalho de confiança existe por padrão, e um administrador deve criá-los se forem necessários. |





