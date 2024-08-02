XSS Stored se refere a injeção de comandos em alguma entrada que armazena dados em algum database. Aqui a aplicação pode até tratar a nossa entrada na hora que submetemos o script, mas quando a aplicação puxa esse script da base de dados acaba o executando.

A entrada pode ser um simples script

```html
<script>alert("Olá triggamos um XSS Stored")</script>
```
![[Pasted image 20240802191522.png]]
Aqui enviamos um script como input. 

![[Pasted image 20240802191545.png]]
Como a aplicação trata a entrada, o código não é executado.

![[Pasted image 20240802191722.png]]
Mas ao recarregarmos a página, ele executa o comando, pois o texto é tratado somente quando o usuário envia o texto.
