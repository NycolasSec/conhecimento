Expressões regulares fornecem um mecanismo de correspondência de padrões para encontrar conteúdo específico. Os comandos `vim`, `grep`e `less`podem usar expressões regulares.

### Descreva uma expressão regular simples
A expressão regular mais simples é a correspondência exata da string a ser pesquisada.

Imagine que um usuário esteja procurando no seguinte arquivo por todas as ocorrências do padrão `cat`:
```txt
cat
dog
concatenate
dogma
category
educated
boondoggle
vindication
chilidog
```

A string ``cat`` e a correspondência exata do caractere c, seguido do caractere ``a`` seguido do caractere ``c``, sem outros caracteres entre eles.
Pesquisar o arquivo com a string `cat` como expressão regular retorna as seguintes correspondências.

```txt
cat
con catenate
category 
edu cated 
vindi cation
```

### Combine o início e o fim de uma linha
A expressão regular corresponderia à string de pesquisa em qualquer lugar da linha em que ocorreu: início, meio ou fim. Use um metacaractere âncora de linha para controlar onde procurar uma correspondência.

Use o circunflexo(``^``) para corresponder o início de uma linha e cifrão para o final (`$`)

Com o mesmo arquivo do exemplo anterior, a expressão `^cat` regular corresponderia a duas linhas.
```txt
cat
categoria
```

A expressão regular `cat$` encontraria apenas uma correspondência, onde os caracteres `cat` estão no final de uma linha.
```txt
cat
```

Localize as linhas no arquivo que terminam com `dog`, usando uma âncora de fim de linha para criar a expressão regular `dog$`, que corresponde a duas linhas:
```txt
dog
pimentadog
```

Para localizar uma linha que contém somente a expressão de busca exatamente, use as âncoras de início e fim de linha. Por exemplo, para localizar a palavra `cat`quando ela estiver no início e no fim de uma linha simultaneamente, use `^cat$`.
```txt
cat
```

### Expressão regular básica e estendida
Os dois tipos de expressões regulares são expressões regulares básicas e expressões regulares estendidas.

