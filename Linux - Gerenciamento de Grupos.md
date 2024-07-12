Sem opções, o comando `groupadd` usa a próxima GID disponível no intervalo que as variáveis `GID_MIN` e `GID_MAX` especificam no arquivo `/etc/login.defs`.

```bash
groupadd -g 1000 administration
tail /etc/group
```
![[Pasted image 20240710150053.png]]


A opção `-r` do comando `groupadd` cria grupos. Como com grupos normais, grupos de sistema usam uma GID do intervalo de GIDs de sistema válidos listados no arquivo `/etc/login.defs`. Os itens de configuração `SYS_GID_MIN` e `SYS_GID_MAX` no arquivo `/etc/login.defs` definem o intervalo de GIDs do sistema.
```bash
groupadd -r group02
tail /etc/group
```
![[Pasted image 20240710150525.png]]

## Modificação de grupos

**`-n`** - Define um novo nome para o grupo.
```bash
groupmod -n group novoNome nomeAntigo
```

**`-g`** - Especifica uma nova GID
```bash
groupmod -g 20000 group0022
```
Assim a GID vai ser alterada para 20000

## Remoção de grupos

```bash groupdel group0022
```

## Alteração de associação de um grupo

Use o comando `usermod -g` para alterar o grupo principal de um usuário.
```bash
id user02
# uid=1006(user02) gid=1008(user02) groups=1008(user02)

usermod -g group01 user02
id user02
# uid=1006(user02) gid=10000(group01) groups=10000(group01)
```


Use o comando `usermod -aG` para adicionar um usuário a um gripo suplementar.
```bash
id user03
# uid=1007(user03) gid=1009(user03) groups=1009(user03)

usermod -aG group01 user03
id user03
# uid=1007(user03) gid=1009(user03) groups=1009(user03),10000(group01)

```

> A opção `-a` do comando `usermod` ativa o modo _acrescentar_. Sem a opção `-a`, o comando remove o usuário de qualquer um dos grupos suplementares atuais que não estejam incluídos na lista de opções `-G`.

#### Alteração temporária de grupo primário
Somente o grupo primário de um usuário é usado para novos atributos de criação de arquivo. Você pode alternar temporariamente seu grupo primário para um grupo suplementar ao qual já pertence.

Você poderá alternar se estiver prestes a criar arquivos, manualmente ou em script, e quiser atribuir um grupo diferente como proprietário quando estiverem sendo criados.

Neste exemplo, o grupo `group01` torna-se temporariamente o grupo primário desse usuário.

```bash
id
# uid=1007(user03) gid=1009(user03) groups=1009(user03),10000(group01)

newgrp group01
id
# uid=1007(user03) gid=10000(group01) groups=1009(user03),10000(group01)
```

























































































