Quando algum site ou sistema interno redireciona um usuário sem o informar.

```html
http://corp.com

<html>
	<head>
		...
	<head>
	<body>
		<a href="/?redirect=https://google.com">CLIQUE AQUI</a>
	</body>
</html>
```

Como este redirecionamento está aberto poderíamos trocar a url e redirecionar o alvo para um site de nosso controle. É necessário engenharia social para ter sucesso nesse ataque, mas como o domínio do ling de ataque serio de uma empresa legítima, é improvável que alguém desconfie.

<input style="width:100%;padding:5px;font-size:18pt;" value="http://corp.com/?redirect=http://evil.com">

Nesse caso ele redirecionaria o usuário para o site ``http://evil.com``