Vamos começar nossa discussão com as questões relacionadas ao **nome** de uma variável . Variáveis ​​não aparecem em nosso programa de alguma forma mágica. Nós (como desenvolvedores) decidimos quantas e quais variáveis ​​queremos que existam em nosso programa. Também damos a elas seus nomes, quase nos tornando seus padrinhos. Se você quiser dar um nome a uma variável, você deve seguir algumas regras rígidas:

- o nome da variável deve ser composto por **letras latinas maiúsculas ou minúsculas, dígitos e o caractere** _ ( _sublinhado_ );
- o nome da variável deve **começar com uma letra** ;
- o **caractere sublinhado é uma letra** (estranho, mas verdadeiro);
- letras maiúsculas e minúsculas são tratadas como **diferentes** (um pouco diferente do que no mundo real – Alice e ALICE são os mesmos nomes, mas são dois nomes de variáveis ​​diferentes, consequentemente, duas variáveis ​​diferentes);

![[Pasted image 20240829192832.png]]


Observe que as mesmas restrições se aplicam aos **nomes de funções** .

O padrão da linguagem “C” não impõe restrições ao comprimento de nomes de variáveis, mas um compilador específico pode ter uma opinião diferente sobre esse assunto. Não se preocupe; geralmente a limitação é definida tão alta que é improvável que você realmente queira usar nomes de variáveis ​​(ou funções) tão longos.

  

Aqui estão alguns nomes de variáveis **​​corretos** , mas nem sempre convenientes:

- variable
- i
- t10
- Exchange_Rate
- counter
- DaysToTheEndOfTheWorld
- TheNameOfAVariableWhichIsSoLongThatYouWillNotBeAbleToWriteItWithoutMistakes
- _

O último nome em particular pode levantar preocupações, mas do ponto de vista do compilador não há nada de errado com ele.

E agora alguns nomes **incorretos** :

- 10t (não começa com uma letra)
- Adiós_Señora (contém caracteres ilegais)
- Exchange Rate (contém um espaço)















































