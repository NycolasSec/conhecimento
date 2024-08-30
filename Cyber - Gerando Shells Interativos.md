Pode haver momentos em que chegamos a um sistema com um shell limitado, e o Python não está instalado. Nesses casos, é bom saber que poderíamos usar vários métodos diferentes para gerar um shell interativo.

Saiba que sempre que vemos `/bin/sh`ou `/bin/bash`, isso também pode ser substituído pelo binário associado à linguagem do interpretador de shell presente naquele sistema. Com a maioria dos sistemas Linux, provavelmente encontraremos `bourne shell`( `/bin/sh`) e `bourne again shell`( `/bin/bash`) presentes no sistema nativamente.

## /bin/sh -i
Este comando executará o interpretador de shell especificado no caminho no modo interativo ( `-i`).

#### Interativo
```shell-session
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

## Perl
Se a linguagem de programação [Perl](https://www.perl.org/) estiver presente no sistema, esses comandos executarão o interpretador de shell especificado.

#### Perl para Shell
```shell-session
perl —e 'exec "/bin/sh";'
```

Esse comando deve ser executado a partir de um script.
```shell-session
perl: exec "/bin/sh";
```

## Ruby
Se o [Ruby](https://www.ruby-lang.org/en/) estiver presente no sistema, este comando executará o interpretador de shell especificado:

#### Ruby para Shell
Esse comando deve ser executado a partir de um script.
```shell-session
ruby: exec "/bin/sh"
```

## Lua
 Se a linguagem [Lua](https://www.lua.org/) estiver presente no sistema, podemos usar o `os.execute`método para executar o interpretador de shell especificado usando o comando completo abaixo:

#### Lua para Shell
Esse comando deve ser executado a partir de um script.
```shell-session
lua: os.execute('/bin/sh')
```

## AWK
[AWK](https://man7.org/linux/man-pages/man1/awk.1p.html) é uma linguagem de varredura e processamento de padrões semelhante a C. Pode ser usada para gerar um shell interativo.

#### AWK para Shell
```shell-session
awk 'BEGIN {system("/bin/sh")}'
```

## Find
[Find](https://man7.org/linux/man-pages/man1/find.1.html) é um comando presente na maioria dos sistemas Unix/Linux, amplamente usado para pesquisar e percorrer arquivos e diretórios usando vários critérios. Ele também pode ser usado para executar aplicativos e invocar um interpretador de shell.

#### Usando Find para um Shell
```shell-session
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```
Este uso do comando find busca qualquer arquivo listado após a opção `-name`, então ele executa `awk`( `/bin/awk`) e executa o mesmo script que discutimos na seção awk para executar um interpretador de shell.

## Usando Exec para iniciar um shell
```shell-session
find . -exec /bin/sh \; -quit
```
a opção execute ( `-exec`) serve para iniciar o interpretador shell diretamente. Se `find` não conseguir encontrar o arquivo especificado, então nenhum shell será obtido.

## VIM
Sim, podemos definir a linguagem do interpretador de shell de dentro do popular editor de texto baseado em linha de comando `VIM`. Esta é uma situação muito específica em que nos encontraríamos para precisar usar este método, mas é bom saber, só por precaução.

#### Vim para Shell
```shell-session
vim -c ':!/bin/sh'
```

#### Vim Escape
```shell-session
vim
:set shell=/bin/sh
:shell
```

## Considerações sobre permissões de execução
Devemos estar atentos às permissões que temos com a conta da sessão do shell. Podemos sempre tentar executar este comando para listar as propriedades do arquivo e as permissões que nossa conta tem sobre qualquer arquivo ou binário:

#### Permissões
```shell-session
ls -la <path/to/fileorbinary>
```

Também podemos tentar executar este comando para verificar quais permissões `sudo` a conta em que acessamos tem:

#### Sudo -l
```shell-session
sudo -l
```
![[Pasted image 20240830110346.png]]

O comando sudo -l acima precisará de um shell interativo estável para ser executado.

















































