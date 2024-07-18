
## Definições 

Podemos começar vendo a saída do comando `ls`

![[Pasted image 20240710162209.png]]

### Primeira coluna
O primeiro character indica que tipo de arquivo é esse:

**`-`** : Arquivo regular

**`d`** : Diretório

**`b`** : Dispositivo de bloco

**`l`** : Link simbólico

**`c`** : Dispositivo de character

**`s`** : Socket

Os outros 9 characters dizem respeito as permissões do arquivo para :

**`Proprietário`** : Permissões do dono do arquivo.

**`Grupo`** : Permissões do grupo do proprietário.

**`Outros`** : Todos que não forem o **`Proprietário`** ou o **`Grupo`**

De 3 em 3 respectivamente

### Por exemplo
##### **`drwxr-xr-x`**

**`d`** (tipo do arquivo) **`rwx`** (Proprietário) **`r-x`** (Grupo) **`r-x`**(Outros)

---

**`r`** : Permissão de leitura - permite exibir o conteúdo de arquivos
**`w`** : Permissão de escrita - permite modificar arquivos
**`x`** : Permissão de execução - permite executar scripts

#### Valor octal

**`r`** : 4
**`w`** : 2
**`x`** : 1

Assim podemos definir uma permissão com base na soma desse números. Cada número representa a soma dor valor octal das permissões.

**`644`** : rw- r-- r--

---

Para definir uma permissão temos de indicar pra quem :

**`u`** : Proprietário
**`g`** : Grupo
**`o`** : Outros

---

## Definindo propriedades

Para atribuir as propriedades usamos o comando **`chmod`**

```bash
ls -l

drwxr-xr-x. 2 nycolas nycolas 6 jul 10 16:41 readme.md
```
Aqui o `Grup` tem permissão de leitura(`r`) e execução(`x).

```bash
chmod g=w readme.md
ls -l

drwx-w-r-x. 2 nycolas nycolas 6 jul 10 16:41 readme.md
```
Como usamos o `=` redefinimos as permissões para o que escolhermos. Mas podemos usar outros modos.

**`=`** : Redefine para as permissões especificadas.
**`+`** : Adiciona as permissão especificada.
**`-`** : Retira as permissões especificadas.

```bash
chmod g+r readme.md
ls -l

drwxrw-r-x. 2 nycolas nycolas 6 jul 10 16:41 readme.md
```
Adicionamos leitura para o Grupo.

```bash
chmod o+wx readme.md
ls -l

drwxrw-rwx. 2 nycolas nycolas 6 jul 10 16:41 readme.md
```
Adicionamos escrita e execução para outros.

### Modo octal

```bash
ls -l

drwxrw-rwx. 2 atylas nycolas 6 jul 10 16:41 readme.md
```
Aqui ele está com permissão `767`

```bash
chmod 644 readme.txt
ls -l

drw-r--r--. 2 atylas nycolas 6 jul 10 16:41 readme.md
```
Agora está com a permissão 644

---

## Modificando o grupo e dono de algum arquivo

```bash
ls -l
drwxrw-rwx. 2 nycolas nycolas 6 jul 10 16:41 readme.md
```

Na terceira e na quarta coluna podemos ver respectivamente o dono do arquivo e o grupo do arquivo.

Podemos alterar como comando `chown`

```bash
chown atylas:nycolas readme.md

drwxrw-rwx. 2 atylas nycolas 6 jul 10 16:41 readme.md
```

Temos que especificar nesse formato `proprietário`:`grupo`, caso deixemos um em branco, não será modificado.

---

### Recursividade

```bash
mkdir -p teste1/test2/teste3
ls -lR

drwxr-xr-x. 3 nycolas nycolas 19 jul 10 17:22 teste1

./teste1:
total 0
drwxr-xr-x. 3 nycolas nycolas 20 jul 10 17:22 test2

./teste1/test2:
total 0
drwxr-xr-x. 2 nycolas nycolas 6 jul 10 17:22 teste3

./teste1/test2/teste3:
total 0
```
Se quisermos modificar as permissões para a pasta e as subpastas, podemos usar o argumento `-R` de recursivo

```bash
chmod -R 744 teste1/
ls -lR

drwxr--r--. 3 nycolas nycolas 19 jul 10 17:22 teste1

./teste1:
total 0
drwxr--r--. 3 nycolas nycolas 20 jul 10 17:22 test2

./teste1/test2:
total 0
drwxr--r--. 2 nycolas nycolas 6 jul 10 17:22 teste3

./teste1/test2/teste3:
total 0
```
Agora a permissão 744 foi aplicada a pasta e as subpastas.

---

## umask
é uma máscara de exclusão de permissões padrão, sendo seu padrão ``0022``, ou seja, ao criarmos um arquivo ela faz contraste coma máscara de criação padrão deste arquivo.

Quando criamos uma pasta sua permissão padrão é: `0777`, mas como temos o umask de : `0022` que retira as permissões de escrita de Grupo e Outros, ficaria. `0755`


---
Agora podemos ir para [[Linux - Permissões especiais]]