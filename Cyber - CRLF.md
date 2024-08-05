- CR = **Carriage Return** ( `\r`, `0x0D`em hexadecimal, 13 em decimal) — move o cursor para o início da linha sem avançar para a próxima linha.
- LF = **Line Feed** ( `\n`, `0x0A`em hexadecimal, 10 em decimal) — move o cursor para a próxima linha sem retornar ao início da linha.

Quando fazemos uma requisição, esses caracteres são enviados junto com o corpo da requisição, mas não são visíveis, mas podemos manipular isso na requisição.

Devemos coloca-los em sua forma hexadecimal, pois não podemos colocar `/n` ou `/r` na requisição.

- CR : %0d
- LF : %0a

![[Pasted image 20240805130812.png]]

Com isso podemos injetar informações no cabeçalho de resposta.

**Injetando um Location**
![[Pasted image 20240805131311.png]]
Aqui podemos ver 2 ``Location`` na resposta, um que é padrão e o outro que nós injetamos.

Caso coloquemos 2 LF ele entende que acabou o header e que vai começar um HTML.

























