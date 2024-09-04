A linguagem “C” nos permite escapar em outras circunstâncias também. Vamos começar com aquelas que denotam literais representando espaços em branco.

``\n`` – denota uma **transição para uma nova linha** e às vezes é chamado de **LF** ( **Line Feed** ), pois as impressoras reagem a esse caractere puxando o papel uma linha de texto.

``\r`` – denota o **retorno ao início da linha** e às vezes é chamado de **CR** ( **Carriage Return** – “carriage” era sinônimo de “cabeça de impressão” na era da máquina de escrever); as impressoras respondem a esse caractere como se fossem instruídas a reiniciar a impressão a partir da margem esquerda da linha já impressa.

``\a`` (como em **alarm** ) é uma relíquia do passado quando teletipos eram frequentemente usados ​​para se comunicar com computadores (você sabe o que é um teletipo, você tem idade suficiente para se lembrar deles?); enviar este caractere para um teletipo liga seu toque; portanto, o caractere é oficialmente chamado de **BEL** (como em **bell** ); curiosamente, se você tentar enviar o caractere para a tela, você ouvirá um som – não será um toque real, mas sim um bipe curto. O poder da tradição funciona até mesmo no mundo da TI.

``\0``( nota: o caractere depois da barra invertida é um zero, não a letra _O_ ) chamado **null** (do latim **nullus** – nenhum) é um caractere que **não representa nenhum caractere** ; apesar das primeiras impressões, pode ser muito útil, como mostraremos nas próximas lições.

Agora tentaremos escapar em uma direção um pouco diferente.

O primeiro exemplo explica a variante quando uma barra invertida é seguida por dois ou três **dígitos octais** (os dígitos do intervalo de 0 a 7). Um número codificado dessa maneira será tratado como um **valor ASCII** .

Pode parecer assim:
```c
Character = '\47';
```

``047`` octal é ``39`` decimal. Observe a tabela de códigos ASCII e você verá que este é o código ASCII de um apóstrofo, então isso é equivalente à notação

```c
'\''
```
(mas apenas para computadores que implementam o código ASCII).

A segunda fuga refere-se à situação em que um ``\`` é seguido pela letra ``X`` (minúsculas ou maiúsculas – não importa). Neste caso, deve haver um ou dois **dígitos hexadecimais** , que serão tratados como código ASCII. Aqui está um exemplo:

```c
Character = '\x27';
```
Como você provavelmente já adivinhou, ``27`` hexadecimal é ``39`` decimal.








