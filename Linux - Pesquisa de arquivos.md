
## locate
O comando locate procura os arquivos com base na sua base de dados que é atualizada diariamente, mas pode-se forçar a atualização com o comando `ùpdatedb`

```bash
updatedb
locate passwd
```
![[Pasted image 20240729075528.png]]

com a opção `-i` o comando não irá diferenciar minúsculas de maiúsculas.
```bash
locate -i messages
```
![[Pasted image 20240729075644.png]]

A opção `-n` limita o número de resultados de pesquisa.

## find
``find`` localiza arquivos em tempo real, não precisando de uma atualização. O primeiro argumento a ser passado é o diretório onde será feita a pesquisa.

Para pesquisarmos pelo nome do arquivo, usamos a opção `-name` e o nome do arquivo.
```bash
find / -name sshd_config
```

Pode-se usar caracteres curinga.
```bash
find / -name '*.txt'
```

Para não diferenciar maiúsculas de minúscula, se usa a opção `-iname`
```bash
find / -iname sshd_config
```

### Pesquisa pelo proprietário

O comando `find` pesquisa arquivos com base na propriedade ou nas permissões. As opções `-user` e `-group` do comando `find` pesquisam por um nome de usuário e grupo ou por ID de usuário e ID de grupo.

Para procurar arquivos no diretório `/home/developer` que o usuário `developer` possui:
```bash
find -user developer
```
![[Pasted image 20240729080352.png]]

Para pesquisar arquivos no diretório `/home/developer` que o grupo `developer` possui:
```bash
find -group developer
```
![[Pasted image 20240729080419.png]]

Para procurar arquivos no diretório `/home/developer` que a ID de usuário `1000` possui:
```bash
find -uid 1000
```
![[Pasted image 20240729080447.png]]

Para pesquisar arquivos no diretório `/home/developer` que a ID de grupo `1000` possui:
```bash
find -gid 1000
```
![[Pasted image 20240729080507.png]]

O exemplo a seguir lista os arquivos que o usuário `root` possui e com o grupo `mail`:
```bash
find / -user root -group mail
```
![[Pasted image 20240729080550.png]]

### Pesquisa por permissões

A opção `-perm` pesquisa por um conjunto específico de permissões. As permissões são precedidas por um sinal `/` ou `-` para controlar os resultados da pesquisa.

A permissão octal precedida pelo sinal `/` corresponde a arquivos em que pelo menos uma permissão foi definida para o usuário, grupo ou outro para esse conjunto de permissões

Um sinal `-` antes da permissão significa que todas as três partes das permissões devem corresponder.

```bash
find /home -perm 764
```
ou
```bash
find /home -perm u=rwx,g=rw,o=r
```

a opção `-ls` do comando `find` lista informações dos arquivos encontrados, inclusive as permissões.
```bash
find /home -perm 764 -ls
```

### Pesquisa pelo tamanho

A opção `-size` do comando `find` é seguida por um valor numérico, e a unidade procura arquivos que correspondem a um tamanho especificado.

- Para kilobytes, use a unidade `k` com `k` sempre em letras minúsculas.
- Para megabytes, use a unidade `M` com `M` sempre em letras maiúsculas.
- Para gigabytes, use a unidade `G` com `G` sempre em letras maiúsculas.

Você pode usar os caracteres de adição `+` e subtração `-` para incluir arquivos maiores e menores do que o tamanho fornecido, respectivamente.

Arquivos com um tamanho exato de 10 megabytes:
```bash
find -size 10M
```

Para pesquisar os arquivos com tamanho superior a 10 gigabytes:
```bash
find -size +10G
```

Para pesquisar os arquivos com tamanho inferior a 10 kilobytes:
```bash
find -size -10K
```

### Pesquisa com base no horário de modificação.
A opção `-mmin` do comando `find`, seguida pelo tempo em minutos, pesquisa todos os arquivos com conteúdo alterado há `n` minutos.

O carimbo de data e hora do arquivo é arredondado para baixo e é compatível com valores fracionários com o intervalo `+n` e `-n`.

Para pesquisar todos os arquivos com conteúdo alterado há 120 minutos:
```bash
find / -min 120
```

O modificador `+` na frente dos minutos encontra todos os arquivos no diretório `/` que foram alterados há mais de `n`minutos

Para pesquisar todos os arquivos com conteúdo alterado há mais de 200 minutos:
```bash
find / -mmin +200
```

O modificador `-` pesquisa todos os arquivos no diretório `/` que foram alterados há menos de `n` minutos.

O exemplo a seguir lista arquivos que foram alterados há menos de 150 minutos:
```bash
find / -mmin -150
```

### Pesquisa com base no tipo de arquivos
A opção `-type` do comando `find` limita o escopo da pesquisa para um determinado tipo de arquivo.

Use os seguintes sinalizadores para limitar o escopo da pesquisa:

- Para arquivos regulares, use o sinalizador `f`.
- Para diretórios, use o sinalizador `d`.
- Para softlinks, use o sinalizador `l`.
- Para dispositivos de bloco, use o sinalizador `b`.

Pesquise todos os diretórios no diretório /etc:
```bash
find /etc -type d
```

Pesquise todos os softlinks no diretório /:
```bash
find / -type l
```

Pesquise todos os dispositivos de bloco no diretório /dev:
```bash
find /dev -type b
```

### Pesquisa por número de links fisícos
A opção `-links` do comando `find` seguida por um número procura todos os arquivos com uma determinada contagem de links físicos.

Se precedido `+` procura arquivos com a contagem mais alta à contagem de links físicos fornecida. Se o número for precedido por `-`, a pesquisa será limitada aos arquivos com uma contagem de links físicos inferior ao número fornecido.

Pesquise todos os arquivos regulares com mais de um link físico:
```bash
find / -type f -links +1
```
