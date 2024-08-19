## Criação e execução de scripts de shell Bash

A administração de sistemas pode ser simplificada usando ferramentas de linha de comando, especialmente quando tarefas complexas exigem a combinação de vários comandos. O Bash permite criar scripts que automatizam essas tarefas, combinando comandos e lógica de programação para resolver problemas repetitivos.

Esses scripts são arquivos executáveis que podem ser usados para melhorar a eficiência das operações diárias dos administradores de sistemas. Editores de texto avançados, como vim e emacs, são úteis ao escrever scripts, pois destacam a sintaxe do Bash, ajudando a identificar erros comuns.

#### Especificação do interpretador de comandos

A primeira linha de um script começa com a notação `#!`, comumente referida como `she-bang` ou `hash-bang`, dos nomes desses dois caracteres, `sharp` ou `hash` e `bang`.

Essa notação é uma diretiva de interpretador para indicar o interpretador de comando e as opções de comando para processar as linhas restantes no arquivo. Para arquivos de script de sintaxe Bash, a primeira linha é a seguinte diretiva:

```bash
#!/usr/bin/bash
```

#### Executar um script do shell Bash
Um arquivo de script do shell deve ter permissões de execução para ser executado como um comando comum. Use o comando `chmod` para modificar as permissões do arquivo. Use o comando `chown`, se necessário, para conceder permissão de execução somente para usuários ou grupos específicos.

Se o script estiver armazenado em um diretório listado na variável de ambiente `PATH` do shell, você poderá executar o script do shell usando apenas seu nome de arquivo, semelhante à execução de comandos compilados.

Se um script não estiver em um diretório PATH, execute o script usando seu nome de caminho absoluto, que você pode determinar consultando o arquivo com o comando `which`. Como alternativa, execute um script em seu diretório de trabalho atual usando o prefixo de diretório `.`, como `./﻿scriptname`.

```sh
[user@host ~]$ which hello
~/bin/hello
[user@host ~]$ echo $PATH

/home/user/.local/bin:/home/user/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin
```

#### Caracteres especiais colocados entre aspas
Vários caracteres e palavras têm significados especiais para o shell Bash. Para usar esses caracteres nos valores literais, em vez de seus significados especiais, você os _escapa_ no script. Use o caractere de barra invertida (`\`), aspas simples (`''`) ou aspas duplas (`""`) para remover (ou _escapar_) o significado especial desses caracteres.

O caractere de barra invertida remove o significado especial do caractere único que imediatamente segue a barra invertida. Por exemplo, para usar o comando `echo` para exibir a string literal `# not a comment`, o caractere de hash `#` não deve ser interpretado como um comentário.

```sh
[user@host ~]$ echo # not a comment

[user@host ~]$ echo \# not a comment
# not a comment
```

Para escapar mais de um caractere em uma string de texto, use o caractere de barra invertida várias vezes ou coloque toda a string entre aspas simples (`''`) para que seja interpretada literalmente. As aspas simples preservam o sentido literal de todos os caracteres que ficam entre elas.

```sh
[user@host ~]$ echo # not a comment #

[user@host ~]$ echo \# not a comment #
# not a comment
[user@host ~]$ echo \# not a comment \#
# not a comment #
[user@host ~]$ echo '# not a comment #'
# not a comment #
```

Use aspas duplas para suprimir _globbing_ (correspondência de padrões de nomes de arquivos) e a expansão do shell, mas ainda permitir substituição de comandos e de variáveis. A substituição de variáveis é conceitualmente a mesma que a substituição de comandos, mas pode usar sintaxe de chave opcional.

Use aspas simples para interpretar todo o texto entre elas literalmente. Além de suprimir globbing e expansão do shell, as aspas simples também direcionam o shell para suprimir substituição de comandos e variáveis. O ponto de interrogação (`?`) está incluído entre aspas, pois é um _metacaractere_ que também precisa escapar da expansão.

```sh
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

#### Fornecer saída de um script do shell
O comando `echo` exibe um texto arbitrário, transmitindo o texto como um argumento ao comando. Por padrão, o texto é enviado para _a saída padrão (STDOUT)_. Você pode enviar texto para outro lugar usando o redirecionamento de saída.

Você pode enviar texto para outro lugar usando o redirecionamento de saída. No script Bash simples a seguir, o comando `echo` exibe a mensagem `"Hello, world"` para STDOUT, cujo padrão é o dispositivo de tela.

```sh
[user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash

echo "Hello, world"

[user@host ~]$ hello
Hello, world
```

O comando `echo` é amplamente usado em scripts de shell para exibir mensagens informativas ou de erro. As mensagens são indicadores úteis do progresso de um script e podem ser direcionadas para a saída padrão e para o erro padrão ou redirecionadas para um arquivo de log para arquivamento.

Ao exibir mensagens de erro, uma boa prática de programação é redirecioná-las para STDERR para separá-las da saída normal do programa.

```sh
[user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash

echo "Hello, world"
echo "ERROR: Houston, we have a problem." >&2

[user@host ~]$ hello 2> hello.log
Hello, world

[user@host ~]$ cat hello.log
ERROR: Houston, we have a problem.
```