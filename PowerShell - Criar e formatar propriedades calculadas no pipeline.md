O `Select-Object` pode criar propriedades personalizadas ou calculadas. Essas propriedades têm um nome que o PowerShell exibe, assim como as propriedades internas. Propriedades calculadas são definidas por uma expressão que determina seu conteúdo. Para criar uma propriedade calculada, você deve fornecer os valores em uma tabela de hash.

## O que são tabelas de hash?
Uma tabela de hash, também chamada de matriz associativa ou dicionário em outras linguagens, contém pares chave-valor. No PowerShell, tabelas de hash são amplamente utilizadas. Ao criar uma tabela de hash, você pode definir suas próprias chaves e valores para atender às suas necessidades específicas.

## Tabela de hash Select-Object
Ao usar uma tabela de hash para criar propriedades calculadas usando **Select-Object**, você deve utilizar as seguintes chaves que o Windows PowerShell espera:

- **label**, **l**, **name** ou **n**. Isso especifica o rótulo, ou o nome, da propriedade calculada. Como a letra minúscula **l** se assemelha ao número 1 em algumas fontes, tente usar **name**, **n** ou **label**.
- **expression** ou **e**. Isso especifica a expressão que define o valor da propriedade calculada.

Isso significa que cada tabela de hash contém apenas um par nome-valor. No entanto, você pode usar mais de uma tabela de hash para criar várias propriedades calculadas.

Por exemplo, suponha que você queira exibir uma lista de processos que incluem o nome, a ID, o uso de memória virtual e o uso de memória paginada de cada processo. Você deseja usar os rótulos de coluna **VirtualMemory** e **PagedMemory** para as duas últimas propriedades, e deseja que esses valores sejam exibidos em bytes. Para fazer isso, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-Process | Select-Object Name,ID,@{n='VirtualMemory';e={$PSItem.VM}},@{n='PagedMemory';e={$PSItem.PM}}
```

O comando anterior inclui duas tabelas de hash e cada uma cria uma propriedade calculada. A tabela de hash pode ser mais fácil de interpretar se você a criar um pouco diferente, como mostra o código a seguir:
```powershell
@{
	n='VirtualMemory';
	e={ $PSItem.VM }
 }
```

No PowerShell, ao usar tabelas de hash para criar propriedades calculadas, você separa pares chave-valor com ponto e vírgula e define as chaves, como "n" e "e". Os rótulos (nomes) são cadeias de caracteres e devem estar entre aspas (simples ou duplas). As expressões que definem o conteúdo das propriedades são blocos de script e ficam entre chaves ({ }).

Uma vírgula separa cada propriedade, como **Name**, **ID** e cada uma das propriedades calculadas das outras na lista.

## Formatando de propriedades calculadas

Talvez você queira modificar o comando anterior para exibir os valores de memória em megabytes (MB). O PowerShell entende as abreviações **KB**, **MB**, **GB**, **TB** e **PB** como representando quilobytes, megabytes, gigabytes, terabytes e petabytes, respectivamente. Portanto, você pode modificar o comando da seguinte maneira:
```powershell
Get-Process |
Select-Object Name,
              ID,
              @{n='VirtualMemory(MB)';e={$PSItem.VM / 1MB}},
              @{n='PagedMemory(MB)';e={$PSItem.PM / 1MB}}
```

Além da formatação revisada que facilita a revisão do comando, este exemplo altera os rótulos de coluna para incluir a designação **MB**. Ele também altera as expressões para incluir uma operação de divisão, dividindo cada valor de memória por 1 MB. No entanto, os valores resultantes têm várias casas decimais, o que não é ideal, visualmente.

Para aprimorar a saída, faça a seguinte alteração:

```powershell
Get-Process |
Select-Object Name,
              ID,
              @{n='VirtualMemory(MB)';e={'{0:N2}' –f ($PSItem.VM / 1MB) -as [Double] }},
              @{n='PagedMemory(MB)';e={'{0:N2}' –f ($PSItem.PM / 1MB) -as [Double] }}
```

O operador de formatação `-f` no PowerShell substitui espaços reservados em uma cadeia de caracteres pelos valores fornecidos. No exemplo, a cadeia `'{0:N2}'` instrui o PowerShell a exibir o primeiro item como um número com duas casas decimais.

A expressão matemática que fornece o valor é colocada entre parênteses para garantir que seja executada como uma unidade. Após inserir o comando e pressionar Enter, você pode ver os resultados formatados conforme especificado.

A sintaxe no exemplo anterior pode parecer confusa porque contém muitos símbolos de pontuação. Comece com a expressão básica:
```powershell
'{0:N2}' –f ($PSItem.VM / 1MB)
```

A expressão anterior divide a propriedade **VM** por 1 MB e formata o resultado como um número com até duas casas decimais. Essa expressão é então colocada na tabela de hash:
```powershell
@{n='VirtualMemory(MB)';e={'{0:N2}' –f ($PSItem.VM / 1MB) }}
```

Essa tabela de hash cria a propriedade personalizada chamada **VirtualMemory(MB)**.




























