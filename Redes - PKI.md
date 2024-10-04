##### Intro
Somente o TLS não garante que o servidor com quem o cliente está se comunicando é seguro, para isso existe a PKI que atesta que um servidor é quem diz ser através de um certificado assinado por uma CA confiável.

### Processo
Para que um servidor garanta a sua identidade com um certificado primeiro ele precisa ter um certificado assinado por uma CA confiável. Esse processo ocorre nas seguintes etapas :

##### Criação das chaves
O requerente cria um par de chaves ( chave privada e chave pública ) com alguma ferramenta que trabalha com um algoritmo de criptografia assimétrica (``RSA``, `ECDSA` ou ``DSA``) como o `openssl` ou ``ssh-keygen``.

##### Solicitação de assinatura de certificado (CSR)
Após a criação das chaves, se cria um arquivo CSR. O CSR contém os dados que a CA utilizará para validar a autenticidade do requerente, além da chave pública do requerente. O CSR também deve ser assinada com o chave privada do requerente, para provar que ele possuíLembrando que nunca enviamos a chave privada, somente a chave pública.


---
### Verificação do certificado
Mesmo que o servidor tenha um certificado, o cliente precisa saber quem assinou o certificado e validar se foi alguma CA intermediária confiável. Isso ocorre da seguinte maneira :

##### Verificação
O cliente abre o certificado e verifica as informações para saber se o certificado expirou, quem assinou e etc. Como esse certificado assinado também contém quem o assinou, o cliente vai subindo nessa cadeia de forma recursiva e verifica se cada certificado é válido. 

Quando essa cadeia chegar na CA intermediária que foi assinada por uma root CA, a partir desse ponto a identidade dessa root CA será atestada por um root program instalado no cliente.

##### Verificação do Root program
O root program do cliente irá verificar nas suas relações de certificados se o certificado da root CA que iniciou a cadeia de assinatura é válido.

##### Resultado
Caso o certificado seja classificado como inválido,  o servidor é dado como inseguro. Caso seja válido o servidor será classificado como confiável.

>[!important] Caso o `root program` do cliente seja adulterado, ele poderá fornecer confiança indevida a uma `root CA` falsa.

---
### Definições

##### CSR (solicitação de assinatura de certificado)
Gerada no mesmo servidor em que você planeja instalar o certificado, a CSR contém informações (por exemplo, nome comum, empresa, país) que a Autoridade Certificadora (AC) usará para criar seu certificado. Ela também contém a chave pública que será incluída em seu certificado e é assinada com a chave privada correspondente.