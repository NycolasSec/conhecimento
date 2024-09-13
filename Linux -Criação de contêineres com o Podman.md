### Uma introdução ao Podman
O Podman é uma ferramenta open source para gerenciar contêineres localmente, permitindo encontrar, executar, compilar e implantar contêineres e imagens OCI.

Ao contrário de outras ferramentas que utilizam um ``daemon`` para gerenciamento, o Podman opera sem um ``daemon``, evitando um ponto único de falha e a necessidade de privilégios elevados.

O Podman oferece uma interface de linha de comando (CLI) compatível com vários sistemas operacionais, e também fornece uma API RESTful e um aplicativo de desktop, o Podman Desktop, para interações e automações adicionais.

### Trabalho com o Podman
Depois de instalar o Podman, você pode usá-lo executando o comando `podman`. O comando a seguir exibe a versão que você está usando.
```shell-session
[user@host ~]$ podman -v
```
![[Pasted image 20240913103752.png]]

#### Pull e exibição de imagens
Antes de executar seu aplicativo em um contêiner, é necessário criar uma imagem de contêiner.

Com o Podman, você busca imagens de contêiner de registros de imagem usando o comando `podman pull`. Por exemplo, o comando a seguir busca uma versão conteinerizada do Red Hat Enterprise Linux 9 no Red Hat Registry.

```shell-session
[user@host ~]$ podman pull registry.redhat.io/rhel9/rhel-guest-image:9.4
```
![[Pasted image 20240913103950.png]]

A imagem em contêiner do RHEL 9 é referenciada no formato `NAME:VERSION`. No exemplo a seguir, você busca a versão `9.4` da imagem `registry.redhat.io/rhel9/rhel-guest-image`.

Após a execução do comando `pull`, a imagem é armazenada localmente em seu sistema. Você pode listar as imagens no seu sistema usando o comando `podman images`.

```shell-session
[user@host ~]$ podman images
```
![[Pasted image 20240913104112.png]]

#### Contêiner e imagens de contêiner
Um contêiner é um ambiente isolado para executar aplicativos como processos separados, garantindo que não interfiram com outros contêineres ou processos do sistema.

Uma imagem de contêiner é um pacote que inclui o aplicativo e suas dependências necessárias para a execução. Embora imagens possam existir sem contêineres, contêineres dependem de imagens para criar o ambiente de tempo de execução necessário para rodar os aplicativos.

#### Execução e exibição de contêineres
Com a imagem armazenada localmente, você pode usar o comando `podman run` para criar um novo contêiner a partir dela. Se a imagem for do RHEL, por exemplo, você pode fornecer comandos Bash como argumento, que serão executados dentro do contêiner do RHEL.

```shell-session
[user@host ~]$ podman run registry.redhat.io/rhel9/rhel-guest-image:9.4 \
echo 'Red Hat'
```
![[Pasted image 20240913104534.png]]

Quando o contêiner conclui a execução do comando `echo`, ele é interrompido porque nenhum outro processo o mantém em execução. Você pode listar os contêineres em execução usando o comando `podman ps`.

```shell-session
[user@host ~]$ podman ps
```
![[Pasted image 20240913104731.png]]

Por padrão, o comando `podman ps` lista os detalhes a seguir para seus contêineres.

- A ID do contêiner
- O nome da imagem que o contêiner está usando
- O comando que o contêiner está executando
- A hora em que o contêiner foi criado
- O status do contêiner
- As portas expostas no contêiner
- O nome do contêiner

No entanto, _interromper_ um contêiner não é o mesmo que _remover_ um contêiner. Embora o contêiner esteja interrompido, o Podman não o remove.

Você pode listar todos os contêineres (em execução e interrompidos) adicionando o sinalizador `--all` ao comando `podman ps`.
```shell-session
[user@host ~]$ podman ps --all
```
![[Pasted image 20240913104907.png]]

Também é possível remover automaticamente um contêiner na saída, adicionando a opção `--rm` ao comando `podman run`.
```shell-session
[user@host ~]$ podman run --rm registry.redhat.io/rhel9/rhel-guest-image:9.4\
echo 'Red Hat'
```
![[Pasted image 20240913105015.png]]

```shell-session
[user@host ~]$ podman ps --all
```
![[Pasted image 20240913105044.png]]

Se um nome não for fornecido para o contêiner durante a criação, o Podman gerará um nome de string aleatório para o contêiner.

