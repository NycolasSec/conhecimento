O Podman fornece um conjunto de subcomandos para criar e gerenciar contêineres. Você pode usar esses subcomandos para gerenciar o contêiner e o ciclo de vida da imagem de contêiner.

A seguinte figura exibe os subcomandos do Podman mais usados para alterar o estado do contêiner e da imagem:

![[Pasted image 20240913111536.png]]

O Podman também fornece um conjunto de subcomandos para obter informações sobre contêineres em execução e interrompidos.

Você pode usar esses subcomandos para extrair informações de contêineres e imagens para fins de depuração, atualização ou relatórios. A seguinte figura exibe os subcomandos mais usados para consultar informações dos contêineres e das imagens:

![[Pasted image 20240913111639.png]]

Esta aula aborda as operações básicas que você pode usar para gerenciar contêineres. Os comandos explicados nesta aula aceitam uma ID de contêiner ou o nome do contêiner.

### Lista de contêineres
Você pode listar os contêineres em execução com o comando `podman ps`.

```shell-session
[user@host ~]$ podman ps
```
![[Pasted image 20240913111837.png]]

Cada linha descreve informações sobre o contêiner, como a imagem usada para iniciar o contêiner, o comando executado quando o contêiner foi iniciado e o tempo de atividade do contêiner.

Você pode incluir contêineres interrompidos na saída adicionando o sinalizador `--all` ou `-a` ao comando `podman ps`.

```shell-session
[user@host ~]$ podman ps --all
```
![[Pasted image 20240913111933.png]]

### Inspeção de contêineres
O comando `podman inspect` retorna um array JSON com informações sobre diferentes aspectos do contêiner, como configurações de rede, uso da CPU, variáveis de ambiente, status, mapeamento de portas ou volumes.

```shell-session
[user@host ~]$ podman inspect 7763097d11ab
```
![[Pasted image 20240913112120.png]]

A saída JSON anterior omite a maioria dos campos. Como a saída do JSON completa é longa, talvez seja difícil encontrar os campos específicos que você deseja recuperar. Você pode passar um sinalizador `--format` para o comando `podman inspect` para filtrar a saída do comando. O sinalizador `--format` espera uma expressão de template Go como parâmetro, que você pode usar para selecionar campos específicos na saída do JSON.

As expressões do template Go usam anotações, que são delimitadas por chaves duplas (`{{` e `}}`), para se referir a elementos, como campos ou chaves, em uma estrutura de dados. Os elementos da estrutura de dados são separados por um ponto (`.`) e devem iniciar em letras maiúsculas.

No comando a seguir, o campo `Status` do objeto `State` é recuperado para um contêiner chamado `redhat`.

```shell-session
[user@host ~]$ podman inspect \
 --format='{{.State.Status}}' redhat
```
![[Pasted image 20240913112239.png]]

No exemplo anterior, o comando `podman inspect` usa o template Go fornecido para navegar na matriz JSON do contêiner `redhat`. O template retorna o valor do campo `Status` do objeto `State` a partir da matriz JSON.

Consulte a seção de referências para obter mais informações sobre a linguagem de templates Go.

### Interrupção normal de contêineres
Você pode interromper um contêiner normalmente usando o comando `podman stop`. Quando você executa o comando `podman stop`, o Podman envia um sinal `SIGTERM` ao contêiner. Os processos usam o sinal `SIGTERM` para implementar procedimentos de limpeza antes de serem interrompidos.

O comando a seguir interrompe um contêiner com uma ID de contêiner `1b982aeb75dd`.
```shell-sessiin
[user@host ~]$ podman stop 1b982aeb75dd
```
![[Pasted image 20240913112616.png]]

Você pode interromper todos os contêineres em execução usando os sinalizadores `--all` ou `-a`.
```shell-session
[user@host ~]$ podman stop --all
```
![[Pasted image 20240913112727.png]]

Se um contêiner não responder ao sinal `SIGTERM`, o Podman enviará um sinal `SIGKILL` para forçar a interrupção do contêiner. Por padrão, o Podman aguarda 10 segundos antes de enviar o sinal `SIGKILL`. Você pode alterar o comportamento padrão usando o sinalizador `--time`.

```shell-session
[user@host ~]$ podman stop --time=100
```

Interromper um contêiner é diferente da remoção de um. Os contêineres interrompidos estão presentes na máquina de host e podem ser listados usando o comando `podman ps --all`.
### Interrupção forçada de contêineres
Você pode enviar diretamente o sinal `SIGKILL` para o contêiner usando o comando `podman kill`. No exemplo a seguir, um contêiner chamado `httpd` é interrompido forçadamente.

```shell-session
[user@host ~]$ podman kill httpd
```
![[Pasted image 20240913113116.png]]

### Pausa de contêineres

