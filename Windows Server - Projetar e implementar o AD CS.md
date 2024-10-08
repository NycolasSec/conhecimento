É essencial garantir que você projete sua AC interna da maneira ideal. Seu design terá implicações significativas em aspectos de segurança e operacionais do seu ambiente de PKI.

## Criação da hierarquia baseada no AD CS
Não é recomendável criar uma hierarquia de AC mais profunda que três níveis, a menos que ela esteja em um ambiente complexo, altamente seguro ou distribuído. Geralmente, as hierarquias de AC têm dois níveis, com a AC raiz no nível superior e uma AC emissora subordinada no segundo nível.

Nesse caso, a AC raiz permanece offline enquanto você depender da autoridade de certificação subordinada para emitir e gerenciar certificados.

Alguns designs de AC mais complexos incluem:
- **AC de Política**: É uma AC subordinada que emite certificados para outras ACs subordinadas e reflete as políticas e procedimentos de segurança da organização. Essas ACs garantem que as políticas de identidade e gestão de certificados sejam seguidas. Elas são usadas quando diferentes setores ou divisões da organização exigem políticas de emissão distintas. Não é obrigatório utilizá-las, mas pode ser útil para separar as políticas internas e externas (ex.: funcionários e prestadores de serviço).
    
- **Relação de Confiança com Certificação Cruzada**: Duas hierarquias de AC independentes podem estabelecer confiança mútua através da emissão de certificados de certificação cruzada, permitindo que diferentes hierarquias interoperem.

![[Pasted image 20241008151854.png]]

## ACs autônomas vs. corporativas
Você pode implantar dois tipos de ACs: autônomas e corporativas. Esses tipos de ACs não tratam da hierarquia, mas, sim, da funcionalidade e da integração com o AD DS. Uma AC autônoma não depende do AD DS. Uma AC corporativa requer o AD DS para fornecer funcionalidades adicionais, como o registro automático.

#### Uso típico
Normalmente, você usa uma AC autônoma para ACs offline.

Normalmente, você usa uma AC corporativa para emitir certificados para usuários, computadores e serviços. Você não pode usá-la como uma AC offline.

#### Dependências de AD DS
Uma AC autônoma não depende do AD DS.

Uma AC corporativa depende do AD DS como seu banco de dados de registro e configuração. Uma AC corporativa também usa o AD DS para publicar certificados e seus metadados.

#### Método de solicitação de certificado
Os usuários só podem solicitar certificados de uma AC autônoma usando um procedimento manual ou um registro na Web.

Os usuários podem solicitar certificados de uma AC corporativa usando o registro manual, registro na Web, registro automático, registro em nome de uma pessoa e serviços Web

#### Métodos da emissão de certificados
Um administrador de AC deve aprovar todas as solicitações manualmente.

A AC pode emitir certificados ou negar a emissão de certificados automaticamente com base em uma configuração personalizada definida pelo administrador da AC.

#### Algumas considerações

- **AC Raiz Corporativa**: É a escolha mais comum para implantações em ambientes de AD DS com uma única AC. Em uma hierarquia de duas camadas, pode-se usar uma **AC raiz autônoma**, que pode ser deixada offline sem interromper a gestão de certificados.
    
- **Tipo de Instalação do Sistema Operacional**: O AD CS é compatível tanto com a **Experiência Desktop** quanto com o **Server Core**. O **Server Core** é preferido em ambientes corporativos, pois reduz a superfície de ataque e a manutenção do sistema.
    
- **Configurações Imutáveis**: Após a implantação de uma AC, não é possível alterar o nome do computador, nome de domínio ou associação de domínio. Portanto, essas configurações devem ser definidas antes da instalação.

Também há algumas considerações específicas a serem feitas para a implantação de uma AC raiz autônoma e offline:
- **Local do CDP e AIA**: Antes de emitir um certificado subordinado, é crucial definir um local acessível para o **CDP** (ponto de distribuição da lista de revogação de certificados) e para o **AIA** (Acesso a Informações de Autoridade). Isso se deve ao fato de que, por padrão, uma AC raiz autônoma tem esses locais nela mesma. Se a AC raiz for deixada offline, as verificações de revogação falharão. As informações da **CRL** (lista de revogação de certificados) e do AIA devem ser copiadas manualmente para o local definido.
    
- **Validade da CRL**: É recomendado definir um período de validade longo para as CRLs publicadas pela AC raiz, como um ano. Isso significa que a AC raiz precisará ser habilitada anualmente para publicar uma nova CRL, que também deve ser copiada para um local acessível. Se a CRL expirar e não for atualizada, as verificações de revogação de todos os certificados falharão.
    
- **Publicação do Certificado da AC Raiz**: Utilize a **Política de Grupo** para publicar o certificado da AC raiz em um repositório confiável em todos os computadores clientes e servidores. Isso deve ser feito manualmente, pois a AC autônoma não suporta a publicação automática, ao contrário da AC corporativa. Alternativamente, o certificado pode ser publicado no **Active Directory Domain Services (AD DS)** utilizando a ferramenta de linha de comando **certutil**.
































