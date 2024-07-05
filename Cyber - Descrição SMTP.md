O `Simple Mail Transfer Protocol`( `SMTP`) é um protocolo para enviar e-mails em uma rede IP.

Ele pode ser usado entre um cliente de e-mail e um servidor de e-mail de saída ou entre dois servidores SMTP.

O SMTP é frequentemente combinado com os protocolos IMAP ou POP3, que podem buscar e-mails e enviar e-mails. Em princípio, é um protocolo baseado em cliente-servidor, embora o SMTP possa ser usado entre um cliente e um servidor e entre dois servidores SMTP. Nesse caso, um servidor atua efetivamente como um cliente.

Por padrão os servidores SMTP aceitam solicitações na porta 25, no entanto os servidores mais novos usam a porta TCP 587.

Essa porta é usada para receber e-mails de usuários/servidores autenticados, geralmente usando o comando STARTTLS para alternar a conexão de texto simples existente para uma conexão criptografada.

Os dados de autenticação são protegidos e não são mais visíveis em texto simples na rede. No início da conexão, a autenticação ocorre quando o cliente confirma sua identidade com um nome de usuário e senha.

Os e-mails podem então ser transmitidos. Para esse propósito, o cliente envia ao servidor os endereços do remetente e do destinatário, o conteúdo do e-mail e outras informações e parâmetros. Após o e-mail ter sido transmitido, a conexão é encerrada novamente. O servidor de e-mail então começa a enviar o e-mail para outro servidor SMTP.

Após o e-mail ter sido transmitido, a conexão é encerrada novamente. O servidor de e-mail então começa a enviar o e-mail para outro servidor SMTP.

O SMTP funciona sem criptografia, Para evitar a leitura não autorizada, é usado junto com a criptografia SSL/TLS. Em alguns momentos o servidor pode usar uma porta diferente da porta padrão 25, como a porta 465,

Uma função essencial desse tipo de servidor é evitar spam, usando mecanismos de autenticação.

Para esse propósito, a maioria dos servidores SMTP modernos suporta a extensão de protocolo ESMTP com SMTP-Auth. Após enviar seu e-mail, o cliente SMTP, também conhecido como `Mail User Agent`( `MUA`), o converte em um cabeçalho e um corpo e carrega ambos para o servidor SMTP.

 Isso tem um chamado `Mail Transfer Agent`( `MTA`), a base de software para enviar e receber e-mails. O MTA verifica o tamanho e o spam do e-mail e o armazena.

Para aliviar o MTA, ele é ocasionalmente precedido por um `Mail Submission Agent`( `MSA`), que verifica a validade, ou seja, a origem do e-mail. Isso `MSA`também é chamado de `Relay`servidor. Estes são muito importantes mais tarde, pois o chamado `Open Relay Attack`pode ser realizado em muitos servidores SMTP devido à configuração incorreta. Discutiremos esse ataque e como identificar o ponto fraco para ele um pouco mais tarde. O MTA então pesquisa o DNS para o endereço IP do servidor de e-mail do destinatário.

Ao chegar ao servidor SMTP de destino, os pacotes de dados são remontados para formar um e-mail completo. De lá, o `Mail delivery agent`( `MDA`) o transfere para a caixa de correio do destinatário.

|Cliente ( `MUA`)|`➞`|Agente de Submissão ( `MSA`)|`➞`|Revezamento aberto ( `MTA`)|`➞`|Agente de entrega de correspondência ( `MDA`)|`➞`|Caixa de correio ( `POP3`/ `IMAP`)|
|---|---|---|---|---|---|---|---|---|

Mas o SMTP tem duas desvantagens inerentes ao protocolo de rede.

1. A primeira é que enviar um e-mail usando SMTP não retorna uma confirmação de entrega utilizável.
2. Os usuários não são autenticados quando uma conexão é estabelecida e, portanto, o remetente de um e-mail não é confiável. Como resultado, os relés SMTP abertos são frequentemente usados ​​indevidamente para enviar spam em massa. Os originadores usam endereços de remetentes falsos arbitrários para esse propósito para não serem rastreados (falsificação de e-mail).

Hoje, muitas técnicas de segurança diferentes são usadas para evitar o uso indevido de servidores SMTP. Por exemplo, e-mails suspeitos são rejeitados ou movidos para quarentena (pasta de spam). Por exemplo, os responsáveis ​​por isso são o protocolo de identificação [DomainKeys](http://dkim.org/) ( `DKIM`), o [Sender Policy Framework](https://dmarcian.com/what-is-spf/) ( `SPF`).

Para esse propósito, foi desenvolvida uma extensão para SMTP chamada `Extended SMTP`( `ESMTP`). Quando as pessoas falam sobre SMTP em geral, elas geralmente querem dizer ESMTP. O ESMTP usa TLS, que é feito após o `EHLO`comando enviando `STARTTLS`. Isso inicializa a conexão SMTP protegida por SSL e, a partir desse momento, toda a conexão é criptografada e, portanto, mais ou menos segura. Agora, a extensão [AUTH PLAIN](https://www.samlogic.net/articles/smtp-commands-reference-auth.htm) para autenticação também pode ser usada com segurança.
































