O armazenamento no ``RHEL`` consiste em várias camadas de drivers, gerenciadores e ferramentas robustas e modernas. O gerenciamento adequado dessas camadas é essencial, pois a configuração do armazenamento afeta o processo de inicialização, o desempenho dos aplicativos e a capacidade de atender às necessidades específicas de cada aplicação.

![[Pasted image 20240912094253.png]]


#### Dispositivo de bloco

O RHEL suporta uma variedade de dispositivos de bloco, que são acessados através do driver ``SCSI`` e incluem ``HDDs``, ``SSDs`` e ``HBAs``. Ele também é compatível com ``iSCSI``, ``FCoE``, ``virtio``, ``SAS`` e ``NVMe``. 

- **iSCSI**: Utilizado para acessar armazenamento em rede, seja físico ou configurado por software, através de números de unidade lógica (``LUNs``).
- **Fibre Channel over Ethernet (FCoE)**: Transmite quadros de Fibre Channel sobre redes Ethernet, permitindo a convergência de tráfego LAN e SAN em uma única infraestrutura, reduzindo custos de hardware e energia.

Esses dispositivos e protocolos oferecem flexibilidade e eficiência na configuração de armazenamento do RHEL.

#### Vários caminhos
Um caminho é uma conexão entre um servidor e o armazenamento subjacente. O mapeador de dispositivos (`dm-multipath`) é uma ferramenta de vários caminhos nativa do RHEL para configurar caminhos de E/S redundantes em um único dispositivo lógico de caminho agregado.

Um dispositivo lógico criado usando o mapeador de dispositivos (`dm`) aparece como um dispositivo de bloco exclusivo no diretório `/dev/mapper/` para cada LUN anexado ao sistema.

A redundância de vários caminhos de armazenamento também pode ser implementada usando ligação de rede quando o armazenamento, como iSCSI e FCoE, usa cabeamento de rede.

#### RAID
A matriz redundante de discos baratos (``RAID``) é uma tecnologia de virtualização de armazenamento que cria volumes lógicos grandes a partir de múltiplos dispositivos de blocos físicos ou virtuais. Os diferentes níveis de ``RAID`` oferecem redundância e/ou desempenho, utilizando técnicas de espelhamento ou distribuição de dados.

No Red Hat Enterprise Linux (``RHEL``), o Logical Volume Manager (``LVM``) suporta os níveis de RAID 0, 1, 4, 5, 6 e 10. Os volumes lógicos ``RAID`` gerenciados pelo ``LVM`` utilizam drivers de kernel ``mdadm``. Sem o ``LVM``, o mapeador de dispositivos RAID (``dm-raid``) fornece uma interface para os drivers ``mdadm``.

#### Gerenciamento de volume lógico
O ``LVM`` (Logical Volume Manager) permite criar volumes de armazenamento lógico a partir de dispositivos físicos ou virtuais, ocultando a configuração de hardware de aplicativos e clientes de armazenamento. É possível empilhar volumes ``LVM`` e aplicar recursos avançados como criptografia e compactação.

O ``LVM`` é compatível com a criptografia ``LUKS``, que protege dispositivos de bloco inteiros, garantindo segurança mesmo quando removidos. Além disso, o ``LVM`` agora suporta desduplicação e compactação ``VDO``, que pode ser usado juntamente com criptografia ``LUKS`` para volumes lógicos.

#### Sistema de arquivos ou outro uso
A camada superior da pilha de armazenamento geralmente é um sistema de arquivos, mas também pode ser usada para armazenamento bruto em bancos de dados ou aplicativos personalizados. O RHEL recomenda o sistema de arquivos ``XFS`` para a maioria dos casos de uso modernos e exige o ``XFS`` para ferramentas como Red Hat ``Ceph Storage`` e ``Stratis``.

Os bancos de dados podem usar armazenamento de diferentes maneiras. Bancos menores utilizam arquivos regulares em sistemas de arquivos, mas enfrentam limitações de escalabilidade. Bancos maiores frequentemente usam armazenamento bruto, ignorando o cache do sistema de arquivos e criando suas próprias estruturas de cache. Os volumes lógicos são apropriados para esses casos de uso.

O Red Hat ``Ceph Storage`` cria estruturas de metadados em dispositivos brutos para formar dispositivos de armazenamento de objetos (``OSDs``). Nas versões recentes, o ``Ceph`` usa o ``LVM`` para inicializar discos como ``OSDs``. Mais informações estão disponíveis no curso Cloud Storage with Red Hat`` Ceph Storage`` (CL260).