Uma diferença entre expressões regulares básicas e estendidas é o comportamento dos caracteres especiais `|`, `+`, `?`, `(`, `)`, `{` e `}`. Na sintaxe básica de expressões regulares, esses caracteres terão um significado especial somente se forem prefixados com um caractere de barra invertida `\`. Na sintaxe estendida de expressões regulares, esses caracteres são especiais, a menos que sejam prefixados com um caractere de barra invertida `\`.
Outras diferenças secundárias se referem a como os caracteres `^`, `$` e `*` são processados.

Os comandos `grep`, `sed` e `vim` usam expressões regulares básicas. A opção `-E` do comando `grep`, a opção `-E` do comando `sed` e o comando `less` usam expressões regulares estendidas.


### Uso de curingas multiplicadores em expressões regulares

As expressões regulares usam um caractere de ponto (`.`) como um caractere curinga para corresponder a qualquer caractere único em uma única linha. A expressão regular `c.t` procura por uma string que contenha um `c` seguido por qualquer caractere único seguido por um `t`. As correspondências do exemplo incluem `cat`, `concatenate`, `vindication`, `cut` e `c$t`.

Com um curinga irrestrito, não é possível prever o caractere que corresponde ao curinga. Para corresponder a caracteres específicos, substitua o curinga irrestrito por caracteres adequados.

O uso de caracteres de colchetes, como na expressão regular `c[aou]t`, corresponde a padrões que começam com um `c`, seguido de um `a`, `o` ou `u`, seguido de um `t`. As possíveis expressões correspondentes podem ser as strings `cat`, `cot` e `cut`.

Os multiplicadores se aplicam ao caractere anterior ou curinga na expressão regular. Um multiplicador frequentemente usado é o caractere asterisco (`*`). Quando usado em uma expressão regular, esse multiplicador asterisco corresponde a zero ou mais ocorrências da expressão multiplicada.

Por exemplo, a expressão regular `c[aou]*t` pode corresponder a `coat` ou `coot`. Uma expressão regular de `c.*t` corresponde a `cat`, `coat`, `culvert` e até mesmo `ct` (zero caracteres entre o `c` e o `t`). Qualquer string que comece com `c`, seja seguida por zero ou mais caracteres e termine com `t` é uma correspondência.

Outro tipo de multiplicador indica um número de caracteres mais preciso no padrão. Um exemplo de um multiplicador explícito é a expressão regular `'c.\{2\}t'`, que corresponde a qualquer palavra que comece com um `c`, seguido por exatamente quaisquer dois caracteres, e termine com um `t`. A expressão `'c.\{2\}t'` corresponderia a duas palavras no seguinte exemplo:

```txt
cat
coat
convert
cart
covert
cypher
```

| Sintaxe básica | Sintaxe estendida | Descrição                                                                                                                                        |
| :------------- | :---------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| .              |                   | O período (`.`) corresponde a qualquer caractere único.                                                                                          |
| ?              |                   | O item anterior é opcional e correspondido no máximo uma vez.                                                                                    |
| *              |                   | O item anterior é correspondido zero ou mais vezes.                                                                                              |
| +              |                   | O item anterior é correspondido uma ou mais vezes.                                                                                               |
| \\{`n`\\}      | {`n`}             | O item anterior é correspondido exatamente `n` vezes.                                                                                            |
| \\{`n`,\\}     | {`n`,}            | O item anterior é correspondido `n` ou mais vezes.                                                                                               |
| \\{,`m`\\}     | {,`m`}            | O item anterior é correspondido no máximo `m` vezes.                                                                                             |
| \\{`n`,`m`\\}  | {`n`,`m`}         | O item anterior é correspondido no máximo `n` vezes, mas não mais do que `m` vezes.                                                              |
| [:alnum:]      |                   | Caracteres alfanuméricos `[:alpha:]` e `[:digit:]`. No idioma 'C' e na codificação de caracteres ASCII, essa expressão equivale a `[0-9A-Za-z]`. |
| [:alpha:]      |                   | Caracteres alfabéticos `[:lower:]` e `[:upper:]`. No idioma 'C' e na codificação de caracteres ASCII, essa expressão equivale a `[A-Za-z]`.      |
| [:blank:]      |                   | Caracteres em branco: espaço e tabulação.                                                                                                        |
| [:cntrl:]      |                   | Caracteres de controle. Em ASCII, esses caracteres têm códigos octais de 000 a 037, e 177 (DEL).                                                 |
| [:digit:]      |                   | Dígitos: `0 1 2 3 4 5 6 7 8 9`.                                                                                                                  |
| [:graph:]      |                   | Caracteres gráficos `[:alnum:]` e `[:punct:]`.                                                                                                   |
| [:lower:]      |                   | Letras minúsculas. No idioma 'C' e na codificação de caracteres ASCII: `a b c d e f g h i j k l m n o p q r s t u v w x y z`.                    |
| [:print:]      |                   | Caracteres imprimíveis `[:alnum:]`, `[:punct:]` e espaço.                                                                                        |
| [:punct:]      |                   | Caracteres de pontuação. No idioma 'C' e na codificação de caracteres ASCII: `! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ' { \| } ~`. |
| [:space:]      |                   | Caracteres de espaço. No idioma 'C', isso significa tabulação, nova linha, tabulação vertical, avanços de página, retornos e espaço.             |
| [:upper:]      |                   | Letras maiúsculas: no idioma 'C' e na codificação de caracteres ASCII que é: `A B C D E F G H I J K L M N O P Q R S T U V W X Y Z`.              |
| [:xdigit:]     |                   | Dígitos hexadecimais: `0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f`.                                                                             |
| \\b            |                   | Corresponder a string vazia na borda de uma palavra.                                                                                             |
| \\B            |                   | Corresponder a string vazia fornecida que não esteja na borda de uma palavra.                                                                    |
| \\<            |                   | Corresponder a string vazia no início da palavra.                                                                                                |
| \\>            |                   | Corresponder a string vazia no final da palavra.                                                                                                 |
| \\w            |                   | Corresponder o constituinte da palavra. Sinônimo de `[_[:alnum:]]`.                                                                              |
| \\W            |                   | Corresponder o constituinte que não é uma palavra. Sinônimo de `[^_[:alnum:]]`.                                                                  |
| \\s            |                   | Corresponda o espaço em branco. Sinônimo de `[[:space:]]`.                                                                                       |
| \\S            |                   | Corresponder o que não é espaço em branco. Sinônimo de `[^[:space:]]`.                                                                           |

