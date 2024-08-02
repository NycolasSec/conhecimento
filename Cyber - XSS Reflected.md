XSS Reflected se refere à reflexão de um dado submetido pelo usuário sem o devido tratamento. Como um input de Nome, email o qualquer outro dado que possamos enviar um script no lugar.

```php
<?php
	if(isset($_GET['pesquisa']) and !empty($_GET['pesquisa'])){
?>
	
	<center>
		Você pesquisou por <?=$_GET['pesquisa']?>
	</center>

<?php
	}
?>


<html>

<head>...</head>

<body>
	<cemter>
		<form action="/index.php" method="get">
			<label for="pesquisar">
				Pesquisar:
			</label>
			<input type="text" name="pesquisa">
			<input type="submit" value="Pesquisar">
		</form>
	</center>
</body>

</html>
```
Nesse código não estamos retornando a entrada do usuário sem tratar o input.

![[Pasted image 20240802185627.png]]
Aqui colocamos um script JS.

![[Pasted image 20240802185637.png]]
Como previsto, o script JS foi executado.














































