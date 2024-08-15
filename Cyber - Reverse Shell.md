### Reverse Shell
[[Cyber - Reverse Shell na mão]]

---
### Melhorando a Reverse Shell
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
Melhora a shell

```sh
script -qc /bin/bash /dev/null
```
Quando nos conectamos, o shell não é muito explícito, com esse comando ele fica mais completo.

---
### Sites de reverse shell
- **`Pentestmonkey`** : https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
- **`Gerador`** : https://www.revshells.com/
- **`Melhorzin`** : https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#python
- https://r.0x7359.com/10.0.20.5:1337
- https://www.youtube.com/watch?v=ljyXvn23BkM