## Corresponder expressões regulares da linha de comando
O comando `grep` usa expressões regulares para isolar dados correspondentes. Você pode usar o comando `grep` para corresponder dados em um único arquivo ou em vários arquivos. Quando você usa `grep` para corresponder dados em vários arquivos, ele imprime o nome do arquivo seguido por um caractere de dois-pontos e as linhas que correspondem à expressão regular.

### Isolamento de dados usando o grep
O comando `grep` especifica uma expressão regular e um arquivo para analisar as correspondências.

```bash-session
[user@host ~]$ grep '^computer' /usr/share/dict/words
computer
computerese
computerise
computerite
computerizable
computerization
computerize
computerized
computerizes
computerizing
computerlike
computernik
computers
```

O comando grep pode processar a saída de outros comandos usando um caractere de operador de pipe (|). O exemplo a seguir mostra o comando grep analisando as linhas da saída de outro comando.

```bash-session
[root@host ~]# ps aux | grep chrony
chrony     662  0.0  0.1  29440  2468 ?        S    10:56   0:00 /usr/sbin/chronyd
```

### Opções de comando grep
O comando `grep` tem muitas opções para controlar como ele analisa as linhas.

| Opção       | Função                                                                                                                              |
| :---------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `-i`        | Usa a expressão regular fornecida e não distingue letras maiúsculas de minúsculas (executa de modo não distintivo).                 |
| `-v`        | Exibe somente as linhas que _não_ contenham correspondências à expressão regular.                                                   |
| `-r`        | Pesquisa os dados que correspondem à expressão regular de forma recursiva em um grupo de arquivos ou diretórios.                    |
| `-A NUMBER` | Exibe um _NUMBER_ de linhas após a correspondência da expressão regular.                                                            |
| `-B NUMBER` | Exibe um _NUMBER_ de linhas antes da correspondência da expressão regular.                                                          |
| `-e`        | Se várias opções `-e` forem usadas, várias expressões regulares poderão ser fornecidas e serão usadas com um OR lógico.             |
| `-E`        | Use sintaxe de expressão regular estendida em vez da sintaxe de expressão regular básica ao analisar a expressão regular fornecida. |

Visualize as páginas `man` para encontrar outras opções para o comando `grep`.

### Exemplos do comando grep
Os próximos exemplos usam arquivos de configuração e arquivos de log variados.

As expressões regulares diferenciam maiúsculas e minúsculas por padrão. Use a opção `-i` do comando `grep` para executar uma pesquisa que diferencia maiúsculas e minúsculas. O exemplo a seguir mostra um trecho do arquivo de configuração `/etc/httpd/conf/httpd.conf`.

```bash-session
[user@host ~]$ cat /etc/httpd/conf/httpd.conf
...output omitted...
ServerRoot "/etc/httpd"

#
# Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on a specific IP address, but note that if
# httpd.service is enabled to run at boot time, the address may not be
# available when the service starts.  See the httpd.service(8) man
# page for more information.
#
#Listen 12.34.56.78:80
Listen 80
...output omitted...
```

O exemplo a seguir procura a expressão regular `serverroot` no arquivo de configuração `/etc/httpd/conf/httpd.conf`.

```bash-session
[user@host ~]$ grep -i serverroot /etc/httpd/conf/httpd.conf
# with "/", the value of ServerRoot is prepended -- so 'log/access_log'
# with ServerRoot set to '/www' will be interpreted by the
# ServerRoot: The top of the directory tree under which the server's
# ServerRoot at a non-local disk, be sure to specify a local disk on the
# same ServerRoot for multiple httpd daemons, you will need to change at
ServerRoot "/etc/httpd"
```

Use a opção `-v` de comando `grep` para _reverter_ a pesquisa da expressão regular. Essa opção só exibe linhas que _não_ correspondem à expressão regular.

No exemplo a seguir, todas as linhas, independentemente do caso, que _não_ contenham a expressão regular `server` são retornadas.

```bash-session
[user@host ~]$ grep -v -i server /etc/hosts
127.0.0.1 localhost.localdomain localhost
172.25.254.254 classroom.example.com classroom
172.25.254.254 content.example.com content
172.25.254.254 materials.example.com materials
### rht-vm-hosts file listing the entries to be appended to /etc/hosts

172.25.250.9	workstation.lab.example.com workstation
172.25.250.254	bastion.lab.example.com bastion
172.25.250.220  utility.lab.example.com utility
172.25.250.220  registry.lab.example.com registry
```

