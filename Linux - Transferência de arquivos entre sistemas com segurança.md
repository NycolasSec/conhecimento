Uma sessão com o comando `sftp` usa o mecanismo de autenticação segura e transferência de dados criptografados de e para um servidor SSH. Especifique um local remoto para a origem ou o destino dos arquivos para copiar. Para o formato do local remoto, use `[user@]host:/path`. Quando você executa o comando `sftp`, seu terminal fornece um prompt `sftp>`.

```shell-session
[user@host ~]$ sftp remoteuser@remotehost
```
![[Pasted image 20240909161320.png]]

A sessão interativa `sftp` aceita vários comandos que funcionam no sistema de arquivos remoto da mesma forma como funcionam no sistema de arquivos local, como os comandos `ls`, `cd`, `mkdir`, `rmdir` e `pwd`. O comando `put` faz o upload de um arquivo para o sistema remoto. O comando `get` faz o download de um arquivo para o sistema remoto. O comando `exit` sai da sessão `sftp`.

Liste os comandos `sftp` disponíveis usando o comando `help` na sessão `sftp`:
```shell-session
sftp> help
```
![[Pasted image 20240909161446.png]]

Em uma sessão `sftp`, talvez você queira executar alguns comandos em seu host local. Para a maioria dos comandos disponíveis, adicione o caractere `l` antes do comando.
```shell-session
sftp> pwd
Remote working directory: /home/remoteuser

sftp> lpwd
Local working directory: /home/user
```

O próximo exemplo faz upload do arquivo `/etc/hosts` no sistema local para o diretório recém-criado `/home/remoteuser/hostbackup` na máquina `remotehost`.

A sessão `sftp` espera que o comando `put` seja seguido por um arquivo local e no diretório pessoal do usuário em conexão, neste caso, o diretório `/home/remoteuser`:
```shell-session
sftp> mkdir hostbackup
sftp> cd hostbackup
sftp> put /etc/hosts
```
![[Pasted image 20240909162008.png]]

Para copiar toda uma árvore de diretório recursivamente, use a opção `-r` do comando `sftp`. O exemplo a seguir copia recursivamente o diretório local `/home/user/directory` para a máquina `remotehost`.

```shell-session
sftp> put -r directory
```
![[Pasted image 20240909162105.png]]

```shell-session
sftp> ls -l
```
![[Pasted image 20240909162133.png]]

Para fazer download do arquivo `/etc/yum.conf` do host remoto para o diretório atual no sistema local, execute o comando `get /etc/yum.conf` e saia da sessão `sftp`.
```shell-session
sftp> get /etc/yum.conf
```
![[Pasted image 20240909162235.png]]

```shell-session
sftp> exit
```
![[Pasted image 20240909162329.png]]

Para obter um arquivo remoto com o comando `sftp` em uma única linha de comando, sem abrir uma sessão interativa, use a sintaxe a seguir. Você não pode usar sintaxe de linha de comando única para colocar arquivos em um host remoto.

```shell-session
[user@host ~]$ sftp remoteuser@remotehost:/home/remoteuser/remotefile
```
![[Pasted image 20240909162418.png]]

### Transferir arquivos com protocolo de cópia segura
O comando de cópia segura, `scp`, que também é parte do conjunto `OpenSSH`, copia arquivos de um sistema remoto para o sistema local ou do sistema local para um sistema remoto. Desde o RHEL 9, o comando `scp` usa o protocolo `sftp` para transferir arquivos.

Você pode especificar um local remoto para a origem ou o destino dos arquivos que está copiando. Assim como com o comando `sftp`, o comando `scp` usa `[user@]host` para identificar o sistema de destino e o nome do usuário. Se você não especificar um usuário, o comando tentará fazer login com seu nome de usuário local como o nome de usuário remoto.

Para reverter para o protocolo `scp` legado, você pode incluir o sinalizador `-O`. Devido à vulnerabilidade de injeção de código com o protocolo `scp`, a Red Hat recomenda que você não use o sinalizador `-O` ao usar o comando `scp`.

















































