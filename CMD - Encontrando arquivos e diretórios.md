## Pesquisando com CMD

#### where

```cmd
C:\Users\student\Desktop>where calc.exe

 C:\Windows\System32\calc.exe

C:\Users\student\Desktop>where bio.txt

 INFO: Could not find files for the given pattern(s).
```

Funcionou porque a pasta system32 está em nosso caminho de variável de ambiente, e é lá onde o ``calc.exe`` se encontra. A segunda tentativa falhou, pois o arquivo `bio.txt` está em nossa pasta pessoal.

Para isso podemos especificar em qual diretório se deve procurar. Também podemos usar da recursividade com argumento `/R`

```cmd
C:\Users\student\Desktop>where /R C:\Users\student\ bio.txt

 C:\Users\student\Downloads\bio.txt
```

### Usando coringas

```cmd
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv

C:\Users\student\AppData\Local\live-hosts.csv
```

#### find
Find é usado para pesquisar strings de texto ou sua ausência em um arquivo ou arquivos. Você também pode usar `find`contra a saída do console ou outro comando.

```cmd
C:\Users\student\Desktop> find "password" "C:\Users\student\not-passwords.txt" 
```

Podemos modificar o comportamento do `find`.

- `/V` : Inverte a cláusula do comando, como exemplo, se usarmos para pesquisar uma string em um arquivo ele nos mostrará qualquer linha que não tenha a string especificada.
- `/N` : Retornará o número de linhas.
- `/I` : Ignora a diferenciação entre maiúsculas e minúsculas.

```cmd
C:\Users\student\Desktop> find /N /I /V "IP Address" example.txt  
```

#### findstr
 `findstr`comando é similar a `find`no sentido de que ele busca por arquivos, mas por padrões. Ele procurará por qualquer coisa que corresponda a um padrão, valor regex, curingas e mais. Pense nisso como find2.0. Para aqueles familiarizados com Linux, `findstr`é mais próximo de `grep`.

### Avaliando e classificando arquivos
Agora, vamos discutir algumas opções para avaliar esses arquivos e compará-los entre si.

Os comandos ``comp`` , ``fc`` e ``sort`` são como faremos isso.

#### comp
Verificará cada byte dentro de dois arquivos procurando por diferenças e então exibirá onde elas começam. Por padrão, as diferenças são mostradas em um formato decimal.

- `/A` : Exibe as diferenças no formato ASCII.
- `/L` : Nos fornece os número de linhas.

```cmd
C:\Users\student\Desktop> comp .\file-1.md .\file-2.md

 Comparing .\file-1.md and .\file-2.md...
 Files compare OK  
```
Retornou `OK` pois os arquivos são os mesmos.

```powershell-session
PS C:\htb> echo a > .\file-1.md
PS C:\Users\MTanaka\Desktop> echo a > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
 Comparing .\file-1.md and .\file-2.md...
 Files compare OK
 <SNIP>
PS C:\Users\MTanaka\Desktop> echo b > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
 Comparing .\file-1.md and .\file-2.md...
 Compare error at OFFSET 2
 file1 = a
 file2 = b  
```

#### fc
`FC`difere porque mostrará quais linhas são diferentes, não apenas um caractere individual ( `/A`) ou byte que é diferente em cada linha.

```cmd-session
C:\htb> fc.exe /?
```

Quando o FC realiza sua inspeção, ele faz distinção entre maiúsculas e minúsculas e se importa mais do que apenas uma comparação byte a byte.

- `/N` : Imprime o número de linhas.

```cmd-session
C:\Users\student\Desktop> fc passwords.txt modded.txt /N

Comparing files passwords.txt and MODDED.TXT
***** passwords.txt
    1:  123456
    2:  password
***** MODDED.TXT
    1:  123456
    2:
    3:  password
*****

***** passwords.txt
    5:  12345
    6:  qwerty
***** MODDED.TXT
    6:  12345
    7:  Just something extra to show functionality. Did it see the space inserted above?
    8:  qwerty
*****
```


#### sort
Com `Sort`, podemos receber entrada do console, pipeline ou um arquivo, classificá-la e enviar os resultados para o console ou para um arquivo ou outro comando.
É relativamente simples de usar e frequentemente será usado em conjunto com operadores de pipeline como `|`, `<`e `>`. Podemos tentar agora alimentando o conteúdo do `file`arquivo para classificar.

- `/O` : Envia a saída para algum arquivo
- `/unique` : Não imprime duplicatas.

```cmd-session
C:\Users\student\Desktop> type .\file-1.md
a
b
d
h
w
a
q
h
g

C:\Users\MTanaka\Desktop> sort.exe .\file-1.md /O .\sort-1.md
C:\Users\MTanaka\Desktop> type .\sort-1.md

a
a
b
d
g
h
h
q
w
```

#### /unique
```cmd-session
C:\htb> type .\sort-1.md

a
a
b
d
g
h
h
q
w

PS C:\Users\MTanaka\Desktop> sort.exe .\sort-1.md /unique

a
b
d
g
h
q
w
```



























































