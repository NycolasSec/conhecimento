#cyber 
# Código de autenticação de mensagem baseado em hash (HMAC)

O Código de autenticação de mensagem com base em hash (HMAC) usa uma chave de criptografia com uma função de hash para autenticar um usuário da Web. Muitos serviços da Web usam a autenticação básica, que não criptografa o nome de usuário e a senha durante a transmissão. Usando o HMAC, o usuário envia um identificador com chave privada e um HMAC. O servidor procura a chave privada do usuário e cria um HMAC. O HMAC do usuário deve ser compatível com o calculado pelo servidor.

As VPNs que usam IPsec contam com as funções HMAC para autenticar a origem de cada pacote e fornecer a verificação de integridade de dados.

![[Pasted image 20240613130843.png]]

Os produtos da Cisco usam hash para fins de autenticação de entidade, integridade de dados e autenticidade de dados.

- Os roteadores Cisco IOS usam o hash com chaves secretas de maneira semelhante ao HMAC, para adicionar informações de autenticação às atualizações do protocolo de roteamento.
- Os IPsec gateways e clientes usam os algoritmos hash, como MD5 e SHA-1 no modo de HMAC, para proporcionar a integridade e a autenticidade do pacote.
- As imagens de software Cisco na página da Cisco.com disponibilizam uma soma de verificação de MD5, para que os clientes possam verificar a integridade das imagens baixadas.

















