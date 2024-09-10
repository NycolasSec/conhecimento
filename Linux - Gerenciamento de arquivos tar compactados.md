## Gerenciamento de arquivos tar compactados

Um ``arquivo`` pode ser um único arquivo regular ou um arquivo de dispositivo que contém múltiplos arquivos. Arquivos de dispositivo incluem unidades de fita, unidades flash ou outras mídias removíveis. O arquivamento de arquivos regulares é semelhante ao uso de utilitários de compressão como o ``zip``, comuns em diversos sistemas operacionais.

Arquivos são usados para criar backups gerenciáveis ou facilitar a transferência de múltiplos arquivos em uma rede, especialmente quando métodos como ``rsync`` não estão disponíveis ou são complexos. Eles podem ser criados com ou sem compactação para reduzir o tamanho.

No Linux, o comando `tar` é utilizado para criar, gerenciar e extrair arquivos, reunindo vários arquivos em um único arquivo com um índice para extração seletiva. O `tar` também suporta compactação com algoritmos compatíveis e pode listar ou extrair o conteúdo de arquivos compactados.

#### Opções do utilitário tar
Uma das seguintes ações do comando `tar` é necessária para realizar uma operação `tar` **:**
- **`-c`** ou **`--create`** : criar um arquivo.
- **`-t`** ou **`--list`** : listar o conteúdo de um arquivo.
- **`-x`** ou **`--extract`** : extrair um arquivo.

As seguintes opções gerais do comando `tar` são normalmente incluídas **:**
- **`-v`** ou **`--verbose`** : mostre os arquivos sendo arquivados ou extraídos durante a operação `tar`.
- **`-f`** ou **`--file`** : siga essa opção com o nome do arquivo a ser criado ou aberto.
- **`-p`** ou **`--preserve-permissions`** : preserve as permissões do arquivo original ao extrair.
- **`--xattrs`** : habilite o suporte a atributos estendidos e armazene atributos de arquivos estendidos.
- **`--selinux`** : habilite o suporte ao contexto ``SELinux`` e armazene contextos de arquivo do ``SELinux``.

As seguintes opções de compactação do comando `tar` são usadas para selecionar um algoritmo **:**
- **`-a`** ou **`--auto-compress`** : use o sufixo do arquivo para determinar o algoritmo a ser usado.
- **`-z`** ou **`--gzip`** : use o algoritmo de compactação `gzip`, resultando em um sufixo `.tar.gz`.
- **`-j`** ou **`--bzip2`** : use o algoritmo de compactação `bzip2`, resultando em um sufixo `.tar.bz2`.
- **`-J`** ou **`--xz`** : use o algoritmo de compactação `xz`, resultando em um sufixo `.tar.xz`.

### Criar um arquivo
Para criar um arquivo com o comando `tar`, use as opções "**create**" e "**file**" com o nome do arquivo como o primeiro argumento, seguido por uma lista de arquivos e diretórios a serem incluídos no arquivo.

