De acordo com uma [pesquisa recente](https://w3techs.com/technologies/details/pl-php) conduzida pela W3Techs, "PHP é usado por `78.6%` de todos os sites cuja linguagem de programação do lado do servidor conhecemos".

Vamos considerar um exemplo prático de preenchimento dos campos de conta de usuário e senha em um formulário de login da web.

#### Página de login do PHP
![[Pasted image 20240830115314.png]]
 Podemos ver um arquivo `login.php`. Então, quando selecionamos o botão de login após preencher o campo Nome de usuário e Senha, essas informações são processadas no lado do servidor usando PHP

## Prática com um Web Shell baseado em PHP.
Neste caso, aproveitaremos a vulnerabilidade no rConfig 3.9.6 para carregar manualmente um shell da web PHP e interagir com o host Linux subjacente.

O rConfig permite que os administradores adicionem dispositivos de rede e os categorizem por fornecedor. Vá em frente e faça login no rConfig com as credenciais padrão (admin:admin), depois navegue até `Devices`> `Vendors`e clique em `Add Vendor`.

#### Aba de fornecedores
![[Pasted image 20240830115600.png]]

Usaremos [o PHP Web Shell do WhiteWinterWolf](https://github.com/WhiteWinterWolf/wwwolf-php-webshell) . Podemos baixar ou copiar e colar o código-fonte em um arquivo `.php`. 

Tenha em mente que o tipo de arquivo é significativo, como veremos em breve. Nosso objetivo é carregar o PHP web shell por meio do botão `browse` do Vendor Logo. Tentar fazer isso inicialmente falhará, pois o rConfig está verificando o tipo de arquivo. Ele permitirá apenas o carregamento de tipos de arquivo de imagem (.png,.jpg,.gif, etc.). No entanto, podemos ignorar isso utilizando `Burp Suite`.

Inicie o Burp Suite, navegue até o menu de configurações de rede do navegador e preencha as configurações de proxy. `127.0.0.1`irá para o campo de endereço IP e `8080`irá para o campo de porta para garantir que todas as solicitações passem pelo Burp (lembre-se de que o Burp atua como proxy da web).

#### Configurações de proxy
![[Pasted image 20240830133652.png]]
Nosso objetivo é alterar o `content-type`para ignorar a restrição do tipo de arquivo no upload de arquivos para serem "apresentados" como o logotipo do fornecedor, para que possamos navegar até esse arquivo e ter nosso shell da web.

## Ignorando a restrição de tipo de arquivo
Clique no botão de navegação, navegue até onde nosso arquivo .php estiver armazenado em nossa caixa de ataque e selecione abrir e `Save`.

Parecerá que a página da web está travando, mas isso é apenas porque precisamos dizer ao Burp para encaminhar as solicitações HTTP. Encaminhe as solicitações até ver a solicitação POST contendo o upload do nosso arquivo. Ficará assim:


#### Solicitação de postagem
![[Pasted image 20240830133747.png]]

Alteraremos Content-type de `application/x-php`para `image/gif`. Isso essencialmente "enganará" o servidor e nos permitirá carregar o arquivo .php, ignorando a restrição do tipo de arquivo.

Depois selecionamos `Forward` duas vezes, e o arquivo será enviado. Podemos desligar o interceptador Burp agora e voltar ao navegador para ver os resultados.

#### Fornecedor adicionado
![[Pasted image 20240830133934.png]]

A mensagem: 'Adicionado novo fornecedor NetVen ao Banco de Dados` nos informa que o upload do nosso arquivo foi bem-sucedido.

Agora podemos tentar usar nosso shell da web. Usando o navegador, navegue até este diretório no servidor rConfig:

`/images/vendor/connect.php`

Isso executa a carga útil e nos fornece uma sessão de shell não interativa inteiramente no navegador, permitindo-nos executar comandos no sistema operacional subjacente.

#### Sucesso do Webshell
![[Pasted image 20240830134028.png]]

## Considerações ao lidar com Web Shells
Ao utilizar web shells, considere os seguintes problemas potenciais que podem surgir durante o processo de teste de penetração:

- Às vezes, os aplicativos da Web excluem arquivos automaticamente após um período predefinido
- Interatividade limitada com o sistema operacional em termos de navegação no sistema de arquivos, download e upload de arquivos, encadeamento de comandos pode não funcionar (por exemplo `whoami && hostname`), retardando o progresso, especialmente ao executar enumeração - Instabilidade potencial por meio de um shell da web não interativo
- Maior chance de deixar para trás provas de que fomos bem-sucedidos em nosso ataque







































































