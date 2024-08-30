## ASPX e uma dica de aprendizado rápido
É bom complementar a leitura assistindo a demonstrações e realizando hands-on como temos feito até agora.

Um ótimo recurso para usar no aprendizado é o `IPPSEC's`site do blog [ippsec.rocks](https://ippsec.rocks/?#) . O site é uma ferramenta de aprendizado poderosa. Veja, por exemplo, o conceito de web shells. Podemos usar o site dele para digitar o conceito que queremos aprender, como aspx.

![[Pasted image 20240830114251.png]]
O site dele rastreia as descrições de cada um dos vídeos que ele postou no YouTube e recomenda um timestamp associado àquela palavra-chave.

## ASPX Explicado
`Active Server Page Extended`( `ASPX`) é um tipo de arquivo/extensão escrito para [o Microsoft's ASP.NET Framework](https://docs.microsoft.com/en-us/aspnet/overview) . Em um servidor web executando o ASP.NET framework, páginas de formulário web podem ser geradas para que os usuários insiram dados. No lado do servidor, as informações serão convertidas em HTML.
Podemos criar  um web shell baseado em ASPX para controlar o sistema operacional Windows subjacente.

## Antak Webshell
Antak é um shell da web baseado em ASP.Net incluído no projeto Nishang, um conjunto de ferramentas Offensive PowerShell. Antak utiliza PowerShell para interagir com servidores Windows, oferecendo uma interface com o tema PowerShell. É útil para obter acesso remoto a um servidor web e interagir com ele por meio de um shell da web.

## Trabalhando com Antak

Os arquivos Antak podem ser encontrados no diretório `/usr/share/nishang/Antak-WebShell`.

```shell-session
NycolasRamos@htb[/htb]$ ls /usr/share/nishang/Antak-WebShell

antak.aspx  Readme.md
```
O Antak web shell funciona como um Powershell Console. No entanto, ele executará cada comando como um novo processo. Ele também pode executar scripts na memória e codificar comandos que você enviar.

## Demonstração Antak

#### Mover uma cópia para modificação
```shell-session
NycolasRamos@htb[/htb]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

Certifique-se de definir credenciais para acesso ao shell da web. Modifique `line 14`, adicionando um usuário (seta verde) e uma senha (seta laranja).

#### Modificar o Shell para uso
![[Pasted image 20240830114739.png]]

 Esse host era um host Windows, então nosso shell deve funcionar perfeitamente com o PowerShell. Envie o arquivo e navegue até a página para uso. Ele fornecerá um prompt de usuário e senha.

Ao navegar até o arquivo `upload.aspx`, você deverá ver um prompt como o que temos abaixo.

#### Sucesso da Shell
![[Pasted image 20240830114849.png]]

Como visto na imagem a seguir, teremos acesso se nossas credenciais forem inseridas corretamente.

![[Pasted image 20240830114904.png]]

Agora que temos acesso, podemos utilizar comandos do PowerShell para navegar e tomar ações contra o host. Podemos emitir comandos básicos da janela do shell Antak, carregar e baixar arquivos, codificar e executar scripts e muito mais (seta verde abaixo).

#### Emitindo Comandos
![[Pasted image 20240830115029.png]]


















































