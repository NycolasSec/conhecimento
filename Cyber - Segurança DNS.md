Muitas empresas estão cientes da vulnerabilidade do DNS e têm adotado medidas como servidores DNS dedicados e software de avaliação de vulnerabilidades para mitigá-la.

Além disso, o DNS está sendo cada vez mais valorizado como uma linha de defesa fundamental em uma estratégia de segurança abrangente, dado que ele está envolvido em todas as conexões de rede.

O DNS pode ser integrado com inteligência de ameaças e sistemas analíticos, como EDR e SIEM, para fornecer uma visão completa da segurança. Serviços de Segurança DNS também ajudam na resposta a incidentes, compartilhando informações de ameaças com outras tecnologias de segurança, como firewalls e scanners de vulnerabilidade.

### DNSSEC
Outro feed usado para a segurança dos servidores DNS é `Domain Name System Security Extensions`( `DNSSEC`), projetado para garantir a autenticidade e a integridade dos dados transmitidos pelo Sistema de Nomes de Domínio, protegendo registros de recursos com certificados digitais.

`DNSSEC`garante que os dados DNS não foram manipulados e não se originam de nenhuma outra fonte. `Private keys` são usados ​​para assinar `Resource Records`digitalmente. `Resource Records`podem ser assinados várias vezes com diferentes chaves privadas, por exemplo, para substituir chaves que expiram com o tempo.

#### Chave privada

O servidor DNS que gerencia uma zona a ser protegida assina seus registros de recursos enviados usando seu único conhecido `private key`. Cada zona tem suas chaves de zona, cada uma consistindo de a `private`e a `public key`.

`DNSSEC`especifica um novo tipo de registro de recurso com o `RRSIG`. Ele contém a assinatura do respectivo registro DNS, e essas chaves usadas têm um período de validade específico e são fornecidas com a `start`e `end date`.

---

#### Chave pública

A chave pública pode ser usada para verificar a assinatura dos destinatários dos dados. Para os mecanismos `DNSSEC` de segurança, ela deve ser suportada pelo provedor das informações de DNS e pelo sistema do cliente solicitante.

Os clientes solicitantes verificam as assinaturas usando a chave pública geralmente conhecida da zona DNS. Se uma verificação for bem-sucedida, a manipulação da resposta será impossível, e as informações vierem da fonte solicitada.