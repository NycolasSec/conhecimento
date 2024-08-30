## Discos de partição
O particionamento de disco divide um disco rígido em várias _partições_ de armazenamento lógico. Você pode usar partições para dividir o armazenamento com base em diferentes requisitos, e essa divisão oferece muitos benefícios:

- Limitar o espaço disponível para aplicativos ou usuários.
- Separar o sistema operacional e os arquivos de programas dos arquivos do usuário.
- Criar uma área separada para swap da memória.
- Limitar o uso do espaço em disco para melhorar o desempenho de ferramentas de diagnóstico e de imagens de backup.
### Esquema de particionamento MBR
O esquema de particionamento de _registro mestre de boot_ (MBR, Master Boot Record) é o padrão em sistemas que executam firmware BIOS. Esse esquema suporta um máximo de quatro partições primárias. Em sistemas Linux, com partições estendidas e lógicas, você pode criar até 15 partições. Com um tamanho de partição de 32 bits, os discos que são particionados com MBR podem ter um tamanho de até 2 TiB.

![[Pasted image 20240830150614.png]]

O limite de tamanho de disco e partição de 2 TiB é agora uma limitação comum e restritiva. Consequentemente, o esquema de MBR herdado é substituído pelo esquema de particionamento de _tabela de partição GUID (GPT, GUID Partition Table )_.

### Esquema de particionamento GPT
Para sistemas que executam o firmware _Interface de firmware extensível unificada (UEFI)_, a GPT é o padrão para particionamento de disco e aborda as limitações do esquema MBR. Uma GPT fornece um máximo de 128 partições. O esquema GPT aloca 64 bits para endereços de bloco lógico, para dar suporte a partições e discos de até oito zebibytes (ZiB) ou oito bilhões de tebibytes (TiB).

![[Pasted image 20240830150722.png]]

O particionamento GPT oferece vantagens sobre o MBR, incluindo a utilização de um Identificador Global Exclusivo (GUID) para discos e partições. Ele fornece redundância com uma GPT primária no início do disco e uma GPT de backup no final. Além disso, a GPT usa uma soma de verificação para detectar erros no cabeçalho e na tabela de partições.

## Gerenciamento de partições
Um administrador pode usar um programa editor de partições para criar, excluir ou alterar partições em um disco. No Red Hat Enterprise Linux, o editor de partições padrão na linha de comando é o `parted`, que é compatível com esquemas de particionamento MBR e GPT.

O comando `parted` usa como seu primeiro argumento o nome do dispositivo que representa todo o dispositivo de armazenamento seguido dos subcomandos.

O exemplo a seguir usa o subcomando `print` para exibir a tabela de partições no disco que é o dispositivo de bloco `/dev/vda` (o primeiro disco de "E/S virtualizada" detectado pelo sistema).
```shell-session
[root@host ~]# parted /dev/vda print
```
![[Pasted image 20240830151404.png]]

Use o comando `parted` sem um subcomando para abrir uma sessão de particionamento interativa.
```shell-session
[root@host ~]# parted /dev/vda
```
![[Pasted image 20240830151438.png]]

Por padrão, o comando `parted` exibe tamanhos em potências de 10 (KB, MB, GB). Você pode alterar o tamanho da unidade com o parâmetro `unit`, que aceita os seguintes valores:

- **`s`** para setor
- **`B`** para byte
- **`MiB`** , **`GiB`** ou **`TiB`** (potências de 2)
- **`MB`** , **`GB`** ou **`TB`** (potências de 10)

```shell-session
[root@host ~]# parted /dev/vda unit s print
```
![[Pasted image 20240830151552.png]]
Como mostrado no exemplo anterior, você também pode especificar vários subcomandos (aqui, `unit` e `print`) na mesma linha.

### Gravação da tabela de partição em um novo disco
Para particionar uma nova unidade, primeiro grave um rótulo de disco. O rótulo do disco indica qual esquema de particionamento deve ser usado. Use `parted` para gravar um rótulo de disco MBR ou um rótulo de disco GPT.

```shell-session
[root@host ~]# parted /dev/vdb mklabel msdos

[root@host ~]# parted /dev/vdb mklabel gpt
```

### Criação de partições de disco MBR
As instruções a seguir criam uma partição de disco MBR. Especifique o dispositivo de disco no qual criar a partição.

Execute o comando `parted` e especifique o nome do dispositivo de disco como um argumento para iniciar no modo interativo. A sessão é exibida `(parted)` como um prompt de subcomando.

```shell-session
[root@host ~]# parted /dev/vdb
```
![[Pasted image 20240830151837.png]]

