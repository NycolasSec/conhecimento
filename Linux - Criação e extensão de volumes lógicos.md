O **Gerenciador de Volumes Lógicos (LVM)** é uma camada de abstração entre o armazenamento físico e o sistema operacional, permitindo maior flexibilidade na gestão de volumes de armazenamento. Com o LVM, volumes podem ser redimensionados sem interrupção, e ele oferece ferramentas para o gerenciamento eficiente.

- **Dispositivos físicos**: Volumes lógicos utilizam dispositivos físicos, como partições, discos completos ou matrizes RAID. Esses dispositivos devem ser inicializados como volumes físicos LVM.
  
- **Volumes físicos (PVs)**: Os ``PVs`` são dispositivos físicos divididos em blocos menores chamados extensões físicas (``PEs``), que são a menor unidade de armazenamento.

- **Grupos de volumes (VGs)**: Um VG é um pool de armazenamento composto por um ou mais ``PVs``. Ele agrupa o espaço disponível e é utilizado para a criação de volumes lógicos.

- **Volumes lógicos (LVs)**: ``LVs`` são criados a partir das ``PEs`` livres em um VG e são utilizados por aplicativos e sistemas operacionais. As extensões lógicas (``LEs``) correspondem às ``PEs`` e podem ser configuradas para espelhamento e outras opções avançadas.

#### Fluxo de trabalho do gerenciador de volumes lógicos
A criação de armazenamento LVM requer a construção de estruturas em um fluxo de trabalho lógico.

- Determine os dispositivos físicos usados para criar volumes físicos e inicialize esses dispositivos como volumes físicos de LVM.
- Crie um grupo de volumes a partir de vários volumes físicos.
- Crie os volumes lógicos a partir do espaço disponível no grupo de volumes.
- Formate o volume lógico com um sistema de arquivos e monte-o, ou ative-o como espaço de swap, ou passe o volume bruto para um banco de dados ou servidor de armazenamento para estruturas avançadas.

![[Pasted image 20240912082133.png]]


>[!NOTE] Os exemplos aqui usam um nome de dispositivo `/dev/vdb` e suas partições de armazenamento. Use os comandos `lsblk`, `blkid` ou `cat /proc/partitions` para identificar os dispositivos no seu sistema.

### Criação de armazenamento LVM
A criação de um volume lógico envolve a criação de partições de dispositivos físicos, volumes físicos e grupos de volumes. Depois de criar um LV, formate o volume e monte-o para acessá-lo como armazenamento.

#### Preparação de dispositivos físicos
O particionamento é opcional quando já está presente. Use o comando `parted` para criar uma partição no dispositivo físico. Defina o dispositivo físico para o tipo de partição `Linux LVM`. Use o comando `udevadm settle` para registrar a nova partição com o kernel.

```shell-session
[root@host ~]# parted /dev/vdb mklabel gpt mkpart primary 1MiB 769MiB
...output omitted...

[root@host ~]# parted /dev/vdb mkpart primary 770MiB 1026MiB

[root@host ~]# parted /dev/vdb set 1 lvm on

[root@host ~]# parted /dev/vdb set 2 lvm on

[root@host ~]# udevadm settle
```

#### Criação de volumes físicos
Use o comando `pvcreate` para rotular a partição física como um volume físico LVM. Rotule vários dispositivos ao mesmo tempo usando nomes de dispositivos delimitados por espaço como argumentos no comando `pvcreate`. Este exemplo rotula os dispositivos `/dev/vdb1` e `/dev/vdb2` como PVs que estão prontos para a criação de grupos de volumes.

```shell-session
[root@host ~]# pvcreate /dev/vdb1 /dev/vdb2
```
![[Pasted image 20240912082718.png]]

#### Criação de um grupo de volumes
O comando `vgcreate` cria um ou mais volumes físicos em um grupo de volumes. O primeiro argumento é um nome de grupo de volumes seguido por um ou mais volumes físicos para alocar a esse VG. Este exemplo cria o VG `vg01` usando os PVs `/dev/vdb1` e `/dev/vdb2`.

```shell-session
[root@host ~]# vgcreate vg01 /dev/vdb1 /dev/vdb2
```
![[Pasted image 20240912082754.png]]

