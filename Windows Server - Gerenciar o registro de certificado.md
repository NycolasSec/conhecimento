## O que é um certificado?
- **Definição de Certificado**: Um certificado é um arquivo pequeno que contém informações sobre seu proprietário, como nome, endereço de e-mail, tipo de uso, período de validade e URLs para os locais de **AIA** (Acesso a Informações de Autoridade) e **CDP** (ponto de distribuição da lista de revogação de certificados).

- **Conteúdo do Certificado**: Inclui a **chave pública** do proprietário e metadados associados, que incluem a **chave privada**. Essas chaves são usadas em processos de validação de identidades, assinaturas digitais e criptografia.

- **Relação entre Chaves**:
  - Quando algo é criptografado com a chave pública, só pode ser descriptografado com a chave privada.
  - Quando algo é criptografado com a chave privada, só pode ser descriptografado com a chave pública.
  - Não há outra chave envolvida na relação entre as chaves de um par.
  - A chave privada não pode ser obtida a partir da chave pública e vice-versa.

- **Processo de Registro de Certificado**: O cliente gera um par de chaves (pública e privada) e envia a chave pública à **Autoridade de Certificação (AC)**. A AC verifica as informações do cliente, assina a chave pública com sua própria chave privada e envia o certificado, que inclui a chave pública do cliente, de volta a ele.

Esse resumo cobre os principais aspectos do funcionamento e da estrutura dos certificados digitais, além do processo de sua emissão.

![[Pasted image 20241008160315.png]]

## O que são modelos de certificado?
Os modelos de certificado definem como usuários e dispositivos podem solicitar e usar certificados de AC corporativa emitidos com base nesse modelo. Por exemplo, você pode criar um modelo que fornecerá uma criptografia de arquivo ou uma funcionalidade de assinatura de email.

>[!info] Os modelos de certificado só estão disponíveis ao usar a AC corporativa. Isso significa que, com uma AC autônoma, cada solicitação de certificado deve ser criada manualmente e deve incluir todas as informações necessárias a serem incluídas no certificado.

A AC fornece modelos para usuários e computadores. Você pode atribuir permissões a modelos de certificado para definir quem pode gerenciá-los, quem pode executar registros ou registros automáticos e quais são seus períodos de validade e renovação. Para disponibilizar modelos para usuários e dispositivos, você deve permitir seu uso explicitamente.

### Versões de modelo
- **Modelos da Versão 1**: Permitem apenas a modificação de permissões relacionadas ao certificado. Esses modelos são criados por padrão ao instalar uma AC.
    
- **Modelos da Versão 2**: Permitem a personalização de configurações adicionais, como períodos de validade e renovação. Essa é a versão mínima que suporta o registro automático. A instalação padrão do AD CS já fornece vários modelos da versão 2 pré-configurados, e é possível criar novos modelos ou duplicar os da versão 1 para criar um modelo da versão 2.
    
- **Modelos da Versão 3**: Suportam a **CNG** (Cryptography Next Generation), que permite o uso de algoritmos de criptografia avançados. Modelos da versão 1 e 2 podem ser duplicados para atualizá-los para a versão 3. Esses modelos permitem o uso de criptografia CNG e algoritmos de hash em solicitações de certificados, nos próprios certificados emitidos e na proteção de chaves privadas.
    
- **Modelos da Versão 4**: Suportam **CSPs** (provedores de serviços de criptografia) e provedores de armazenamento de chaves. É possível configurá-los para exigir renovação com a mesma chave.









































