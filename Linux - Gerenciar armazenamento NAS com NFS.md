O **NFS (Network File System)** é um protocolo padrão da internet utilizado por Linux, UNIX e outros sistemas semelhantes para sistemas de arquivos de rede. O **Red Hat Enterprise Linux 9** usa por padrão a versão **4.2** do NFS, sendo compatível também com **NFSv3** e **NFSv4**. O **NFSv3** pode usar TCP ou UDP, enquanto o **NFSv4** permite apenas conexões TCP.

Os servidores NFS _exportam_ diretórios. Os clientes NFS montam diretórios exportados em um diretório de ponto de montagem local existente. Os clientes NFS podem montar diretórios exportados de várias maneiras:

- **Manualmente**, usando o comando `mount`.
- **De modo persistente no boot**, configurando entradas no arquivo `/etc/fstab`.
- **Sob demanda**, configurando um método de montador automático.

Você deve instalar o pacote `nfs-utils` para obter as ferramentas do cliente para montagem manual, ou para montagem automática, para obter diretórios NFS exportados.
```shell-session
[root@host ~]# dnf install nfs-utils
```

As opções de montagem são específicas ao protocolo e dependem da configuração do servidor Windows ou do servidor Samba.

### Consulta aos diretórios NFS exportados de um servidor
O método para consultar um servidor para visualizar as exportações disponíveis é diferente para cada versão do protocolo.

O NFSv3 usava o protocolo RPC. Um cliente NFSv3 se conecta ao serviço `rpcbind` na porta 111. O servidor responde com a porta atual para o serviço NFS. Use o comando `showmount` para consultar as exportações disponíveis em um servidor NFSv3 baseado em RPC.

```shell-sesion
[root@host ~]# showmount --exports server
```
![[Pasted image 20240830164712.png]]

O protocolo NFSv4 eliminou o uso do protocolo RPC legado para transações NFS. O uso do comando `showmount` em um servidor compatível apenas com NFSv4 expira sem receber uma resposta, pois o serviço `rpcbind` não está em execução no servidor. No entanto, consultar um servidor NFSv4 é mais simples do que consultar um servidor NFSv3.

O **NFSv4** introduziu uma árvore de exportação que organiza todos os diretórios exportados do servidor. Para visualizar esses diretórios, é necessário montar a raiz (/) da árvore de exportação do servidor. Isso permite navegar pelos caminhos dos diretórios exportados, que aparecem como filhos do diretório raiz, mas não monta efetivamente nenhum dos diretórios exportados.
```shell-session
[root@host ~]# mkdir /mountpoint
[root@host ~]# mount server:/ /mountpoint
[root@host ~]# ls /mountpoint
```

Para montar uma exportação NFSv4 e navegar pela árvore de exportação, você pode alterar o diretório para um caminho exportado ou usar o comando `mount` com o caminho completo de um diretório exportado.

No entanto, diretórios exportados com segurança Kerberos não permitem montagem ou acesso ao navegar na árvore, apenas a visualização do caminho. Montar compartilhamentos protegidos por Kerberos exige configuração adicional no servidor e credenciais de usuário do Kerberos.

### Montar manualmente diretórios NFS exportados
Depois de identificar a exportação NFS a ser montada, crie um ponto de montagem local se ele ainda não existir. O diretório `/﻿mnt` está disponível para uso como um ponto de montagem temporário, mas a prática recomendada é não usar `/mnt` para montagem de longo prazo ou persistente.

```shell-session
[root@host ~]# mkdir /mountpoint
```

Assim como nos sistemas de arquivos de volume local, monte a exportação NFS para acessar seu conteúdo. Os compartilhamentos NFS podem ser montados de forma temporária ou permanente, apenas por um usuário com privilégios.
```shell-session
[root@host ~]# mount -t nfs -o rw,sync server:/export /mountpoint
```

A opção `-t nfs` especifica o tipo de sistema de arquivos NFS. No entanto, quando o comando `mount` detecta a sintaxe _server:/export_, o comando usa como padrão o tipo NFS. Com o sinalizador `-o`, você pode adicionar uma lista de opções separadas por vírgulas ao comando `mount`. No exemplo, a opção `rw` especifica que o sistema de arquivos exportado é montado com acesso de leitura/gravação. A opção `sync` especifica transações síncronas para o sistema de arquivos exportado. Esse método é altamente recomendado para todas as montagens de rede de produção em que as transações devem ser concluídas ou então retornadas como com falha.

O uso do comando `mount` manual não é persistente. Quando o sistema for reinicializado, a exportação NFS ainda não será montada. Montagens manuais são úteis para fornecer acesso temporário a um diretório exportado ou para testar a montagem de uma exportação NFS antes de montá-la de modo persistente.

### Montar de modo persistente diretórios NFS exportados

Para montar de modo persistente uma exportação NFS, edite o arquivo `/etc/fstab` e adicione a entrada de montagem com sintaxe semelhante à montagem manual.
```shell-session
[root@host ~]# vim /etc/fstab
```
![[Pasted image 20240830170515.png]]

Em seguida, é possível montar a exportação NFS usando apenas o ponto de montagem. O comando `mount` obtém o servidor NFS e as opções de montagem da entrada correspondente no arquivo `/etc/fstab`.
```shell-session
[root@host ~]# mount /mountpoint
```

### Desmontar diretórios NFS exportados

Como um usuário com privilégios, desmonte uma exportação NFS com o comando `umount`. As entradas no arquivo `/etc/fstab` são persistentes e remontadas durante o boot.
```shell-session
[root@host ~]# umount /mountpoint
```

Às vezes, ao tentar desmontar um diretório montado, você pode receber um erro indicando que o dispositivo está ocupado. Isso ocorre porque um arquivo ainda está aberto por um aplicativo ou um shell de usuário está usando um diretório dentro do sistema de arquivos montado.

Para resolver o erro de "dispositivo ocupado" ao desmontar um diretório, verifique suas próprias janelas de shell e use `cd` para sair do sistema de arquivos montado. Se ainda não conseguir desmontar, use o comando `lsof` para listar arquivos abertos e identificar quais processos estão mantendo o arquivo aberto.

```shell-session
[root@host ~]# lsof  /mountpoint
```
![[Pasted image 20240830171033.png]]

Com essas informações, feche normalmente todos os processos que estão usando arquivos no sistema de arquivos e tente novamente desmontar. Somente em cenários críticos, quando não for possível fechar normalmente um aplicativo, encerre o processo para fechar o arquivo.

Como alternativa, use a opção `umount -f` para forçar a desmontagem, o que pode causar perda de dados não gravados em todos os arquivos abertos.