Tanto o comando `podman stop` quanto o `podman kill` posteriormente enviam um sinal `SIGKILL` para o contêiner. O comando `podman pause` suspende todos os processos no contêiner enviando o sinal `SIGSTOP`.

```shell-session
[user@host ~]$ podman pause 4f2038c05b8c
```
![[Pasted image 20240913113204.png]]

O comando `podman unpause` retoma um contêiner pausado.
```shell-session
[user@host ~]$ podman unpause 4f2038c05b8c
```
![[Pasted image 20240913113229.png]]

### Reinicialização de contêineres
Execute o comando `podman restart` para reiniciar um contêiner em execução. Você também pode usar o comando para iniciar os contêineres interrompidos.

O comando a seguir reinicia um contêiner chamado `nginx`.
```shell-session
[user@host ~]$ podman restart nginx
```
![[Pasted image 20240913113344.png]]

### Remoção de contêineres
Use o comando `podman rm` para remover um contêiner interrompido. O comando a seguir remove um contêiner interrompido com a ID de contêiner `c58cfd4b90df`.

```shell-session
[user@host ~]$ podman rm c58cfd4b90df
```
![[Pasted image 20240913113558.png]]

Por padrão, você não pode remover contêineres em execução. Primeiro, você deve interromper o contêiner em execução para depois removê-lo. O comando a seguir tenta remover um contêiner em execução.
```shell-session
[user@host ~]$ podman rm c58cfd4b90df
```
![[Pasted image 20240913113656.png]]

Você pode adicionar o sinalizador `--force` (ou `-f`) para remover o contêiner à força.
```shell-session
[user@host ~]$ podman rm c58cfd4b90df --force
```
![[Pasted image 20240913113722.png]]

Você também pode adicionar o sinalizador `--all` (ou `-a`) para remover todos os contêineres interrompidos. Esse sinalizador falha ao remover contêineres em execução. O comando a seguir tenta remover dois contêineres.
```shell-ession
[user@host ~]$ podman rm --all
```
![[Pasted image 20240913113759.png]]

Você pode combinar os sinalizadores `--force` e `--all` para remover todos os contêineres, incluindo os contêineres em execução.

### Armazenamento persistente de contêineres

Por padrão, os dados gravados em um contêiner são efêmeros e se perdem quando o contêiner é removido. Para garantir a persistência de dados, você pode usar volumes montados do sistema de arquivos do host com a opção `--volume (-v)`. 

Ao montar um diretório do host no contêiner, é importante considerar as permissões de sistema de arquivos. Por exemplo, no contêiner MariaDB, o diretório `/var/lib/mysql` deve ser de propriedade do usuário `mysql`, tanto no host quanto dentro do contêiner. Se o contêiner for executado como usuário root, as UIDs e GIDs no host e no contêiner devem corresponder.

Em contêineres sem raiz, o usuário dentro do contêiner tem acesso root, pois o Podman isola o contêiner no namespace do usuário. Nesse caso, a correspondência de UID e GID entre o host e o contêiner não é tão direta.

Você pode usar o comando `podman unshare` para executar um comando dentro do namespace do usuário. Para obter o mapeamento de UID para seu namespace de usuário, use o comando `podman unshare cat`.

```shell-session
[user@host ~]$ podman unshare cat /proc/self/uid_map
```
![[Pasted image 20240913114547.png]]

```shell-session
[user@host ~]$ podman unshare cat /proc/self/gid_map
```
![[Pasted image 20240913114605.png]]

A saída anterior mostra que, no contêiner, o usuário root (UID e GID de 0) é mapeado para seu usuário (UID e GID de `1000`) na máquina host. No contêiner, a UID e a GID de 1 são mapeadas para a UID e a GID de 100000 na máquina host. Cada UID e GID após 1 é incrementada em 1. Por exemplo, a UID e a GID de 30 dentro de um contêiner são mapeadas para a UID e a GID de 100029 na máquina host.

Você usa o comando `podman exec` para visualizar a UID e a GID do usuário `mysql` dentro do contêiner que em execução com armazenamento efêmero.

```shell-session
[user@host ~]$ podman exec -it db01 grep mysql /etc/passwd
```
![[Pasted image 20240913114754.png]]

Você decide montar o diretório `/home/user/db_data` no contêiner `db01` para fornecer armazenamento persistente no diretório `/var/lib/mysql` do contêiner. Em seguida, você cria o diretório `/home/user/db_data` e usa o comando `podman unshare` para definir a UID do namespace do usuário e a GID de `27` como a proprietária do diretório.

```shell-session
[user@host ~]$ mkdir /home/user/db_data

[user@host ~]$ podman unshare chown 27:27 /home/user/db_data
```

A UID e a GID de `27` no contêiner são mapeadas para a UID e a GID de `100026` na máquina host. Você pode verificar o mapeamento exibindo a propriedade do diretório `/home/user/db_data` com o comando `ls`.

