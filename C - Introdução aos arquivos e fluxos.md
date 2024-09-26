Qualquer programa escrito na linguagem “C” não se comunica com os arquivos **diretamente** , mas por meio de uma entidade abstrata chamada **stream** . As operações realizadas com o **fluxo abstrato** refletem as atividades relacionadas ao **arquivo físico** . Para **conectar** (vincular) o fluxo ao arquivo, você precisa realizar uma operação explícita.

A operação de conectar o fluxo com um arquivo é chamada **de abertura** do arquivo, enquanto desconectar esse link é chamado de **fechamento** do arquivo. Portanto, podemos concluir que a primeira operação realizada no fluxo é sempre _open_ e a última é _close_ . O programa é, na verdade, livre para manipular o fluxo entre esses dois eventos e para lidar com o arquivo associado. Essa liberdade é limitada, é claro, pelas características físicas do arquivo e pela maneira como o arquivo foi aberto.

## O que podemos fazer com um fluxo ?

A abertura do fluxo é associada ao arquivo e também deve declarar a maneira como o fluxo será processado. Essa declaração é chamada de **open mode** .

Existem duas operações básicas executadas no fluxo:

- **ler** do fluxo: partes de dados são **recuperadas** do arquivo e colocadas em uma área de memória gerenciada pelo programa (por exemplo, uma variável);
- **escrever** no fluxo: partes de dados da memória (por exemplo, uma variável) são **transferidas** para um arquivo.

## Como podemos abrir um fluxo ?

Existem três modos básicos usados ​​para abrir o fluxo:
- **modo de leitura** : um fluxo aberto neste modo permite apenas operações de leitura – tentar gravar no fluxo causará um erro de tempo de execução;
- **modo de gravação** : um fluxo aberto neste modo permite apenas operações de gravação – tentar ler o fluxo causará um erro de tempo de execução;
- **modo de atualização** : um fluxo aberto neste modo permite tanto escrita quanto leitura.

## Como representamos um fluxo no programa ?

O padrão da linguagem “C” pressupõe que todo ambiente de programação deve garantir a existência de um tipo chamado _FILE_ , que é usado para representar fluxos no programa. O padrão não diz nada sobre como esse tipo é declarado pois da perspectiva do programador, a maneira como o _FILE_ é declarado é completamente **irrelevante** . O programador nunca usará os dados diretamente, mas apenas por meio de funções de biblioteca.

Uma variável do tipo _FILE_ é criada quando você abre o arquivo e aniquilada no momento do fechamento. No entanto, devido às definições de funções que operam nesses dados, o programa não usa variáveis ​​do tipo _FILE_ , mas usa ponteiros para elas.

O tipo _FILE_ é definido dentro do arquivo de cabeçalho ``stdio.h``. Qualquer programa que use um stream precisa incluir este arquivo.

## Fluxos binários vs fluxos de texto



























