#### Criação de um volume lógico.
O comando `lvcreate` cria um volume lógico a partir das PEs disponíveis em um grupo de volumes. Use o comando `lvcreate` para definir o nome e o tamanho do LV, e o nome do VG que conterá esse volume lógico. Este exemplo cria o LV `lv01` com 300 MiB no VG `vg01`.

```shell-session
[root@host ~]# lvcreate -n lv01 -L 300M vg01
```
![[Pasted image 20240912082923.png]]

Esse comando poderá falhar se o grupo de volumes não tiver extensões físicas livres suficientes. O tamanho do LV é arredondado para o próximo valor do tamanho da PE quando o tamanho não corresponde exatamente.

A opção `-L` do comando `lvcreate` exige tamanhos em bytes, mebibytes (megabytes binários, 1.048.576 bytes), gibibytes (gigabytes binários) ou similar. A letra minúscula `-l` requer tamanhos que são especificados como um número de extensões físicas. Os comandos a seguir são duas opções para criar o mesmo LV com o mesmo tamanho:

- **`lvcreate -n lv01 -L 128M vg01`** : cria um LV de 128 MiB, arredondado para a próxima PE.
- **`lvcreate -n lv01 -l 32 vg01`** : cria um LV de 32 PEs a 4 MiB cada com um total de 128 MiB.

#### Criar um volume lógico com desduplicação e compactação
O **RHEL 9** utiliza o **Virtual Data Optimizer (VDO)** no **LVM** para gerenciar volumes VDO, oferecendo desduplicação de blocos, compactação e provisionamento thin. A implementação VDO permite usar até 256 TB de armazenamento físico e é gerenciada como um tipo de volume lógico no LVM, semelhante ao provisionamento dinâmico. 

Um volume LVM VDO consiste em dois componentes:
1. **LV do pool VDO**: Armazena, desduplica e compacta os dados, definindo o tamanho do volume com base no dispositivo físico.
2. **LV do VDO**: É o dispositivo virtual que define o tamanho lógico do volume antes de desduplicação e compactação.

O volume VDO é apresentado como um volume lógico regular e pode ser formatado e utilizado como qualquer outro LV.

Para usar a desduplicação e a compactação do VDO, instale os pacotes `vdo` e `kmod-kvdo`.
```shell-session
[root@host ~]# dnf install vdo kmod-kvdo
```

Verifique se o grupo de volumes LVM selecionado tem capacidade de armazenamento livre suficiente. Use o comando `lvcreate` com o parâmetro `--type vdo` para criar um LV do VDO.
```shell-session
[root@host ~]# lvcreate --type vdo --name vdo-lv01 --size 5G vg01
```
![[Pasted image 20240912083440.png]]

#### Criação de um sistema de arquivos no volume lógico
Especifique o volume lógico usando o nome tradicional, ``/dev/_`vgname`_/_`lvname`_``, ou o nome do mapeador de dispositivos do kernel, `/dev/mapper/vgname-lvname`.

Use o comando `mkfs` para criar um sistema de arquivos no novo volume lógico.
```shell-session
[root@host ~]# mkfs -t xfs /dev/vg01/vdo-lv01
```
![[Pasted image 20240912083728.png]]

Crie um ponto de montagem usando o comando `mkdir`.
```shell-session
[root@host ~]# mkdir /mnt/data
```

Para tornar o sistema de arquivos disponível de modo persistente, adicione uma entrada ao arquivo `/etc/fstab`.
![[Pasted image 20240912083810.png]]

Montagem do LV usando o comando `mount`.
```shell-session
[root@host ~]# mount /mnt/data/
```

>[!NOTE] Você pode montar um volume lógico por nome ou por ``UUID``, pois o ``LVM`` analisa os `PVs` procurando pela ``UUID``. Esse comportamento tem êxito mesmo quando o ``VG`` foi criado usando um nome, pois o ``PV`` sempre contém uma ``UUID``.

### Exibição de status do componente LVM
Use os comandos `pvdisplay`, `vgdisplay` e `lvdisplay` para mostrar as informações de status dos componentes do LVM. Os comandos associados `pvs`, `vgs` e `lvs` são comumente usados e mostram um subconjunto das informações de status, com uma linha para cada entidade.

