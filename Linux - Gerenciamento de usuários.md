#Linux 
# Linux - Gerenciamento de usuários

| **Comando** | **Descrição**                                                                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `sudo`      | Execute o comando como um usuário diferente.                                                                                                                             |
| `su`        | O utilitário `su` solicita credenciais de usuário apropriadas via PAM e alterna para esse ID de usuário (o usuário padrão é o superusuário). Um shell é então executado. |
| `useradd`   | Cria um novo usuário ou atualiza as informações padrão do novo usuário.                                                                                                  |
| `userdel`   | Exclui uma conta de usuário e arquivos relacionados.                                                                                                                     |
| `usermod`   | Modifica uma conta de usuário.                                                                                                                                           |
| `addgroup`  | Adiciona um grupo ao sistema.                                                                                                                                            |
| `delgroup`  | Remove um grupo do sistema.                                                                                                                                              |
| `passwd`    | Altera a senha do usuário.                                                                                                                                               |

### Criação de usuários

O comando `useradd --help` exibe as opções básicas para substituir os padrões. Geralmente, é possível usar as mesmas opções com o comando `usermod` para modificar um usuário existente.

O arquivo `/etc/login.defs` define algumas opções padrão para contas de usuário, como o intervalo de números de UID válidos e as regras padrão para duração de senhas. Não afeta usuários criados anteriormente, somente novos usuários.

```bash
useradd username
```

### Modificação de usuários

O comando `usermod --help` exibe as opções para modificar uma conta.

| Opções do `usermod`:    | Uso                                                                                                                                                                                     |
| :---------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-a, --append`          | Use-o com a opção `-G` para adicionar os grupos suplementares ao conjunto atual de associações de grupos, em vez de substituir o conjunto de grupos suplementares por um novo conjunto. |
| `-c, --comment COMMENT` | Adiciona o texto `COMMENT` ao campo de comentários.                                                                                                                                     |
| `-d, --home HOME_DIR`   | Especifica um diretório pessoal para a conta de usuário.                                                                                                                                |
| `-g, --gid GROUP`       | Especifica o grupo primário para a conta de usuário.                                                                                                                                    |
| `-G, --groups GROUPS`   | Especifica uma lista de grupos suplementares separados por vírgula para a conta de usuário.                                                                                             |
| `-L, --lock`            | Bloqueia a conta do usuário.                                                                                                                                                            |
| `-m, --move-home`       | Move um diretório pessoal do usuário para um novo local. Você deve usá-lo com a opção `-d`.                                                                                             |
| `-s, --shell SHELL`     | Especifica um shell de login específico para a conta de usuário.                                                                                                                        |
| `-U, --unlock`          | Desbloqueia a conta do usuário.                                                                                                                                                         |

### Exclusão de usuários

```bash
userdel nycolas
```
Remove o usuário `nycolas` de `/etc/passwd`, mas deixa o diretório pessoal do usuário intacto.

```bash
userdel -r nycolas
```
Remove o usuário `/etc/passwd` e exclui o diretório pessoal do usuário.


---

`id` para mostrar informações sobre o usuário conectado atualmente:

```bash
id
```
![[Pasted image 20240710134650.png]]

Para vermos de outro usuário, basta passarmos o nome para o comando `id`

```bash
id user2
```
![[Pasted image 20240710134832.png]]

### processo

```bash
ps -au
```
![[Pasted image 20240710134948.png]]
Por padrão o `ps` mostra apenas os processos no shell atual.

- **`a`** : Lista todos os processos
- **`u`** : Lista o usuário associado aos processos

---

 Por padrão, os sistemas usam o arquivo simples `/etc/passwd` para armazenar informações sobre os usuários locais.

```bash
cat /etc/passwd
```
![[Pasted image 20240710135320.png]]

- **`user01`** : o nome de usuário para esse usuário.
- **`x`** : a senha criptografada do usuário foi armazenada historicamente aqui; agora é um espaço reservado.
- **`1000`** : o número da UID para essa conta de usuário.
- **`1000`** : o número da GID para o grupo primário dessa conta de usuário.
- **`User One`** : um breve comentário, uma descrição ou o nome real desse usuário.
- **`/home/user01`** : o diretório pessoal do usuário e o diretório de trabalho inicial quando o shell de login é iniciado.
- **`/bin/bash`** : o programa de shell padrão para esse usuário que é executado no login. Algumas contas usam o shell `/﻿sbin/nologin` para proibir logins interativos com essa conta.

## Grupos

Um grupo é uma coleção de usuários que precisam compartilhar o acesso a arquivos e outros recursos do sistema.

Internamente, o sistema distingue grupos pelo _ID do grupo_ ou _GID_, atribuído a eles

Por padrão, os sistemas usam o arquivo `/etc/group` para armazenar informações sobre os grupos locais.

Cada linha no arquivo `/etc/group` contém informações sobre um grupo. Cada entrada de grupo é dividida em quatro campos separados por cor. Veja a seguir um exemplo de linha de `/etc/group`:

```bash
cat /etc/group
```
![[Pasted image 20240710135613.png]]

- **`group01`** : nome do grupo.
- **`x`** : campo de senha de grupo obsoleto; agora é um espaço reservado.
- **`10000`** : o número da GID para esse grupo (`10000`).
- **`user01,user02,user03`** : uma lista de usuários que são membros desse grupo como um grupo suplementar.

### Grupos primários e grupos suplementares

Quando se cria um usuário, um grupo de mesmo nome é criado par ser o grupo primário, o usuário é o único membro desse grupo privado.

Os usuários também podem ter _grupos suplementares_. A associação em grupos suplementares é armazenada no arquivo `/etc/group`. Os usuário recebem permissões com base no acesso de seus grupos, sendo eles primários ou suplementares


---

#### su

```bash
su nycolas
su - nycolas
```

- **`-`** : Inicia um shell de login, definindo o ambiente de shell como um novo login. Enquanto que por padrão ele inicia um novo usuário com as configurações do shell atual.

Outro benefício de `sudo` é registrar por padrão todos os comandos executados em `/var/log/secure`.

## Restrição de acesso

```bash
usermod -L sysadmin03
```
Bloqueia a conta de usuário `sysadmin03`

```bash
usermod -L -e 2022-08-14 cloudadmin10
```
Aqui o comando `usermod` bloqueia e expira o usuário `cloudadmin10` em `2022-08-14`.

Use a opção `-U` do comando `usermod` para habilitar o acesso à conta novamente.

### Shell sem login

Uma solução comum para essa situação é definir o shell de login do usuário como `/sbin/nologin`. Se o usuário tentar fazer login no sistema diretamente, o shell `nologin` fecha a conexão.

```bash
usermod -s /sbin/nologin newapp
su - newapp
# Last login: Wed Feb  6 17:03:06 IST 2019 on pts/0
 #This account is currently not available.
```











































