O comando `tar` lida com nomes de arquivos absolutos e relativos. Por padrão, ele remove as barras (**/**) dos nomes de arquivos absolutos, armazenando-os como nomes de caminho relativos. Isso evita a substituição de arquivos existentes durante a extração, pois permite extrair os arquivos para um diretório diferente sem sobrescrever os arquivos já presentes.

O comando a seguir cria o arquivo `mybackup.tar` para conter os arquivos `myapp1.log`, `myapp2.log` e `myapp2.log` no diretório pessoal do usuário. Se um arquivo com o mesmo nome do solicitado existir no diretório de destino, o comando `tar` substitui o arquivo.

```shell-session
[user@host ~]$ tar -cf mybackup.tar myapp1.log myapp2.log myapp3.log

[user@host ~]$ ls mybackup.tar
mybackup.tar
```

Para criar um arquivo com o comando `tar`, o usuário precisa de permissões de leitura nos arquivos de destino. Por exemplo, para arquivar o diretório `/etc`, são necessários privilégios de root, pois apenas usuários com privilégios podem ler todos os arquivos e diretórios dentro de `/etc`.

Usuários sem privilégios podem criar um arquivo compactado do diretório `/etc`, mas o arquivo resultante excluirá arquivos e diretórios para os quais o usuário não tem permissões de leitura e execução.

Neste exemplo, o usuário `root` cria o arquivo `/root/etc-backup.tar` do diretório `/etc`.

```shell-session
[root@host ~]# tar -cf /root/etc-backup.tar /etc
tar: Removing leading `/' from member names
```

#### Listar conteúdo do arquivo
Use a opção `t` do comando `tar` para listar os nomes dos arquivos de dentro do arquivo especificado com a opção `f`. Os arquivos são listados com sintaxe de nome relativo, pois a barra inicial foi removida durante a criação do arquivo.

```shell-session
[root@host ~]# tar -tf /root/etc.tar
```
![[Pasted image 20240909153919.png]]

#### Extrair conteúdo do arquivo
Extraia um arquivo tar em um diretório vazio para evitar a substituição de arquivos existentes. Quando o usuário `root` extrai um arquivo, os arquivos extraídos preservam o usuário original e a propriedade do grupo. Se um usuário regular extrair arquivos, ele se tornará o proprietário dos arquivos extraídos.

Liste o conteúdo do arquivo `/root/etc.tar` e extraia seus arquivos para o diretório `/root/etcbackup`:

```shell-session
[root@host ~]# mkdir /root/etcbackup
[root@host ~]# cd /root/etcbackup
[root@host etcbackup]# tar -tf /root/etc.tar
```
![[Pasted image 20240909154125.png]]

```shell-session
[root@host etcbackup]# tar -xf /root/etc.tar
```

Ao extrair arquivos de um arquivo ``tar``, o ``umask`` atual é usado para ajustar as permissões dos arquivos extraídos. Para preservar as permissões originais dos arquivos, utilize a opção `-p` no comando `tar`. A opção `--preserve-permissions` está ativada por padrão para superusuários.

```shell-session
[user@host scripts]# tar -xpf /home/user/myscripts.tar
```

### Criar um arquivo compactado
O comando `tar` é compatível com estes métodos de compactação, entre outros:

- A compactação **`gzip`** é o método mais rápido e antigo, além de estar amplamente disponível em todas as plataformas.
- A compactação **`bzip2`** cria arquivos menores, mas é menos amplamente disponível do que **`gzip`**.
- A compactação **`xz`** é mais recente e oferece a melhor taxa de compactação dos métodos disponíveis.

A eficácia de qualquer algoritmo de compactação depende do tipo dos dados a serem compactados. Arquivos de dados anteriormente compactados, como formatos de imagem ou arquivos RPM, normalmente não são compactados de modo significativo.

Crie o arquivo `/root/etcbackup.tar.gz` com a compactação `gzip` a partir do conteúdo do diretório `/etc`.
```shell-session
[root@host ~]# tar -czf /root/etcbackup.tar.gz /etc
```
![[Pasted image 20240909155519.png]]

Crie o arquivo `/root/logbackup.tar.bz2` com a compactação `bzip2` a partir do conteúdo do diretório `/var/log`.
```shell-session
[root@host ~]$ tar -cjf /root/logbackup.tar.bz2 /var/log
```
![[Pasted image 20240909155653.png]]

Crie o arquivo `/root/sshconfig.tar.xz` com a compactação `xz` a partir do conteúdo do diretório `/﻿etc/ssh`.
```shell-session
[root@host ~]$ tar -cJf /root/sshconfig.tar.xz /etc/ssh
```
![[Pasted image 20240909155756.png]]

Depois de criar um arquivo, verifique seu índice com as opções `tf` do comando `tar`. Liste o conteúdo arquivado no arquivo `/root/etcbackup.tar.gz`, que usa a compactação `gzip`:
```shell-sesion
[root@host ~]# tar -tf /root/etcbackup.tar.gz
```
![[Pasted image 20240909160003.png]]

#### Extrair conteúdo do arquivo compactado
O comando `tar` pode detectar automaticamente o tipo de compactação usada em um arquivo, eliminando a necessidade de especificar manualmente a opção correta.

Se um tipo de compactação incorreto for fornecido, o `tar` avisará sobre a incompatibilidade entre o tipo especificado e o arquivo. Por exemplo, se o arquivo tiver a extensão `.xz` (compactação ``xz``), mas a opção `-z` (``gzip``) for usada, o `tar` reportará o erro.
```shell-session
[root@host ~]# tar -xzf /root/etcbackup.tar.xz
```
![[Pasted image 20240909160058.png]]

Listar um arquivo compactado `tar` funciona da mesma forma que listar um arquivo `tar` sem compactação. Use o comando `tar` com a opção `tf` para verificar o conteúdo do arquivo compactado antes de extrair seu conteúdo:

```shell-session
[root@host logbackup]# tar -tf /root/logbackup.tar
```
![[Pasted image 20240909160348.png]]

Os algoritmos `gzip`, `bzip2` e `xz` podem ser usados como comandos autônomos para compactar arquivos individualmente, mas não permitem criar arquivos compactados de múltiplos arquivos ou diretórios. Para isso, é necessário utilizar o comando `tar` junto com uma opção de compactação.

Para descompactar um arquivo ou verificar o tamanho descompactado antes de extrair, você pode usar os comandos `gunzip`, `bunzip2` e `unxz`, além da opção `-l` nos comandos `gzip` e `xz` para ver o tamanho descompactado.

```shell-session
[user@host ~]$ gzip -l file.tar.gz
```
![[Pasted image 20240909160700.png]]

```shell-session
[user@host ~]$ xz -l file.xz
```
![[Pasted image 20240909160724.png]]



























































































































































