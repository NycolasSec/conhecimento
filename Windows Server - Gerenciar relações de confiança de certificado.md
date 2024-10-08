Para que um certificado seja efetivo, todos os usuários, dispositivos ou aplicativos que fazem uso dele devem confiar na AC que o emitiu.

## O que é uma relação de confiança de certificado?
Ao usar certificados, é importante levar em consideração quem ou o que talvez precise avaliar sua autenticidade e validade. Há três tipos de certificados que você pode usar:

- Certificados internos de uma AC organizacional, como um servidor que hospeda a função AD CS.
- Certificados externos de uma AC pública, como uma organização que fornece serviços de software ou identidade de segurança cibernética comercial.
- Um certificado autoassinado.

Se você implantar uma AC raiz corporativa e usá-la para registrar certificados nos dispositivos conectados ao domínio de seus usuários, esses dispositivos aceitarão os certificados registrados como confiáveis. No entanto, qualquer dispositivo de grupo de trabalho considerará esses mesmos certificados como não confiáveis.

## Gerenciar certificados e relações de confiança de certificado no Windows
Você pode gerenciar certificados no Windows usando várias ferramentas, incluindo:

1. **Windows Admin Center**
2. **Snap-in de Certificados do Console de Gerenciamento Microsoft**
3. **Windows PowerShell**
4. **Certutil** (linha de comando)

Essas ferramentas permitem acessar e gerenciar os repositórios de certificados do usuário, do computador local e de seus serviços. Cada repositório é composto por várias pastas, incluindo:

1. **Pessoal**: Contém certificados emitidos para o usuário local, computador local ou seus serviços, dependendo do repositório selecionado.
    
2. **Autoridades de Certificação Confiáveis**: Abriga certificados de **ACs raiz** confiáveis.
    
3. **Confiabilidade Empresarial**: Armazena listas de certificados confiáveis que implementam relações de confiança com certificados autoassinados de outras organizações.
    
4. **Autoridades de Certificação Intermediárias**: Contém certificados emitidos para **ACs subordinadas** dentro da hierarquia de certificação.
    

Para garantir que os dispositivos de um grupo de trabalho confiem na AC raiz corporativa, é necessário exportar o certificado da pasta **Autoridades de Certificação Raiz Confiáveis** em um computador conectado ao domínio e importá-lo para a mesma pasta nos dispositivos do grupo de trabalho.

## Criar um certificado autoassinado para fins de teste
Certificados autoassinados não são ideais para produção, mas são úteis para testes. Você pode criar um certificado autoassinado usando o cmdlet **`New-SelfSignedCertificate`** no Windows PowerShell. Se incluir o parâmetro **`CloneCert`** e fornecer um certificado existente, o novo terá configurações semelhantes, exceto pela chave pública, que será gerada com o mesmo algoritmo e comprimento.

Um exemplo de uso do cmdlet cria um certificado SSL autoassinado no repositório pessoal do computador local, definindo o nome alternativo da entidade como **[www.fabrikam.com](http://www.fabrikam.com)** e **[www.contoso.com](http://www.contoso.com)**, e o nome da Entidade e do Emissor como **[www.fabrikam.com](http://www.fabrikam.com)**.

```powershell
New-SelfSignedCertificate -DnsName "www.fabrikam.com", "www.contoso.com" -CertStoreLocation "cert:\LocalMachine\My"
```





























