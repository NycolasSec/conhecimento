Em alguns casos teremos que dar permissão para um grupo ou usuário sobre um arquivo ou uma pasta, mas não poderemos adicioná-los como donos do arquivo. Para resolver esse problema podemos usar uma ACL, assim podemos dar privilégios específicos a um grupo ou usuário.

### Sintaxe
`setfacl -m [u,g,o]:[nome do usuário/grupo]:[permissão] [arquivo]`
`setfacl -x [u,g,o]:[nome do usuário/grupo] [arquivo]`

`getfacl [arquivo]`

**`setfacl`** : Adiciona ou remove uma ACL em um arquivo ou pasta.
**`getfacl`** : Retorna as ACLs setadas em um arquivo ou pasta

**`m`** : Adiciona uma ACL.
**`x`** : Remove uma ACL - Não precisa especificar as permissões.

**`u`** : Ajusta permissão de um usuário qualquer.
**`g`** : Ajusta permissão de um grupo qualquer.
**`o`** : Ajusta permissão de todos os outros usuários - Não precisamos especificar usuário ou grupo.

### Exemplos
```bash
setfacl -m u:davi:r-x info.txt
setfacl -m g:ti:rwx info.txt
setfacl -m u:atylas:r-- info.txt
```

```bash
getfacl info.txt
```
![[Pasted image 20240712173044.png]]

O símbolo de `+` nas permissões indica que ACL está setada.
```bash
ls -lha
```
![[Pasted image 20240712173356.png]]

## Mais exemplos

```bash
setfacl -m o::x info.txt
```