Você pode atribuir um nome aos seus contêineres adicionando o sinalizador `--name` ao comando `podman run`.
```shell-session
[user@host ~]$ podman run --name podman_rhel9 \
registry.redhat.io/rhel9/rhel-guest-image:9.4 echo 'Red Hat'
```
![[Pasted image 20240913105214.png]]

O Podman pode identificar os contêineres pela ID _Identificador Exclusivo Universal (UUID)_ curta, composta por doze caracteres alfanuméricos, ou pela UUID longa, composta por 64 caracteres alfanuméricos, como mostrado no exemplo.

```shell-session
[user@host ~]$ podman ps --all
```
![[Pasted image 20240913105435.png]]

Neste exemplo, `20236410bcef` é a ID do contêiner ou a UUID curta. Além disso, `podman_rhel9` é listado como o nome do contêiner.

Se quiser recuperar a ID de contêiner UUID longa, você pode adicionar o sinalizador `--format=json` ao comando `podman ps --all`.
```shell-session
[user@host ~]$ podman ps --all --format=json
```
![[Pasted image 20240913105508.png]]

![[Pasted image 20240913105537.png]] ID do contêiner UUID longa.
Você pode recuperar informações de um contêiner no formato JSON ou como um template Go.

#### Exposição dos contêineres
Você pode usar a opção `-p` para mapear uma porta em sua máquina local para uma porta dentro do contêiner. Dessa forma, o tráfego na sua porta local é encaminhado para a porta dentro do contêiner, permitindo assim que você acesse o aplicativo de seu computador.

O exemplo a seguir cria um novo contêiner que executa um servidor HTTP Apache mapeando a porta 8080 em sua máquina local para a porta 8080 dentro do contêiner.
```shell-session
[user@host ~]$ podman run -p 8080:8080 \
 registry.access.redhat.com/ubi9/httpd-24:latest
```
![[Pasted image 20240913110410.png]]

Você pode acessar o servidor HTTP em `localhost:8080`.

Se quiser que o contêiner seja executado em segundo plano para evitar o bloqueio do terminal, você pode usar a opção `-d`.
```shell-session
[user@host ~]$ podman run -d -p 8080:8080 \
 registry.access.redhat.com/ubi9/httpd-24:latest
```
![[Pasted image 20240913110452.png]]

#### Uso de variáveis de ambiente
Variáveis de ambiente são usadas para fornecer valores de configuração a aplicativos de fora do próprio programa, sendo definidas pelo sistema operacional ou ambiente de execução.

Elas são acessíveis durante a execução do aplicativo e permitem a injeção de valores específicos do ambiente de maneira segura. Por exemplo, um aplicativo pode usar diferentes nomes de host para o banco de dados dependendo do ambiente, como `database.local`, `database.stage` ou `database.test`.

Você pode passar variáveis de ambiente para um contêiner usando a opção `-e`. No exemplo a seguir, é passada uma variável de ambiente chamada `NAME` com o valor `Red Hat`. Em seguida, a variável de ambiente é impressa usando o comando `printenv` dentro do contêiner.

```shell-session
[user@host ~]$ podman run -e NAME='Red Hat' \
registry.redhat.io/rhel9/rhel-guest-image:9.4 printenv NAME
```
![[Pasted image 20240913110747.png]]

### Podman Desktop
O _Podman Desktop_ é uma interface gráfica de usuário usada para gerenciar e interagir com contêineres em ambientes locais. Ele usa o engine do Podman por padrão e é compatível com outros engines de contêiner também, como o Docker.

Ao iniciar, o Podman Desktop exibe um painel com informações sobre o status do engine do Podman. O painel pode exibir avisos ou erros, por exemplo, se o engine do Podman não estiver instalado ou se a compatibilidade do Docker não estiver totalmente configurada.

![[Pasted image 20240913110831.png]]

Com o Podman Desktop, você pode realizar muitas das tarefas que faria com a CLI `podman`, como pull de imagens e criação de contêineres. Por exemplo, você pode criar, listar e executar contêineres na seção Containers, como mostra a seguinte imagem:

![[Pasted image 20240913110837.png]]

Da mesma forma, você pode fazer pull, listar imagens e criar contêineres a partir dessas imagens na seção Images.

![[Pasted image 20240913110845.png]]

O Podman Desktop é modular e extensível. Você pode usar e criar extensões que forneçam recursos adicionais. Por exemplo, com a extensão do _Red Hat OpenShift_, o Podman Desktop pode implantar contêineres no Red Hat OpenShift Container Platform.

