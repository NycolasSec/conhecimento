Primeiro, definimos nossas expectativas para o programa. Elas serão bem modestas. Queremos que um texto curto e sem sentido apareça na tela. Vamos supor que o texto deve proclamar ao mundo:

```
It's me, your first program.
```

Não esperamos mais nada dele até agora.

Quais passos adicionais nosso primeiro programa deve executar? Vamos tentar enumerá-los aqui:

1. para **começar** ;
2. para **escrever** o texto na tela;
3. parar**​**

Esse tipo de descrição estruturada e semiformal de cada etapa do programa é chamado de **algoritmo** . As fontes dessa palavra podem ser rastreadas até a língua árabe, e ela se originou no início dos tempos medievais, o que pode ser o pretexto para notar que os primórdios da programação de computadores estão em tempos muito antigos.

Agora é hora de ver nosso programa no editor:
```c
#include <stdio.h>

int main(void)
{
	puts("It's me, your first program.");
    return 0;
}
```

Parece um pouco misterioso, não é? Agora, vamos olhar cuidadosamente para cada linha do programa, explicando seu significado e propósito. A descrição não é particularmente precisa e aqueles que conhecem a linguagem “C” provavelmente já concluiriam que é muito simplista e um tanto infantil. Fizemos isso de propósito – não é nossa intenção construir Roma em um dia.

Vamos começar.

Preste atenção ao personagem#(hash) no início da primeira linha. Isso significa que o conteúdo desta linha é uma **diretiva do pré-processador** . Vamos falar mais sobre o **pré-processador** um pouco mais tarde, mas por enquanto vamos apenas dizer que é uma parte separada do compilador, cuja tarefa é pré-ler o texto do programa e fazer algumas modificações nele. O prefixo “pre” sugere que essas operações são realizadas **antes que** o processamento completo (compilação) ocorra.

As mudanças que o **pré-processador** introduz são controladas inteiramente por suas **diretivas** . No programa de exemplo, estamos lidando com a diretiva include. Quando o pré-processador encontra essa diretiva, ele substitui a diretiva pelo conteúdo do arquivo cujo nome está listado na diretiva (no nosso caso, este é o arquivostdio.h). Nota – as alterações feitas pelo pré-processador nunca modificam o conteúdo do seu arquivo de origem de forma alguma. Quaisquer alterações são feitas em uma cópia volátil do seu programa, que desaparece imediatamente após o compilador terminar seu trabalho.

Você pode perguntar por que queremos que o pré-processador inclua o conteúdo de um arquivo completamente desconhecidostdio.h. Escrever um programa é semelhante a construir um prédio com blocos prontos. Em nosso programa, usaremos esse bloco e o usaremos quando quisermos escrever algo na tela. Esse bloco é chamado puts (você pode encontrá-lo dentro do nosso código), mas o compilador não sabe nada sobre ele até agora. Em particular, o compilador não tem ideia de que puts é um nome válido para esse bloco, enquanto puts não é. O compilador precisa estar ciente disso. Essas informações preliminares necessárias ao compilador estão incluídas nos arquivos cujos nomes geralmente terminam com “ _.h_ ” ( **header** ). Esses arquivos são comumente chamados **de arquivos de cabeçalho** .

O arquivo **stdio.h** (definido pelo padrão da linguagem “C”) contém uma coleção de informações preliminares sobre blocos prontos que podem ser usados ​​por um programa para escrever texto na tela ou ler letras do teclado. Então, quando nosso programa vai escrever algo, ele obviamente usará um bloco chamado puts, que é capaz de fazer o truque. Não queremos que o compilador seja surpreendido, então devemos avisá-lo sobre isso. Os desenvolvedores do compilador colocam um conjunto dessas informações antecipatórias no arquivo stdio.h. Só precisamos usar o arquivo. Isso é exatamente o que esperamos doincluirdiretiva.

Você também pode perguntar onde ostdio.harquivo está localizado. A resposta é simples, mas não tão precisa quanto você pode querer – mas esse não é o nosso problema agora. O pré-processador sabe onde ele está. Retornaremos ao problema quando começarmos a história detalhada do pré-processamento.

  
Já mencionamos algo sobre **blocos** . Vamos nos aprofundar um pouco mais agora. Um dos tipos mais comuns de blocos usados ​​para construir programas em “C” são **as funções** . Se você entende funções apenas em um sentido puramente matemático, esta ainda é uma boa pista. Imagine uma função como uma **caixa preta** , onde você pode inserir algo nela (nem sempre necessário) e tirar algo novo dela como se fosse de um chapéu mágico. As coisas que são colocadas na caixa são chamadas de **argumentos de função (ou** **parâmetros** de função ). As coisas que são retiradas da caixa são chamadas de **resultados** de função . Além disso, uma função pode fazer outra coisa paralelamente. Se isso soar um tanto vago, não se preocupe, falaremos sobre funções muitas vezes com mais detalhes.

Vamos retornar ao nosso programa. O padrão da linguagem “C” assume que, entre os muitos blocos diferentes que podem ser colocados em um programa, um bloco específico deve sempre estar presente, caso contrário o programa não estará correto. Este bloco é sempre uma função de mesmo nome:principal.

Cada função em “C” começa com o seguinte conjunto de informações:

- qual é o **resultado** da função?
- qual é o **nome** da função?
- Quantos **parâmetros** a função tem e quais são seus **nomes** ?

Dê uma olhada no nosso programa e tente lê-lo corretamente, aceitando o fato de que você pode não entender tudo ainda.

```c
#include <stdio.h>

int main(void)
{
	puts("It's me, your first program.");
    return 0;
}
```

- o **resultado** da função é um **valor inteiro** (nós o lemos da palavra ``int`` que é a abreviação de _inteiro_ )
- o **nome** da função é `main`(já sabemos o porquê)
- a função **não requer nenhum parâmetro** (que lemos da palavra `void`)
- 
Um conjunto de informações como esse às vezes é chamado de **protótipo** e é como um rótulo afixado a uma função, anunciando como podemos usar essa função em seu programa. O protótipo não diz nada sobre a finalidade da função. Ele é escrito dentro da função e o interior da função é chamado de **corpo** da função . O corpo da função começa onde o primeiro colchete de abertura{é colocado e termina onde o colchete de fechamento correspondente}é colocada. Pode parecer surpreendente, mas o corpo da função pode estar vazio – isso significa apenas que a função não faz nada.

```c
void lazy(void) { }
```