#### Exibir informações de volume físico
O comando `pvdisplay` exibe informações sobre os ``PVs``. Use o comando sem argumentos para listar informações de todos os ``PVs`` no sistema. Fornecer o nome do PV como o argumento para o comando mostra as informações específicas ao PV.

```shell-session
[root@host ~]# pvdisplay /dev/vdb1
```
![[Pasted image 20240912085125.png]]

| ![[Pasted image 20240912085253.png]] | `LV Path` mostra o nome do dispositivo do LV.                                                                                                |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| ![[Pasted image 20240912085302.png]] | `VG Name` mostra o VG usado para criar esse LV.                                                                                              |
| ![[Pasted image 20240912085309.png]] | `LV Size` mostra o tamanho total do LV. Use as ferramentas do sistema de arquivos para determinar o espaço livre e o espaço usado para o LV. |
| ![[Pasted image 20240912085318.png]] | `Current LE` mostra o número de extents lógicos usados por este LV.                                                                          |

### Estender e reduzir o armazenamento LVM
Depois de criar um volume lógico, você pode estender o volume para expandir o sistema de arquivos. Poderá ser necessário estender o ``PV`` ou ``VG`` para aumentar a capacidade de armazenamento do ``LV``.

#### Extensão de tamanho de um grupo de volumes
Talvez seja necessário adicionar mais espaço em disco para estender um VG.

Prepare o dispositivo físico e crie o volume físico quando não estiver disponível.
```shell-session
[root@host ~]# parted /dev/vdb mkpart primary 1072MiB 1648MiB
...output omitted...

[root@host ~]# parted /dev/vdb set 3 lvm on
...output omitted...

[root@host ~]# udevadm settle

[root@host ~]# pvcreate /dev/vdb3
  Physical volume "/dev/vdb3" successfully created.
```

O comando `vgextend` adiciona o novo PV ao VG. Forneça os nomes de VG e PV como argumentos para o comando `vgextend`.
```shell-session
[root@host ~]# vgextend vg01 /dev/vdb3
```
![[Pasted image 20240912090028.png]]
Esse comando amplia o VG `vg01` para o tamanho do PV `/dev/vdb3`.

#### Extensão do tamanho do volume lógico
Adicione extensões físicas livres ao LV no VG para estender a capacidade do LV e expandir o sistema de arquivos. Use o comando `vgdisplay` para confirmar se o grupo de volumes tem espaço livre suficiente para a extensão do LV.

Use o comando `lvextend` para estender o LV.
```shell-session
[root@host ~]# lvextend -L +500M /dev/vg01/lv01
```
![[Pasted image 20240912090318.png]]
Esse comando aumenta o tamanho do volume lógico `lv01` em 500 MiB. O sinal de adição (`+`) antes do tamanho significa adicionar esse valor ao tamanho existente. Na falta do sinal, o valor define o tamanho final do LV.

A opção `-l` do comando `lvextend` espera o número de PE como argumento. A opção `-L` do comando `lvextend` espera tamanhos em bytes, mebibytes, gibibytes e similares.

#### Extensão de um sistema de arquivos XFS para o tamanho do volume lógico
O comando `xfs_growfs` ajuda a expandir o sistema de arquivos para ocupar o LV estendido. O sistema de arquivos de destino deve ser montado antes de você usar o comando `xfs_growfs`.
```shell-session
[root@host ~]# xfs_growfs /mnt/data/
```
![[Pasted image 20240912090741.png]]

>[!IMPORTANT] Sempre execute o comando `xfs_growfs` depois de executar o comando `lvextend`. Use a opção `-r` do comando `lvextend` para executar as duas etapas consecutivamente. Depois de estender o LV, redimensione o sistema de arquivos usando o comando `fsadm`. Essa opção é compatível com vários outros sistemas de arquivos.

#### Extensão de um sistema de arquivos EXT4 para o tamanho do volume lógico
As etapas para estender o ``LV`` usando o sistema de arquivos `ext4` são as mesmas para o ``LV`` usando o sistema de arquivos `XFS`, exceto para a etapa para redimensionar o sistema de arquivos.

O comando `resize2fs` expande o sistema de arquivos para ocupar o novo LV estendido. Você pode continuar usando o sistema de arquivos enquanto o redimensiona.

