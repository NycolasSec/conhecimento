## HTTP
Hypertext Transfer Protocol ( `HTTP`) é um protocolo de Application Layer sem estado. O HTTP permite a transferência de dados em texto simples entre um cliente e um servidor por TCP.

O cliente enviaria uma solicitação HTTP ao servidor, solicitando um recurso. Uma sessão é estabelecida e o servidor responde com a mídia solicitada (HTML, imagens, hiperlinks, vídeo). O HTTP utiliza as portas 80 ou 8000 por TCP durante operações normais. Em circunstâncias excepcionais, ele pode ser modificado para usar portas alternativas ou, às vezes, UDP.

### Métodos HTTP
Para executar as operações usa-se métodos HTTP, para definir as ações da requisição.

|**Método**|**Descrição**|
|---|---|
|`HEAD`|`required`é um método seguro que solicita uma resposta do servidor similar a uma solicitação Get, exceto que o corpo da mensagem não é incluído. É uma ótima maneira de adquirir mais informações sobre o servidor e seu status operacional.|
|`GET`|`required`Get é o método mais comum usado. Ele solicita informações e conteúdo do servidor. Por exemplo, `GET http://10.1.1.1/Webserver/index.html`solicita a página index.html do servidor com base em nosso URI fornecido.|
|`POST`|`optional`Post é uma maneira de enviar informações para um servidor com base nos campos da solicitação. Por exemplo, enviar uma mensagem para um post do Facebook ou fórum de site é uma ação POST. A ação real tomada pode variar com base no servidor, e devemos prestar atenção aos códigos de resposta enviados de volta para validar a ação.|
|`PUT`|`optional`Put pegará os dados anexados à mensagem e os colocará sob o URI solicitado. Se um item ainda não existir lá, ele criará um com os dados fornecidos. Se um objeto já existir, o novo PUT será considerado o mais atualizado, e o objeto será modificado para corresponder. A maneira mais fácil de visualizar as diferenças entre PUT e POST é pensar assim; PUT criará ou atualizará um objeto no URI fornecido, enquanto POST criará entidades filhas no URI fornecido. A ação tomada pode ser comparada com a diferença entre criar um novo arquivo vs. escrever comentários sobre esse arquivo na mesma página.|
|`DELETE`|`optional`Delete faz como o nome indica. Ele removerá o objeto no URI fornecido.|
|`TRACE`|`optional`Permite diagnóstico de servidor remoto. O servidor remoto ecoará a mesma solicitação que foi enviada em sua resposta se o método TRACE estiver habilitado.|
|`OPTIONS`|`optional`O método Options pode reunir informações sobre os métodos HTTP suportados que o servidor reconhece. Dessa forma, podemos determinar os requisitos para interagir com um recurso ou servidor específico sem realmente solicitar dados ou objetos dele.|
|`CONNECT`|`optional`Connect é reservado para uso com Proxies ou outros dispositivos de segurança como firewalls. Connect permite tunelamento sobre HTTP. ( `SSL tunnels`)|
 
 Como um requisito do padrão, GET e HEAD devem sempre funcionar e existir com implementações HTTP padrão.
 
Os métodos trace, options, delete, put e post são funcionalidades opcionais que podem ser permitidas.

## HTTPS
HTTP Secure ( `HTTPS`) é uma modificação do protocolo HTTP projetada para utilizar Transport Layer Security ( `TLS`) ou Secure Sockets Layer ( `SSL`) com aplicativos mais antigos para segurança de dados.

Embora seja HTTP em sua base, o HTTPS utiliza as portas 443 e 8443 em vez da porta padrão 80. Vamos dar uma olhada em uma saída de tráfego HTTPS e discernir como a `TLS handshake`funciona por um minuto.

### Handshake TLS via HTTPS

![[https.webp]]

Nos primeiros pacotes, podemos ver que o cliente estabelece uma sessão com o servidor usando a porta 443 (Caixa azul).

Uma vez que uma sessão é iniciada via TCP, um TLS ClientHello é enviado em seguida para iniciar o handshake TLS.
Durante o handshake, vários parâmetros são acordados, incluindo identificador de sessão, certificado peer x509, algoritmo de compressão a ser usado, o algoritmo de criptografia cipher spec, se a sessão é retomável e um segredo mestre de 48 bytes compartilhado entre o cliente e o servidor para validar a sessão.

