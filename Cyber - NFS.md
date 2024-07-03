O Network File System (``NFS``), foi desenvolvido pela Sun Microsystem, e tem o mesmo propósito do SMB. Seu propósito é acessar arquivos uma rede como se fosse locais. No entanto usa um protocolo diferente do SMB. 

Isso significa que os clientes NFS não podem se comunicar diretamente com servidores SMB

Enquanto o protocolo NFS versão 3.0 ( `NFSv3`), que está em uso há muitos anos, autentica o computador cliente, isso muda com `NFSv4`. Aqui, como no protocolo SMB do Windows, o usuário deve se autenticar.

| **Versão** | **Características**                                                                                                                                                                                                                                        |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NFSv2`    | É mais antigo, mas é suportado por muitos sistemas e inicialmente era operado inteiramente por UDP.                                                                                                                                                        |
| `NFSv3`    | Possui mais recursos, incluindo tamanho de arquivo variável e melhor relatório de erros, mas não é totalmente compatível com clientes NFSv2.                                                                                                               |
| `NFSv4`    | Inclui Kerberos, funciona através de firewalls e na Internet, não requer mais portmappers, suporta ACLs, aplica operações baseadas em estado e fornece melhorias de desempenho e alta segurança. É também a primeira versão a ter um protocolo com estado. |

NFSv4.1 inclui um mecanismo de entroncamento de sessão, também conhecido como NFS multipathing. Uma vantagem significativa do NFSv4 sobre seus predecessores é que apenas uma porta `2049` UDP ou TCP é usada para executar o serviço, o que simplifica o uso do protocolo em firewalls.

O NFS é baseado no protocolo ONC-RPC exposto nas portas UDP e TCP ``111``, que usa a representação de dados externa (XDR), para troca de dados independente do sistema.

Não possui mecanismo de autenticação ou autorização

Em vez disso, a autenticação é completamente transferida para as opções do protocolo RPC. A autorização é derivada das informações disponíveis do sistema de arquivos.

A autenticação mais comum é via UNIX `UID`/ `GID`e `group memberships`, e é por isso que essa sintaxe é mais provavelmente aplicada ao protocolo NFS.

Um problema é que o cliente e o servidor não precisam necessariamente ter os mesmos mapeamentos de UID/GID para usuários e grupos, e o servidor não precisa fazer mais nada. Nenhuma verificação adicional pode ser feita por parte do servidor. É por isso que o NFS deve ser usado somente com esse método de autenticação em redes confiáveis.

## Configuração padrão

O arquivo `/etc/exports` contém uma tabela de sistemas de arquivos físicos em um servidor NFS acessível pelos clientes. A [Tabela de Exportações NFS](http://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html) mostra quais opções ela aceita e assim indica quais opções estão disponíveis para nós.

### Arquivo de exportação

```sh
cat /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
```


Primeiro, a pasta é especificada e disponibilizada para outros, e então os direitos que eles terão neste compartilhamento NFS são conectados a um host ou a uma sub-rede. Finalmente, opções adicionais podem ser adicionadas aos hosts ou sub-redes.

| **Opção**          | **Descrição**                                                                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`               | Permissões de leitura e gravação.                                                                                                             |
| `ro`               | Permissões somente leitura.                                                                                                                   |
| `sync`             | Transferência de dados síncrona. (Um pouco mais lento)                                                                                        |
| `async`            | Transferência de dados assíncrona. (Um pouco mais rápido)                                                                                     |
| `secure`           | Portas acima de 1024 não serão utilizadas.                                                                                                    |
| `insecure`         | Serão utilizadas portas acima de 1024.                                                                                                        |
| `no_subtree_check` | Esta opção desabilita a verificação de árvores de subdiretórios.                                                                              |
| `root_squash`      | Atribui todas as permissões aos arquivos de UID/GID raiz 0 ao UID/GID de anônimo, o que impede `root`o acesso a arquivos em uma montagem NFS. |

Criação de uma entrada

### ExportFS

