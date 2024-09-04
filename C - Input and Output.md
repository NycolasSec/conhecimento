Veja, agora o ``printf`` é invocado com dois argumentos. O primeiro é o formato. O segundo é uma variável do tipo Inteiro. Dê uma olhada no formato – há um personagem suspeito ali:**%**. Para que serve essa porcentagem misteriosa?

Se houver um símbolo de porcentagem dentro do formato, o ``printf`` considera isso uma **dica sobre como proceder** com os argumentos seguindo o formato. A primeira dica se refere ao primeiro argumento após o formato, a segunda ao segundo, etc. (mas há algumas exceções a essa regra simples).

O **%** unido a algum outro(s) caractere(s) é às vezes chamado de **especificador** . No exemplo aqui abaixo, o formato contém um especificador:**%d**:

```
printf("Sheep counted so far: %d", SheepCounter);
```

 A letra `e` carta e indica que o argumento correspondente deve ser tratado como um valor do tipo Inteiro e enviado para a tela como um número.

Vamos dar uma olhada em uma lista abreviada de especificadores agora. A lista completa é muito mais exaustiva, enquanto a forma geral do especificador é muito mais complexa e dá mais oportunidades de lidar com a aparência e o formato dos dados apresentados.

- %d (como em **decimal** ) especifica que o argumento correspondente é um **valor do tipoInteiro**e deve ser apresentado como um número decimal de ponto fixo; seu sinônimo é um%euespecificador (como em **i** inteiro).
  
- %x(como em he**x**adecimal) especifica que o argumento correspondente é um valor do tipo Inteiro **e deve ser apresentado como um número hexadecimal** de ponto fixo .
  
- %o(como em **o**ctal) especifica que o argumento correspondente é um valor do tipo Inteiro e deve ser apresentado como um número **octal** de ponto fixo .
  
- %c(como em **c**har) especifica que o argumento correspondente é um valor do tipo Inteiro ou Caracteres e deve ser apresentado como um **personagem** .
  
- %f(como em **f**loat) especifica que o argumento correspondente é um valor do tipo flutuador e deve ser apresentado como um **número de ponto flutuante de precisão simples** .
  
- %if especifica que o argumento correspondente é um valor do tipo double e deve ser apresentado como um **número de ponto flutuante de precisão dupla** .