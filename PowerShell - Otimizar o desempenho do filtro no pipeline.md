A maneira como você cria os seus comandos pode ter um efeito significativo sobre o desempenho. Imagine que você tem um contêiner de blocos plásticos. Cada bloco é vermelho, verde ou azul. Cada bloco tem uma letra do alfabeto impressa nele. Você precisa colocar todos os blocos vermelhos em ordem alfabética. O que você faria primeiro?

Considere criar essa tarefa como um comando Windows PowerShell usando o comando fictício **Get-Block**. Qual dos dois exemplos a seguir você acha que será mais rápido?

```powershell
Get-Block | Sort-Object –Property Letter | Where-Object –FilterScript { $PSItem.Color –eq 'Red' }

Get-Block | Where-Object –FilterScript { $PSItem.Color –eq 'Red' } | Sort-Object –Property Letter
```

O segundo comando será mais rápido, pois remove os blocos indesejados do pipeline para que apenas os blocos restantes sejam classificados. O primeiro comando classifica todos os blocos e depois remove muitos deles. Isso significa que grande parte do esforço de classificação foi desperdiçado.

Muitos scripts do PowerShell usam um _mnemônico_, que é uma frase que serve como um lembrete simples, para ajudá-los a se lembrar de fazer a coisa certas quando estiverem otimizando o desempenho. A frase é _filtrar à esquerda_ e significa que qualquer filtragem deve ocorrer tanto à esquerda quanto possível ou o mais próximo possível do início da linha de comando.

Às vezes, mover a filtragem o mais para a esquerda possível significa que você não usará **Where-Object**. Por exemplo, o comando **Get-ChildItem** pode produzir uma lista que inclui arquivos e pastas. Cada objeto produzido pelo comando tem uma propriedade chamada **PSIsContainer**. Ela contém True se o objeto representa uma pasta e False se o objeto representa um arquivo. O comando a seguir produzirá uma lista que inclui apenas arquivos:

```powershell
Get-ChildItem | Where { -not $PSItem.PSIsContainer }
```

No entanto, essa não é a maneira mais eficiente de produzir o resultado. O comando **Get-ChildItem** tem um parâmetro que limita a saída do comando:

```powershell
Get-ChildItem -File
```
Quando for possível, verifique os arquivos de ajuda para obter os comandos que você usa, para ver se eles contêm um parâmetro que pode fazer a filtragem que você deseja.
