```shell-session
[user@host ~]$ ls -l /home/user/
```
![[Pasted image 20240913114914.png]]

Agora que as permissões corretas no nível do sistema de arquivos estão definidas, você usa a opção `-v` do comando `podman run` para montar o diretório.

```shell-session
[user@host ~]$ podman run -d --name db01 \
-e MYSQL_USER=student \
-e MYSQL_PASSWORD=student \
-e MYSQL_DATABASE=dev_data \
-e MYSQL_ROOT_PASSWORD=redhat \
-v /home/user/db_data:/var/lib/mysql \
registry.lab.example.com/rhel8/mariadb-105
```

Você observa que o contêiner `db01` não está em execução.

```shel-session
[user@host ~]$ podman ps -a
```
![[Pasted image 20240913115013.png]]

O comando `podman container logs` mostra um erro de permissão para o diretório `/var/lib/mysql/db_data`.

```shell-session
[user@host ~]$ podman container logs db01
```
![[Pasted image 20240913115034.png]]

Esse erro ocorre devido ao contexto SELinux incorreto, que está definido no diretório `/home/user/db_data` na máquina host.

#### Contextos SELinux para armazenamento de contêineres
Você deve definir o tipo de contexto SELinux `container_file_t` antes de montar o diretório como armazenamento persistente para um contêiner. Se o diretório não tiver o contexto SELinux `container_file_t`, o contêiner não poderá acessar o diretório. Você pode anexar a opção `Z` ao argumento da opção `-v` do comando `podman run` para definir automaticamente o contexto SELinux no diretório.


Portanto, você usa o comando `podman run -v /home/user/db_data:/var/lib/mysql:Z` para definir o contexto SELinux para o diretório `/home/user/db_data` ao montá-lo como armazenamento persistente para o diretório `/var/lib/mysql`.

```shell-session
[user@host ~]$ podman run -d --name db01 \
-e MYSQL_USER=student \
-e MYSQL_PASSWORD=student \
-e MYSQL_DATABASE=dev_data \
-e MYSQL_ROOT_PASSWORD=redhat \
-v /home/user/db_data:/var/lib/mysql:Z \
registry.lab.example.com/rhel8/mariadb-105
```

Em seguida, você verifica se o contexto SELinux correto está definido no diretório `/home/user/db_data` com a opção `-Z` do comando `ls`.

```shell-session
[user@host ~]$ ls -Z /home/user/
```
![[Pasted image 20240913115815.png]]

### Iniciar um serviço conteinerizado no boot
Em um ambiente tradicional, aplicativos como servidores web e bancos de dados são configurados para iniciar automaticamente durante o boot e funcionar continuamente como serviços ``systemd``. O ``systemd`` é uma ferramenta para gerenciar sistemas e serviços em Linux, utilizando arquivos de unidade de serviço para controlar o início e a parada dos aplicativos.

Administradores geralmente usam o comando `systemctl` para gerenciar esses serviços. Usuários comuns podem também criar arquivos de unidade systemd para gerenciar contêineres sem privilégios de root, permitindo que esses contêineres sejam tratados como serviços normais com `systemctl`.
#### Gerar um arquivo de unidade systemd
Para criar um arquivo de unidade ``systemd`` para um contêiner de serviço especificado, use o comando `podman generate systemd`. Use a opção `--name` para especificar o nome do contêiner. A opção `--files` gera arquivos em vez de imprimir na saída padrão (STDOUT).

```shell-session
[user@host ~]$ podman generate systemd --name web --files
```
![[Pasted image 20240913130827.png]]

O comando anterior cria o arquivo de unidade `container-web.service` para um contêiner chamado `web`. Depois de revisar e adaptar a configuração do serviço gerado de acordo com seus requisitos, armazene o arquivo de unidade no diretório de configuração ``systemd`` do usuário (`~/.config/systemd/user/`).

Depois de adicionar ou modificar os arquivos de unidade, você deve usar o comando `systemctl` para recarregar a configuração do systemd.

```shell-session
[user@host ~]$ systemctl --user daemon-reload
```

#### Gerenciamento do serviço conteinerizado
Para gerenciar um serviço conteinerizado, use o `systemctl command`.

```shell-session
[user@host ~]$ systemctl --user [start, stop, status, enable, disable] container-web.service
```
Quando você usa a opção `--user`, por padrão, o systemd inicia o serviço quando você faz login e o interrompe quando você sai. Você pode iniciar seus serviços habilitados no boot do sistema operacional e interrompê-los no desligamento executando o comando `loginctl enable-linger`.

```shell-session
[user@host ~]$ loginctl enable-linger
```
Para reverter a operação, use o comando `loginctl disable-linger`.


















