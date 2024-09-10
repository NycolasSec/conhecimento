O comando `rsync` é uma ferramenta eficiente para copiar arquivos entre sistemas, sincronizando apenas as diferenças entre os arquivos em dois servidores. Isso torna o processo de atualização mais rápido após a sincronização inicial.

A opção `-n` permite executar um *dry run*, simulando o processo sem realizar mudanças reais, o que ajuda a evitar erros. A opção `-v` exibe detalhes do progresso, enquanto a opção `-a` ativa o modo de arquivo compactado, copiando recursivamente e preservando as características dos arquivos.

|Opção|Descrição|
|:--|:--|
|`-r`, `--recursive`|Sincronizar toda a árvore de diretório recursivamente|
|`-l`, `--links`|Sincronizar links simbólicos|
|`-p`, `--perms`|Preservar permissões|
|`-t`, `--times`|Preservar carimbos de data e hora|
|`-g`, `--group`|Preservar propriedade de grupo|
|`-o`, `--owner`|Preservar o proprietário dos arquivos|
|`-D`, `--devices`|Preservar arquivos do dispositivo|

O modo de arquivo compactado não preserva links físicos, porque isso pode adicionar um tempo significativo à sincronização. Use a opção `-H` do comando `rsync` para preservar também os links físicos.

>[!NOTE] Para incluir atributos estendidos ao sincronizar arquivos, adicione estas opções ao comando rsync:
>
>- **-A** para preservar listas de controle de acesso (ACLs)
>
>- **-X** para preservar os contextos de arquivo do SELinux

Você também pode sincronizar o conteúdo de dois arquivos ou diretórios locais na mesma máquina.

Como o comando `sftp`, o comando `rsync` especifica locais remotos no formato `[user@]host:/path`. O local remoto pode ser o sistema de origem ou de destino, mas uma das duas máquinas deve ser local.

Para preservar a propriedade do arquivo, você deve ser o usuário `root` no sistema de destino. Se o destino for remoto, autentique como o usuário `root`. Se o destino for local, você deverá executar o comando `rsync` como o usuário `root` .

Neste exemplo, sincronize o diretório local `/var/log` com o diretório `/tmp` no sistema `hosta`:
```shell-session
[root@host ~]# rsync -av /var/log hosta:/tmp
```
![[Pasted image 20240909163922.png]]

Da mesma forma, o diretório remoto `/var/log` na máquina `hosta` é sincronizado com o diretório `/tmp` na máquina `host`:
```shell-session
[root@host ~]# rsync -av hosta:/var/log /tmp
```
![[Pasted image 20240909163957.png]]

O seguinte exemplo sincroniza o conteúdo do diretório `/var/log` com o diretório `/tmp` na mesma máquina:
```shell-session
[root@host ~]# rsync -av /var/log /tmp
```
![[Pasted image 20240909164134.png]]

