Para ver um arquivo sem a distração de linhas de comentários, use a opção `-v` do comando `grep`. No exemplo a seguir, a expressão regular corresponde e exclui todas as linhas que começam com um caractere de hash (`#`) ou um caractere de ponto-e-vírgula (`;`) no arquivo `/etc/systemd/system/multi-user.target.wants/rsyslog.service`.

Nesse arquivo, o caractere hash no início de uma linha indica um comentário geral, enquanto o caractere ponto-e-vírgula refere-se a um valor de variável comentado.

```bash-session
[user@host ~]$ grep -v '^[#;]' \
/etc/systemd/system/multi-user.target.wants/rsyslog.service
[Unit]
Description=System Logging Service
Documentation=man:rsyslogd(8)
Documentation=https://www.rsyslog.com/doc/

[Service]
Type=notify
EnvironmentFile=-/etc/sysconfig/rsyslog
ExecStart=/usr/sbin/rsyslogd -n $SYSLOGD_OPTIONS
ExecReload=/usr/bin/kill -HUP $MAINPID
UMask=0066
StandardOutput=null
Restart=on-failure

LimitNOFILE=16384

[Install]
WantedBy=multi-user.target
```

A opção `-e` do comando `grep` pode pesquisar mais de uma expressão regular por vez. O exemplo a seguir, usando uma combinação dos comandos `less` e `grep`, localiza todas as ocorrências de `pam_unix`, `user root` e `Accepted publickey` no arquivo de log `/var/log/secure`.

```bash-session
[root@host ~]# cat /var/log/secure | grep -e 'pam_unix' \
-e 'user root' -e 'Accepted publickey' | less
Mar  4 03:31:41 localhost passwd[6639]: pam_unix(passwd:chauthtok): password changed for root
Mar  4 03:32:34 localhost sshd[15556]: Accepted publickey for devops from 10.30.0.167 port 56472 ssh2: RSA SHA256:M8ikhcEDm2tQ95Z0o7ZvufqEixCFCt+wowZLNzNlBT0
Mar  4 03:32:34 localhost systemd[15560]: pam_unix(systemd-user:session): session opened for user devops(uid=1001) by (uid=0)
```

Para pesquisar texto em um arquivo aberto com os comandos `vim` ou `less`, primeiro digite o caractere de barra (`/`) e, em seguida, digite o padrão a ser encontrado. Pressione **Enter** para iniciar a pesquisa. Pressione **N** para localizar a próxima correspondência.

```bash-session
[root@host ~]# vim /var/log/boot.log
...output omitted...
[^[[0;32m  OK  ^[[0m] Finished ^[[0;1;39mdracut pre-pivot and cleanup hook^[[0m.^M
         Starting ^[[0;1;39mCleaning Up and Shutting Down Daemons^[[0m...^M
[^[[0;32m  OK  ^[[0m] Stopped target ^[[0;1;39mRemote Encrypted Volumes^[[0m.^M
[^[[0;32m  OK  ^[[0m] Stopped target ^[[0;1;39mTimer Units^[[0m.^M
[^[[0;32m  OK  ^[[0m] Closed ^[[0;1;39mD-Bus System Message Bus Socket^[[0m.^M
/Daemons
```

```bash-session
[root@host ~]# less /var/log/messages
...output omitted...
Mar  4 03:31:19 localhost kernel: pci 0000:00:02.0: vgaarb: setting as boot VGA device
Mar  4 03:31:19 localhost kernel: pci 0000:00:02.0: vgaarb: VGA device added: decodes=io+mem,owns=io+mem,locks=none
Mar  4 03:31:19 localhost kernel: pci 0000:00:02.0: vgaarb: bridge control possible
Mar  4 03:31:19 localhost kernel: vgaarb: loaded
Mar  4 03:31:19 localhost kernel: SCSI subsystem initialized
Mar  4 03:31:19 localhost kernel: ACPI: bus type USB registered
Mar  4 03:31:19 localhost kernel: usbcore: registered new interface driver usbfs
Mar  4 03:31:19 localhost kernel: usbcore: registered new interface driver hub
Mar  4 03:31:19 localhost kernel: usbcore: registered new device driver usb
/device
```









































