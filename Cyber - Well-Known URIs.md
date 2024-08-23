O `.well-known`padrão, definido no [RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615) , serve como um diretório padronizado dentro do domínio raiz de um site. Este local designado, normalmente acessível pelo `/.well-known/`caminho em um servidor web, centraliza os metadados críticos de um site, incluindo arquivos de configuração e informações relacionadas a seus serviços, protocolos e mecanismos de segurança.

Ao estabelecer um local consistente para tais dados, `.well-known`simplifica o processo de descoberta e acesso para várias partes interessadas, incluindo navegadores da web, aplicativos e ferramentas de segurança.

Essa abordagem simplificada permite que os clientes localizem e recuperem automaticamente arquivos de configuração específicos construindo a URL apropriada. Por exemplo, para acessar a política de segurança de um site, um cliente solicitaria `https://example.com/.well-known/security.txt`.

O `Internet Assigned Numbers Authority`( `IANA`) mantém um [registro](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) de `.well-known` URIs, cada um servindo a um propósito específico definido por várias especificações e padrões. Abaixo está uma tabela destacando alguns exemplos notáveis:

| Sufixo URI                     | Descrição                                                                                                     | Status     | Referência                                                                              |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------- | ---------- | --------------------------------------------------------------------------------------- |
| `security.txt`                 | Contém informações de contato para pesquisadores de segurança relatarem vulnerabilidades.                     | Permanente | RFC 9116                                                                                |
| `/.well-known/change-password` | Fornece um URL padrão para direcionar os usuários a uma página de alteração de senha.                         | Provisório | https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri |
| `openid-configuration`         | Define detalhes de configuração para o OpenID Connect, uma camada de identidade sobre o protocolo OAuth 2.0.  | Permanente | http://openid.net/specs/openid-connect-discovery-1_0.html                               |
| `assetlinks.json`              | Usado para verificar a propriedade de ativos digitais (por exemplo, aplicativos) associados a um domínio.     | Permanente | https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md     |
| `mta-sts.txt`                  | Especifica a política para SMTP MTA Strict Transport Security (MTA-STS) para aprimorar a segurança de e-mail. | Permanente | RFC 8461                                                                                |

Esta é apenas uma pequena amostra dos muitos `.well-known`URIs registrados na IANA. Cada entrada no registro oferece diretrizes e requisitos específicos para implementação, garantindo uma abordagem padronizada para alavancar o `.well-known`mecanismo para vários aplicativos.

## Web Recon e Well-Known URIs

No web recon, os `.well-known`URIs podem ser inestimáveis ​​para descobrir endpoints e detalhes de configuração que podem ser testados posteriormente durante um teste de penetração. Um URI particularmente útil é `openid-configuration`.

O `openid-configuration`URI faz parte do protocolo OpenID Connect Discovery, uma camada de identidade construída sobre o protocolo OAuth 2.0. Quando um aplicativo cliente deseja usar o OpenID Connect para autenticação, ele pode recuperar a configuração do OpenID Connect Provider acessando o `https://example.com/.well-known/openid-configuration`

Este endpoint retorna um documento JSON contendo metadados sobre os endpoints do provedor, métodos de autenticação suportados, emissão de token e muito mais:

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

As informações obtidas do `openid-configuration`ponto final fornecem múltiplas oportunidades de exploração:

1. `Endpoint Discovery`:
    - `Authorization Endpoint`: Identificando a URL para solicitações de autorização do usuário.
    - `Token Endpoint`: Encontrar a URL onde os tokens são emitidos.
    - `Userinfo Endpoint`: Localizando o ponto de extremidade que fornece informações do usuário.
2. `JWKS URI`: O `jwks_uri`revela o `JSON Web Key Set`( `JWKS`), detalhando as chaves criptográficas usadas pelo servidor.
3. `Supported Scopes and Response Types`: Entender quais escopos e tipos de resposta são suportados ajuda a mapear a funcionalidade e as limitações da implementação do OpenID Connect.
4. `Algorithm Details`:Informações sobre algoritmos de assinatura suportados podem ser cruciais para entender as medidas de segurança em vigor.

Explorar o [IANA Registry](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) e experimentar os vários `.well-known`URIs é uma abordagem inestimável para descobrir oportunidades adicionais de reconhecimento da web. Conforme demonstrado com o `openid-configuration`endpoint acima, esses URIs padronizados fornecem acesso estruturado a metadados críticos e detalhes de configuração, permitindo que profissionais de segurança mapeiem de forma abrangente o cenário de segurança de um site.

[Anterior](https://academy.hackthebox.com/module/144/section/3077)