Uma vez que a sessão é estabelecida, todos os dados e métodos serão enviados através da conexão TLS e aparecerão como TLS Application Data `as seen in the red box`.

#### Resumindo o Handshake
1. Cliente e servidor trocam mensagens de saudação para concordar com os parâmetros de conexão.
2. Cliente e servidor trocam parâmetros criptográficos necessários para estabelecer um segredo pré-mestre.
3. Cliente e servidor trocarão certificados x.509 e informações criptográficas permitindo autenticação dentro da sessão.
4. Gere um segredo mestre a partir do segredo pré-mestre e troque valores aleatórios.
5. Cliente e servidor emitem parâmetros de segurança negociados para a parte da camada de registro do protocolo TLS.
6. O cliente e o servidor verificam se o seu par calculou os mesmos parâmetros de segurança e se o handshake ocorreu sem adulteração por um invasor.

## FTP

File Transfer Protocol ( `FTP`) é um protocolo Application Layer que permite a transferência rápida de dados entre dispositivos de computação. O próprio FTP é estabelecido como um protocolo inseguro, e a maioria dos usuários passou a utilizar ferramentas como SFTP

Quando pensamos em comunicação entre hosts, normalmente pensamos em um cliente e um servidor falando por um único soquete.

Nesse aspecto, o FTP é único, pois utiliza várias portas ao mesmo tempo. O FTP usa as portas 20 e 21 sobre TCP. A porta 20 é usada para transferência de dados, enquanto a porta 21 é utilizada para emitir comandos que controlam a sessão FTP.

Em relação à autenticação, o FTP suporta autenticação de usuário, bem como permite acesso anônimo, se configurado.

O FTP é capaz de rodar em dois modos diferentes, `active`ou `passive`.

#### Exemplos de comando e resposta FTP
![[ftp-example.webp]]

A imagem acima mostra vários exemplos de solicitações emitidas pelo canal de comando FTP `green arrows`e as respostas enviadas de volta do servidor FTP `blue arrows`.

Alguns comandos comuns que podemos ver passando pela porta 21 incluem:

| **Comando** | **Descrição**                                                  |
| ----------- | -------------------------------------------------------------- |
| `USER`      | especifica o usuário para efetuar login.                       |
| `PASS`      | envia a senha para o usuário que tenta efetuar login.          |
| `PORT`      | quando em modo ativo, isso alterará a porta de dados usada.    |
| `PASV`      | alterna a conexão com o servidor do modo ativo para o passivo. |
| `LIST`      | exibe uma lista dos arquivos no diretório atual.               |
| `CWD`       | mudará o diretório de trabalho atual para um especificado.     |
| `PWD`       | imprime o diretório em que você está trabalhando no momento.   |
| `SIZE`      | retornará o tamanho de um arquivo especificado.                |
| `RETR`      | recupera o arquivo do servidor FTP.                            |
| `QUIT`      | encerra a sessão.                                              |

## PMEs
Server Message Block ( `SMB`) é um protocolo mais amplamente visto em ambientes empresariais Windows que permite o compartilhamento de recursos entre hosts em arquiteturas de rede comuns. SMB é um protocolo orientado a conexão que requer autenticação do usuário do host para o recurso para garantir que o usuário tenha permissões corretas para usar esse recurso ou executar ações.

Como usuário, o SMB nos fornece acesso fácil e conveniente a recursos como impressoras, drives compartilhados, servidores de autenticação e muito mais.

Como qualquer outro aplicativo que usa TCP como seu mecanismo de transporte, ele executará funções padrão como o handshake de três vias e o reconhecimento de pacotes recebidos.

#### PME na linha
![[smb-actions.webp]]
Neste exemplo, há muitos erros, o que é um exemplo de algo para se aprofundar mais. Uma ou duas falhas de autenticação de um usuário são relativamente comuns, mas um grande cluster delas se repetindo pode sinalizar um indivíduo potencialmente não autorizado tentando acessar a conta de um usuário ou usar suas credenciais para se mover.







































