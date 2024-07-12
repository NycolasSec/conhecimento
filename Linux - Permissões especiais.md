
As permissões especiais são:
- stick bit
- sgid
- suid

---

## suid
A permissão `suid` tem valor `4` e fornece para todos os usuários ter as mesmas permissões do dono do arquivo ou diretório.

```bash
chmod 4777 /sbin/shutdown
```
Assim qualquer usuário pode executar o binário `/sbin/shutdown` com as mesmas permissões do dono do arquivo.

---

## guid
A permissão `guid` tem valor `2` e fornece para todos grupos as  mesmas permissões do grupo dono do arquivo ou diretório.

```bash
chmod 2777 /sbin/shutdown
```
Assim qualquer grupo pode executar o binário `/sbin/shutdown` com as mesmas permissões do grupo dono do arquivo.

---

## stick bit
A permissão `stick bit` tem valor `1` e é representado pela letra `T` nas permissões. Ele permite que apenas o dono do arquivo modifique ou exclua os arquivos. Caso uma pasta esteja com o sticky bit definido, somente o dono pode alterar ou excluir os arquivos da pasta.

```bash
ls -l

drwxr--r--. 3 nycolas nycolas 19 jul 10 17:22 teste1
```

```bash
ls -l

drwxr--r-T. 3 nycolas nycolas 19 jul 10 17:22 teste1
```