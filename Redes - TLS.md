
## Chave pública e privada
Elas são geradas juntas por algum algoritmo que tem suporte a criptografia assimétrica, como o ``RSA``, `ECDSA` e o `DSA`.

**Chave privada :** É simétrica, e consegue tanto criptografar quanto descriptografar alguma informação. Ela deve ser mantida em total segurança.

**Chave pública :** É assimétrica, só consegue criptografar alguma mensagem, e pode ser divulgada pois não tem a capacidade de descriptografar alguma mensagem.

## TLS ( Transporte Layer Security )
Após o ``Three Way Handshake`` do protocolo ``TCP``, e o cliente ( costuma ser o navegador ) e o servidor ( costuma ser um servidor web ) terem estabelecido uma conexão. Ocorre o ``Handshake`` do protocolo ``TLS`` que acontece nas seguinte etapas :

1. O servidor envia uma cópia da sua chave pública para o cliente.
2. O cliente faz uma cópia da sua chave privada, criptografa com a chave pública do servidor e envia de volta ao servidor.

Assim ambos terão uma cópia da chave privada do cliente, podendo então se comunicar criptografando as mensagens com essa chave, pois somente eles podem descriptografar as mensagens.

Essa chave privada do cliente costuma ser gerada no momento da conexão, serve somente para aquela sessão.

![[Pasted image 20241003165646.png]]

Mas o TLS não garante que o servidor com o qual o cliente esta se comunicando é o servidor confiável ou legítimo, pois algum atacante pode subir algum servidor mal intencionado . Para garantir que o servidor é quem ele diz ser e é confiável, ele precisa ter um certificado assinado por uma CA.