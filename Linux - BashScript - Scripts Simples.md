Se pode realizar várias tarefas pela linha de comando, mas tarefas mais complexas necessitam de encadeamento de comandos que passam resultados entre eles. Usando um ambiente bash e recursos de script podemos realizar essas tarefas.

Um script de bash é um arquivo que contém uma lista de comandos e uma lógica para produzir os resultados esperados.


### Especificando o interpretador

A primeira linha começa com `#!`, mas chamada de ``shee-bang``, ela indica o caminho do interpretador que irá interpretar o script.

```bash
#!/usr/bin/bash
```

### Executar um script Bash Shell
Para ser executado o arquivo do script precisa ter permissão de execução. Usamos o comando `chmod` para executar essa ação.

```bash-session
[user@host ~]$ chmod +x ./script.sh
```
Assim damos permissão de execução para o script.

Podemos executa-lo assim:
```bash-session
[user@host ~]$ ./script.sh
#ou
[user@host ~]$ ./script
```

Caso o script esteja na variável de ambiente do shell ``$PATH``, o script pode ser chamado apenas passando o nome do seu arquivo, não necessitando da extensão e nem do caminho absoluto.

```bash-session
[user@host ~]$ which hello
~/bin/hello

[user@host ~]$ echo $PATH
/home/user/.local/bin:/home/user/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin
```

### Citar caracteres especiais
Alguns nomes e caracteres tem significado especial para o bash. Para usarmos os caracteres em seu valor literal e não especial devemos escapa-los com uma barra invertida (``\``) ,aspas simples `''` ou aspas duplas `""` .

O caractere de barra invertida remove o significado especial  do caractere único que segue a barra.

O exemplo a seguir mostra o caractere de barra invertida ( `\`) modificando o caractere de hash para que ele não seja interpretado como um comentário:

```bash-session
[user@host ~]$ echo # not a comentary

[user@host ~]$ echo \# not a comentary
# not a comentary

```

Para escapar mais de um caractere em uma sequência de texto, use o caractere de barra invertida várias vezes ou coloque a sequência inteira entre aspas simples ( `''`) para interpretar literalmente. Aspas simples preservam o significado literal de todos os caracteres que elas colocam.

```bash-session
[user@host ~]$ echo # not a comment #

[user@host ~]$ echo \# not a comment #
# not a comment
[user@host ~]$ echo \# not a comment \#
# not a comment #
[user@host ~]$ echo '# not a comment #'
# not a comment #
```

Use aspas duplas para suprimir_englobamento_(correspondência de padrão de nome de arquivo) e expansão de shell, mas ainda permite substituição de comando e variável.

Substituição de variável é conceitualmente a mesma que substituição de comando, mas pode usar sintaxe de chaves opcional.

Use aspas simples para interpretar todo o texto incluso literalmente. Além de suprimir globbing e expansão de shell, aspas simples também direcionam o shell a suprimir substituição de comando e variável. O ponto de interrogação ( `?`) é incluído dentro das aspas, porque é um_metacaractere_que também precisa escapar da expansão.

```bsah-session
[user@host ~]$ var=$(hostname -s); echo $var
host

[user@host ~]$ echo "***** hostname is ${var} *****"
***** hostname is host *****

[user@host ~]$ echo Your username variable is \$USER.
Your username variable is $USER.

[user@host ~]$ echo "Will variable $var evaluate to $(hostname -s)?"
Will variable host evaluate to host?

[user@host ~]$ echo 'Will variable $var evaluate to $(hostname -s)?'
Will variable $var evaluate to $(hostname -s)?

[user@host ~]$ echo "\"Hello, world\""
"Hello, world"

[user@host ~]$ echo '"Hello, world"'
"Hello, world"
```

### Fornecer saída de um script shell

O comando echo exibe texto arbitrário passando o texto como um argumento para o comando.

 Por padrão, o texto é enviado para _saída padrão (STDOUT)_. Você pode enviar texto para outro lugar usando redirecionamento de saída. No script Bash simples a seguir, o comando `echo` exibe a mensagem `"Hello, world"` para STDOUT, que assume como padrão o dispositivo de tela.

```bsah-session
[user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash 

echo "Olá, mundo" 

[user@host ~]$ hello
Olá, mundo
```

O comando `echo` é amplamente usado em scripts de shell para exibir mensagens informativas ou de erro.

As mensagens indicam de forma útil o progresso de um script e podem ser direcionadas para saída padrão ou erro padrão, ou ser redirecionadas para um arquivo de log para arquivamento. Quando você exibe mensagens de erro, uma boa prática de programação é redirecionar as mensagens de erro para STDERR para separá-las da saída normal do programa.

```bsah-session
[user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash 

echo "Olá, mundo" 
echo "ERRO: Houston, temos um problema." >&2 

[user@host ~]$ hello 2> hello.log
Olá, mundo 

[user@host ~]$ cat hello.log
ERRO: Houston, temos um problema.
```