```shell-session
root@nfs:~# echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
root@nfs:~# systemctl restart nfs-kernel-server 
root@nfs:~# exportfs

/mnt/nfs      	10.129.14.0/24
```

Compartilhamos a pasta `/mnt/nfs`para a sub-rede `10.129.14.0/24`com a configuração mostrada acima.

Isso significa que todos os hosts na rede poderão montar esse compartilhamento NFS e inspecionar o conteúdo dessa pasta.

## Configurações perigosas

|                  |                                                                                                                                                 |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`             | Permissões de leitura e gravação.                                                                                                               |
| `insecure`       | Portas acima de 1024 serão usadas.                                                                                                              |
| `nohide`         | Se outro sistema de arquivos foi montado abaixo de um diretório exportado, este diretório será exportado por sua própria entrada de exportação. |
| `no_root_squash` | Todos os arquivos criados pelo root são mantidos com o UID/GID 0.                                                                               |
Podemos dar uma olhada na opção `insecure`. Isso é perigoso porque os usuários podem usar portas acima de 1024. As primeiras 1024 portas só podem ser usadas pelo root. Isso evita que nenhum usuário possa usar sockets acima da porta 1024 para o serviço NFS e interagir com ele.

## Footprint the Service

as portas TCP `111`e `2049`são essenciais. Também podemos obter informações sobre o serviço NFS e o host via RPC, conforme mostrado abaixo no exemplo.

## Nmap

```sh
sudo nmap 10.129.14.128 -p111,2049 -sV -sC
```

O `rpcinfo`script NSE recupera uma lista de todos os serviços RPC em execução no momento, seus nomes e descrições, e as portas que eles usam.

```sh
sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049
```

---
### Mostrar ações NFS disponíveis

```shell-session
showmount -e 10.129.14.128
```


### Montagem de compartilhamento NFS

```sh
mkdir target-NFS
sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
cd target-NFS
tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

Lá teremos a oportunidade de acessar os direitos e os nomes de usuários e grupos aos quais os arquivos exibidos e visualizáveis ​​pertencem

Porque uma vez que temos os nomes de usuários, nomes de grupos, UIDs e GUIDs, podemos criá-los em nosso sistema e adaptá-los ao compartilhamento NFS para visualizar e modificar os arquivos.

### Listar conteúdo com nomes de usuários e nomes de grupos

```sh
ls -l mnt/nfs/

# total 16
# -rw-r--r-- 1 cry0l1t3 cry0l1t3 1872 Sep 25 00:55 cry0l1t3.priv
# -rw-r--r-- 1 cry0l1t3 cry0l1t3  348 Sep 25 00:55 cry0l1t3.pub
# -rw-r--r-- 1 root     root     1872 Sep 19 17:27 id_rsa
# -rw-r--r-- 1 root     root      348 Sep 19 17:28 id_rsa.pub
# -rw-r--r-- 1 root     root        0 Sep 19 17:22 nfs.share
```

### Listar conteúdos com UIDs e GUIDs

```sh
ls -n mnt/nfs/

# total 16
# -rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 cry0l1t3.priv
# -rw-r--r-- 1 1000 1000  348 Sep 25 00:55 cry0l1t3.pub
# -rw-r--r-- 1    0 1000 1221 Sep 19 18:21 backup.sh
# -rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
# -rw-r--r-- 1    0    0  348 Sep 19 17:28 id_rsa.pub
# -rw-r--r-- 1    0    0    0 Sep 19 17:22 nfs.share
```

Se a opção `root_squash` estiver definida, não poderemos editar o arquivo `backup.sh` mesmo como ``root``

Também podemos usar NFS para escalonamento posterior. Por exemplo, se tivermos acesso ao sistema via SSH e quisermos ler arquivos de outra pasta que um usuário específico pode ler, precisaríamos carregar um shell para o compartilhamento NFS que tem o `SUID`desse usuário e então executar o shell via usuário SSH.

### Desmontando

```sh
cd ..
sudo umount ./target-NFS
```














































































































