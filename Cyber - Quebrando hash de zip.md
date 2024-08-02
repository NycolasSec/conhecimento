Primeiro devemos obter a hash do arquivo zip.
```bash
zip2john backup.zip > hashes
```

Depois de obtermos as hashes podemos quebrá-las
```bash
john -wordlist=/usr/share/wordlists/rockyou.txt hashes
```
