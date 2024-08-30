Você sabe como os computadores realizam cálculos em números? Talvez você já tenha ouvido falar do **sistema binário** e saiba que é o sistema que os computadores usam para armazenar números, e que eles podem executar qualquer operação sobre eles. Não vamos explorar as complexidades dos sistemas numéricos posicionais aqui, mas diremos que os números manipulados pelos computadores modernos são de dois tipos:

- **inteiros** , ou seja, aqueles que são desprovidos da parte fracionária;
- **números de ponto flutuante** (ou simplesmente floats), que contêm (ou são capazes de conter) a parte fracionária.

Esta definição não é totalmente precisa, mas é boa o suficiente para nossos propósitos. Esta distinção é muito importante e o limite entre esses dois tipos de números é **muito estrito** . Ambos os tipos de números diferem significativamente em como são **armazenados** na memória de um computador e no intervalo de valores aceitáveis. Além disso, a característica de um número que determina seu tipo, intervalo e aplicação é chamada de **tipo** .

Neste ponto, fizemos amizade com dois tipos da linguagem “C” – um **tipo inteiro** (conhecido como **_int_** ) e um **tipo de ponto flutuante** (conhecido como **_float_** ).

Por enquanto, vamos deixar os números de ponto flutuante de lado (voltaremos a eles em breve) e vamos considerar a questão, talvez um pouco banal à primeira vista, de como a linguagem “C” reconhece números inteiros.

![[Pasted image 20240829192216.png]]

Bem, é quase da mesma forma que você está acostumado a escrevê-los com um lápis no papel – é simplesmente uma sequência de dígitos que compõem o número. Mas há uma ressalva – você não deve inserir nenhum caractere que não seja um dígito dentro do número. Tome como exemplo o número onze milhões cento e onze mil cento e onze. Se você pegasse um lápis na mão agora mesmo, escreveria o número assim:

![[Pasted image 20240829192401.png]]

Obviamente, isso facilita a leitura se o número for composto por muitos dígitos. No entanto, em “C” é proibido. Você **deve** escrever esse número da seguinte forma:

![[Pasted image 20240829192420.png]]

Caso contrário, você se exporia a alguns comentários mordazes do compilador. Como codificamos números negativos em “C”? Como de costume – adicionando um menos. Você pode escrever:

![[Pasted image 20240829192444.png]]

Números positivos não precisam ser precedidos pelo sinal de mais, mas você pode fazer isso se quiser. As linhas a seguir descrevem o mesmo número:

![[Pasted image 20240829192459.png]]

Existem duas convenções adicionais, desconhecidas para o mundo da matemática. A primeira nos permite usar os números em uma representação octal. Se um número inteiro for precedido pelo dígito _0_ , ele será tratado como um **valor octal** . Isso significa que o número deve conter dígitos retirados apenas do intervalo _[0..7] ._

![[Pasted image 20240829192533.png]]

é um número octal com valor decimal igual a 83.

![[Pasted image 20240829192552.png]]

é um **número hexadecimal** com valor decimal igual a 291.

Talvez você queira ver o resultado do seu cálculo. Discutiremos isso mais tarde, mas agora é um bom momento para mencionar como imprimir o valor de um número.

Bem, para imprimir um número inteiro, você deve usar (esta é apenas uma forma simples):

```
print("Hello world!")
```

Para imprimir um número de ponto flutuante, você deve usar (este é apenas um formulário simples):

```
printf("%f\n", FloatNumberOrExpression);
```

Em ambos os casos, você deve primeiro incluir o arquivo de cabeçalho stdio (como fizemos no primeiro programa):

```
#include <stdio.h>
```












































































































































