### Gerenciamento de armazenamento do Stratis
O _Stratis_ é uma ferramenta de gerenciamento de armazenamento local desenvolvida pela Red Hat e pela comunidade upstream da Fedora. O Stratis configura o armazenamento inicial, muda uma configuração de armazenamento e usa recursos avançados de armazenamento.

O Stratis é um serviço de gerenciamento de armazenamento que cria e administra pools de dispositivos de armazenamento físico e sistemas de arquivos de maneira transparente. Ele utiliza o conceito de provisionamento thin, alocando espaço de armazenamento dinamicamente a partir de um pool, conforme necessário, permitindo que sistemas de arquivos apareçam maiores do que o espaço físico realmente ocupado.

Você pode criar múltiplos pools com diferentes dispositivos e até 224 sistemas de arquivos por pool. O Stratis usa componentes padrão do Linux e a infraestrutura do mapeador de dispositivos, semelhante ao LVM, e formata os sistemas de arquivos gerenciados com XFS.

![[Pasted image 20240912100653.png]]

### Métodos de administração do ``Stratis``
Para gerenciar os sistemas de arquivos usando a solução de gerenciamento de armazenamento do ``Stratis``, instale os pacotes `stratis-cli` e `stratisd`. O pacote `stratis-cli` fornece o comando `stratis`, que envia solicitações de reconfiguração ao daemon do sistema `stratisd`. 

O pacote `stratisd` fornece o serviço `stratisd`, que lida com as solicitações de reconfiguração, além de gerenciar e monitorar os dispositivos de bloco, os pools e os sistemas de arquivos do ``Stratis``.

#### Instalação e habilitação do Stratis
Para usar o Stratis, certifique-se de que seu sistema tem o software necessário e que o serviço `stratisd` está em execução. Instale os pacotes `stratis-cli` e `stratisd` e inicie e habilite o serviço `stratisd`.

```shell-session
[root@host ~]# dnf install stratis-cli stratisd

[root@host ~]# systemctl enable --now stratisd
```

#### Criação de pools do Stratis
Crie pools de um ou mais dispositivos de bloco usando o comando `stratis pool create`. Depois, use o comando `stratis pool list` para visualizar a lista de pools disponíveis.
```shell-session
[root@host ~]# stratis pool create pool1 /dev/vdb

[root@host ~]# stratis pool list
```
![[Pasted image 20240912102049.png]]

Use o comando `stratis pool add-data` para adicionar dispositivos de bloco a um pool. Depois, use o comando `stratis blockdev list` para verificar os dispositivos de bloco de um pool.
```shell-session
[root@host ~]# stratis pool add-data pool1 /dev/vdc
[root@host ~]# stratis blockdev list pool1
```
![[Pasted image 20240912102233.png]]

#### Gerenciamento de sistemas de arquivos do Stratis
Use o comando `stratis filesystem create` para criar um sistema de arquivos a partir de um pool. Os links para os sistemas de arquivos Stratis estão no diretório ``/dev/stratis/_`pool1`_``. Use o comando `stratis filesystem list` para visualizar a lista de sistemas de arquivos disponíveis.
```shell-session
[root@host ~]# stratis filesystem create pool1 fs1
[root@host ~]# stratis filesystem list
```
![[Pasted image 20240912102616.png]]

Crie um snapshot do sistema de arquivos do Stratis usando o comando `stratis filesystem snapshot`. Os snapshots são independentes dos sistemas de arquivos de origem.

O Stratis aloca dinamicamente o espaço de armazenamento de snapshots e usa 560 MB iniciais para armazenar o diário do sistema de arquivos.

```shell-session
[root@host ~]# stratis filesystem snapshot pool1 fs1 snapshot1
```

#### Montagem persistente de sistemas de arquivos do Stratis
Você pode montar os sistemas de arquivos do Stratis de modo persistente editando o arquivo `/etc/fstab` e especificando os detalhes do sistema de arquivos. Você também pode usar o comando `stratis filesystem list` para obter a UUID do sistema de arquivos.

```shell-session
[root@host ~]# lsblk --output=UUID /dev/stratis/pool1/fs1
```
![[Pasted image 20240912102904.png]]

O exemplo a seguir mostra uma entrada no arquivo `/etc/fstab` para montar de modo persistente um sistema de arquivos do Stratis. Essa entrada de exemplo é uma única linha longa no arquivo. A opção de montagem `x-systemd.requires=stratisd.service` atrasa a montagem do sistema de arquivos até o daemon `systemd` iniciar o serviço `stratisd` durante o processo de boot.
![[Pasted image 20240912102958.png]]












































