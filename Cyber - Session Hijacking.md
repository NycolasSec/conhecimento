Session Hijacking se  presume a conseguirmos sequestrar a sessão de um usuário. Normalmente de pende de algum outro ataque.

Algumas linguagens como o PHP criam sessões, criando um cookie e um arquivo com as informações da sessão em uma pasta padrão, se esses arquivos forem visíveis, poderemos roubar a sessão de algum usuário apenas trocando o nosso cookie de sessão.

![[Pasted image 20240805124445.png]]

**Pasta padrão de sessões do PHP**
`/var/lib/php/sessions`
![[Pasted image 20240805124556.png]]

