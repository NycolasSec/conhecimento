## Ajuda de Get-EventLog
Use a ajuda para **Get-EventLog** como exemplo. Se você inserir o comando **Get-Help Get-EventLog** no console e pressionar a tecla Enter, a ajuda retornará a seguinte sintaxe:

```powershell
Get-EventLog [-LogName] <String> [[-InstanceId] <Int64[]>] [-After <DateTime>] [-AsBaseObject] [-Before <DateTime>] [-ComputerName <String[]>] [-EntryType {Error | Information | FailureAudit | SuccessAudit | Warning}] [-Index <Int32[]>] [-Message <String>] [-Newest <Int32>] [-Source <String[]>] [-UserName <String[]>] [<CommonParameters>]

Get-EventLog [-AsString] [-ComputerName <String[]>] [-List] [<CommonParameters>]

```
Os dois blocos de texto são _conjuntos de parâmetros_, e cada um deles representa uma maneira de executar o comando.

Não é possível misturar e corresponder parâmetros entre conjuntos. Ou seja, se você decidir usar o parâmetro _–List_, também não poderá usar _–LogName_, pois esses parâmetros não aparecem juntos no mesmo conjunto de parâmetros.

O nome do parâmetro _–LogName_ está entre colchetes, o que significa que é um _parâmetro posicional_. Não é possível executar o comando sem um nome de log.

No entanto, você não precisa inserir o nome do parâmetro –_LogName_. Você precisa passar a cadeia de caracteres de nome de log como o primeiro parâmetro, pois essa é a posição no arquivo de ajuda em que o parâmetro _–LogName_ aparece.

```powershell
Get-EventLog –LogName Application
Get-EventLog Application
```


## Omitindo nomes de parâmetros
Tenha cuidado ao omitir nomes de parâmetros, por alguns motivos. Você não pode omitir todos os parâmetros. Por exemplo, o parâmetro _-ComputerName_ não pode ter o nome do parâmetro omitido.

Quando você fornece nomes de parâmetros, os parâmetros podem aparecer em qualquer ordem, como o seguinte comando ilustra:

```powershell
Get-EventLog –ComputerName LON-DC1 –LogName Application –Newest 10
```

No entanto, ao omitir nomes de parâmetros, você deve garantir a inserção dos parâmetros na ordem correta. Por exemplo, o comando a seguir não funcionará porque o valor do nome do log não aparece na posição correta:

```powershell
Get-EventLog –ComputerName LON-DC1 Application
```

## Especificando vários valores
Alguns parâmetros aceitam mais de um valor. Na seção **SYNTAX**, uma notação de colchete duplo no tipo de valor do parâmetro designa esses parâmetros. Por exemplo:

```powershell
-ComputerName <string[]>
```

A sintaxe acima indica que o parâmetro _–ComputerName_ pode aceitar um ou mais valores de cadeia de caracteres. Uma maneira de especificar vários valores é usando uma lista separada por vírgulas. Você não precisa colocar os valores entre aspas, a menos que os próprios valores contenham uma vírgula ou espaço em branco, como um caractere de espaço ou de tabulação.

```powershell
Get-EventLog –LogName Application –ComputerName LON-CL1,LON-DC1
```