Use o subcomando `mkpart` para criar uma partição primária ou estendida.
![[Pasted image 20240830151934.png]]

Indique o tipo de sistema de arquivos para criar na partição, como `xfs` ou `ext4`. Esse valor é apenas um rótulo de tipo de partição útil e não cria o sistema de arquivos.
![[Pasted image 20240830152014.png]]

Para listar os arquivos tipos de sistemas de arquivos compatíveis, use o seguinte comando:
```shell-session
[root@host ~]# parted /dev/vdb help mkpart
```
![[Pasted image 20240830152050.png]]

Especifique o setor do disco em que a nova partição será iniciada.
![[Pasted image 20240830152114.png]]

O sufixo `s` fornece o valor em setores ou usa os sufixos `MiB`, `GiB`, `TiB`, `MB`, `GB` ou `TB`. O comando `parted` tem como padrão o sufixo `MB`. O comando `parted` arredonda os valores fornecidos para satisfazer as restrições de disco.

Quando o comando `parted` é iniciado, ele recupera a topologia do disco do dispositivo, como o tamanho do bloco físico do disco. O comando `parted` garante que a posição inicial que você fornecer alinha corretamente a partição com a estrutura do disco para otimizar o desempenho. Se a posição inicial resultar em uma partição desalinhada, o comando `parted` exibirá um aviso. Com a maioria dos discos, um setor inicial múltiplo de 2.048 é seguro.

Especifique o setor do disco no qual a nova partição deve terminar e saia de `parted`. Você pode especificar o final como um tamanho ou como um local de término.
![[Pasted image 20240830152300.png]]

Quando você fornece a posição final, o comando `parted` atualiza a tabela de partições no disco com os novos detalhes da partição.

Execute o comando `udevadm settle`. Esse comando aguarda que o sistema detecte a nova partição e crie o arquivo de dispositivo associado no diretório `/dev`. O prompt retorna quando a tarefa é concluída.
```shell-session
[root@host ~]# udevadm settle
```

Como alternativa ao modo interativo, você pode criar uma partição em um único comando:
```shell-session
[root@host ~]# parted /dev/vdb mkpart primary xfs 2048s 1000MB
```

### Criação de partições GPT
O esquema de GPT também usa o comando `parted` para criar partições. Especifique o dispositivo de disco no qual criar a partição.

Como o usuário `root`, execute o comando `parted` e especifique o nome do dispositivo de disco como um argumento.

```shell-session
[root@host ~]# parted /dev/vdb
```
![[Pasted image 20240830152552.png]]

Use o subcomando `mkpart` para começar a criar a partição. Com o esquema de GPT, cada partição recebe um nome.
![[Pasted image 20240830152622.png]]

Indique o tipo de sistema de arquivos para criar na partição, como `xfs` ou `ext4`. Esse valor não cria o sistema de arquivos, mas é um rótulo de tipo de partição útil.
![[Pasted image 20240830152821.png]]

Especifique o setor no disco em que a nova partição será iniciada.
![[Pasted image 20240830152837.png]]

Especifique o setor do disco para encerrar a nova partição e saia de `parted`. Quando você fornece a posição final, o comando `parted` atualiza a GPT no disco com os novos detalhes da partição.
![[Pasted image 20240830152858.png]]

Execute o comando `udevadm settle`. Esse comando aguarda que o sistema detecte a nova partição e crie o arquivo de dispositivo associado no diretório `/dev`. O prompt retorna quando a tarefa é concluída.
```shell-session
[root@host ~]# udevadm settle
```

Como alternativa ao modo interativo, você pode criar uma partição em um único comando:
```shell-session
[root@host ~]# parted /dev/vdb mkpart userdata xfs 2048s 1000MB
```

### Exclusão de partições
As instruções a seguir se aplicam aos esquemas de particionamento MBR e GPT. Especifique o disco que contém a partição a ser removida.

Execute o comando `parted` com o dispositivo de disco como único argumento.
```shell-session
[root@host ~]# parted /dev/vdb
```
![[Pasted image 20240830153009.png]]

Identifique o número da partição a ser excluída.

![[Pasted image 20240830153109.png]]

Exclua a partição e saia de `parted`. O subcomando `rm` exclui imediatamente a partição da tabela de partições no disco.
![[Pasted image 20240830153135.png]]

Como alternativa ao modo interativo, você pode excluir uma partição em um único comando:
```shell-sessino
[root@host ~]# parted /dev/vdb rm 1
```

## Criação de sistemas de arquivos
Após a criação de um dispositivo de bloco, o próximo passo é adicionar um sistema de arquivos a ele. O Red Hat Enterprise Linux suporta vários tipos de sistema de arquivos, e XFS é o padrão recomendado.