```shell-session
[root@host ~]# resize2fs /dev/vg01/lv01
```
![[Pasted image 20240912091113.png]]

O comando `xfs_growfs` é usado para expandir sistemas de arquivos XFS, geralmente especificando o ponto de montagem ou o dispositivo montado. Já o comando `resize2fs` redimensiona sistemas de arquivos ext2, ext3 e ext4, aceitando o nome do dispositivo como argumento.

O `xfs_growfs` só suporta redimensionamento on-line, enquanto o `resize2fs` permite redimensionar on-line e off-line. Embora sistemas de arquivos ext4 possam ser redimensionados para cima ou para baixo, o redimensionamento de sistemas de arquivos XFS é possível apenas para aumentar o tamanho.

#### Estender volumes lógicos de espaço de swap
Você pode estender os LVs usados como espaço de troca. No entanto, o processo é diferente da expansão do sistema de arquivos `ext4` ou `XFS`. Volumes lógicos usados como espaço de troca devem estar off-line para serem estendidos.

Use o comando `swapoff` para desativar o espaço de swap no LV.
```shell-session
[root@host ~]# swapoff -v /dev/vg01/swap
```
![[Pasted image 20240912091619.png]]

Use o comando `lvextend` para estender o LV.
```shell-session
[root@host ~]# lvextend -L +300M /dev/vg01/swap
```
![[Pasted image 20240912091651.png]]

Use o comando `mkswap` para formatar o LV como espaço de troca.
```shell-session
[root@host ~]# mkswap /dev/vg01/swap
```
![[Pasted image 20240912091914.png]]

Use o comando `swapon` para ativar o espaço de swap no LV.
```shell-session
[root@host ~]# swapon /dev/vg01/swap
```

#### Reduzir o armazenamento do grupo de volumes
A redução de um VG envolve a remoção de PVs não usados do VG. O comando `pvmove` move dados de extensões em um PV para extensões em outro PV com extensões livres suficientes no mesmo VG.

Você pode continuar a usar o LV do mesmo VG durante a redução. Use a opção `-A` do comando `pvmove` para fazer backup automaticamente dos metadados do VG após uma alteração. Essa opção usa o comando `vgcfgbackup` para fazer backup de metadados automaticamente.

```shell-session
[root@host ~]# pvmove -A y /dev/vdb3
```

Use o comando `vgreduce` para remover um PV de um VG.
```shell-session
[root@host ~]# vgreduce vg01 /dev/vdb3
```
![[Pasted image 20240912092702.png]]

### Remoção de armazenamento LVM
Use os comandos `lvremove`, `vgremove` e `pvremove` para remover um componente do LVM que não é mais necessário.

#### Preparar o sistema de arquivos
Mova todos os dados que devem ser mantidos para outro sistema de arquivos. Use o comando `umount` para desmontar o sistema de arquivos e, em seguida, remova todas as entradas `/etc/fstab` associadas a esse sistema de arquivos.

```shell-session
[root@host ~]# umount /mnt/data
```

#### Remoção do volume lógico
Use o comando ``lvremove _`DEVICE-NAME`_`` para remover um volume lógico que não é mais necessário. Desmonte o sistema de arquivos LV antes de executar esse comando. O comando solicita a confirmação antes de remover o LV.
```shell-session
[root@host ~]# lvremove /dev/vg01/lv01
```
As extensões físicas do LV são liberadas e disponibilizadas para atribuição a LVs novos ou existentes no grupo de volumes.

#### Remoção do grupo de volumes
Use o comando `vgremove VG-NAME` para remover um grupo de volumes que não é mais necessário.

```shell-session
[root@host ~]# vgremove vg01
```
Os volumes físicos do VG são liberados e disponibilizados para atribuição a VGs novos ou existentes no sistema.

#### Remoção de volumes físicos
Use o comando `pvremove` para remover os volumes físicos que não são mais necessários. Use uma lista delimitada por espaços de dispositivos do PV para remover mais de um dispositivo por vez. Esse comando exclui os metadados de PV da partição (ou disco).
```shell-session
[root@host ~]# pvremove /dev/vdb1 /dev/vdb2
```










































