Como o usuário `root`, use o comando `mkfs.xfs` para aplicar um sistema de arquivos XFS a um dispositivo de blocos. Para um sistema de arquivos ext4, use o comando `mkfs.ext4`.
```shell-session
[root@host ~]# mkfs.xfs /dev/vdb1
```
![[Pasted image 20240830153718.png]]

## Montagem de sistemas de arquivos
Depois adicionar o sistema de arquivos, a última etapa é montar o sistema de arquivos em um diretório na estrutura de diretórios. Quando você monta um sistema de arquivos na hierarquia de diretórios, os utilitários de espaço do usuário podem acessar ou gravar arquivos no dispositivo.

### Montagem manual de sistemas de arquivos
Use o comando `mount` para anexar manualmente um dispositivo a um local do diretório de _ponto de montagem_. O comando `mount` exige um dispositivo e um ponto de montagem e pode incluir opções de montagem do sistema de arquivos. As opções do sistema de arquivos personalizam o comportamento do sistema de arquivos.

```shell-session
[root@host ~]# mount /dev/vdb1 /mnt
```

Você também usa o comando `mount` para visualizar sistemas de arquivos montados, os pontos de montagem e as opções.
```shell-session
[root@host ~]# mount | grep vdb1
```
![[Pasted image 20240830153932.png]]

### Montagem persistente de sistemas de arquivos
Quando o servidor é reinicializado, o sistema não monta automaticamente o sistema de arquivos novamente.

Para configurar o sistema para montar automaticamente o sistema de arquivos no boot do sistema, adicione uma entrada ao arquivo `/etc/fstab`. Esse arquivo de configuração lista os sistemas de arquivos a serem montados no boot do sistema.

O arquivo `/etc/fstab` é um arquivo com seis campos por linha delimitado por espaços em branco.
```shell-session
[root@host ~]# cat /etc/fstab
```
![[Pasted image 20240830154359.png]]

O primeiro campo especifica o dispositivo. Este exemplo usa uma UUID para especificar o dispositivo. Os sistemas de arquivos criam e armazenam a UUID no superbloco da partição no momento da criação. Como alternativa, você pode usar o arquivo de dispositivo, como `/dev/vdb1`.

O segundo campo é o ponto de montagem do diretório, a partir do qual o dispositivo de bloco está acessível na estrutura de diretórios. O ponto de montagem deve existir; caso contrário, crie-o com o comando `mkdir`.

O terceiro campo contém o tipo de sistema de arquivos, como `xfs` ou `ext4`.

O quarto campo é a lista de opções separadas por vírgulas a serem aplicadas ao dispositivo. `defaults` é um conjunto de opções comumente usadas. A página do man `mount`(8) documenta as outras opções disponíveis.

O quinto campo é usado pelo comando `dump` para fazer o backup do dispositivo. Outros aplicativos de backup geralmente não usam esse campo.

O último campo, o campo de ordem `fsck`, determina se o comando `fsck` deve ser executado no boot do sistema para verificar se os sistemas de arquivos estão limpos. O valor neste campo indica a ordem em que `fsck` deve ser executado. Para sistemas de arquivos XFS, configure este campo para `0`, porque o XFS não usa o `fsck` para verificar seu status de sistema de arquivos.

Para sistemas de arquivos ext4, configure-o como `1` para o sistema de arquivos raiz e `2` para os outros sistemas de arquivos ext4. Ao usar essa notação, o utilitário `fsck` processa o sistema de arquivos raiz primeiro e, depois, verifica os sistemas de arquivos em discos separados ao mesmo tempo e os sistemas de arquivos no mesmo disco em sequência.

Quando você adiciona ou remove uma entrada no arquivo `/etc/fstab`, execute o comando `systemctl daemon-reload` ou reinicialize o servidor para garantir que o daemon `systemd` carregue e use a nova configuração.
```shell-session
[root@host ~]# systemctl daemon-reload
```

A Red Hat recomenda usar UUIDs para montar sistemas de arquivos de forma persistente, pois os nomes de dispositivos de blocos podem mudar em cenários como mudanças na camada de armazenamento de uma máquina virtual ou a ordem dos discos no boot. Enquanto o nome do dispositivo pode variar, a UUID no superbloco do sistema de arquivos permanece constante.

Use o comando `lsblk --fs` para verificar os dispositivos de bloco conectados a uma máquina e recuperar as UUIDs do sistema de arquivos.
```shell-session
[root@host ~]# lsblk --fs
```
![[Pasted image 20240830155143.png]]





























