O PowerShell fornece várias maneiras de controlar a formatação da saída do pipeline. A formatação padrão da saída depende dos objetos existentes na saída e dos arquivos de configuração que definem a saída. Depois que o PowerShell decide sobre o formato apropriado, ele passa a saída para um conjunto de cmdlets de formatação sem a sua interferência.

Os cmdlets de formatação são:
- **Format-List**
- **Format-Table**
- **Format-Wide**
- **Format-Custom**

Você pode substituir a formatação de saída padrão especificando qualquer um dos cmdlets anteriores como parte do pipeline.

Todos os cmdlets de formatação aceitam o parâmetro _-Property_. O parâmetro _-Property_ aceita uma lista separada por vírgulas de nomes de propriedades e, em seguida, filtra a lista de propriedades que são exibidas e a ordem na qual são exibidas. Tenha em mente que, quando você especificar nomes de propriedades para esse parâmetro, o comando original deverá ter retornado essas propriedades.

Por exemplo, o cmdlet **Get-ADUser** retorna apenas um subconjunto de propriedades, a menos que você especifique o respectivo parâmetro _-Properties_. Portanto, se você especificar a propriedade **Cidade** no parâmetro _-Property_ para um cmdlet de formatação, ela será exibida como se a propriedade não estivesse definida, a menos que você verifique que a propriedade **Cidade** é uma das propriedades retornadas para os usuários consultados.

#### Format-List
O cmdlet **Format-List**, como o nome sugere, formata a saída de um comando como uma lista simples de propriedades, em que cada propriedade é exibida em uma nova linha. Se o comando que passa a saída para **Format-List** retornar vários objetos, será exibida uma lista separada de propriedades para cada objeto.

O alias do cmdlet **Format-List** é **fl**.

```powershell
Get-Process | Format-List 
```

#### Format-Table
O cmdlet **Format-Table** formata a saída como uma tabela, em que cada linha representa um objeto e cada coluna representa uma propriedade.

A formatação da tabela depende dos objetos retornados. Você pode modificar essa formatação usando uma variedade de parâmetros, como:

- _-AutoSize_. Este parâmetro ajusta o tamanho da coluna e o número de colunas com base na largura dos dados. No Windows PowerShell 5.0 e mais recentes, _-AutoSize_ é definido como **true** por padrão. Em versões mais antigas do Windows PowerShell, os valores padrão podem truncar dados na tabela.
- _-HideTableHeaders_. Este parâmetro remove os cabeçalhos da tabela da saída.
- _-Wrap_. Este parâmetro faz com que o texto mais largo que a largura da coluna seja quebrado para a próxima linha.

Para exibir as propriedades **Name**, **ObjectClass** e **Description** para todos os objetos do Windows Server Active Directory como uma tabela, com as colunas definidas para dimensionar e quebrar automaticamente o texto, insira o seguinte comando no console e pressione a tecla Enter:

O alias do cmdlet **Format-Table** é **ft**.

```powershell
Get-ADObject -filter * -Properties * | ft -Property Name, ObjectClass, Description -AutoSize -Wrap
```

#### Format-Wide
A saída do cmdlet **Format-Wide** é uma única propriedade em uma única lista exibida em várias colunas. Esse cmdlet funciona como o parâmetro _/w_ do comando **dir** em **cmd.exe**. O formato largo é útil para exibir grandes listas de dados simples, como os nomes de arquivos ou processos, em um formato compacto.

Por padrão, **Format-Wide** exibe a saída em duas colunas. Você pode modificar o número de colunas usando o parâmetro _-Column_. O parâmetro _-AutoSize_, que funciona da mesma maneira como funciona para **Format-Table**, também está disponível. No entanto, não é possível usar _-AutoSize_ e _-Column_ juntos. O parâmetro _-Property_ também está disponível, mas no caso de **Format-Wide**, ele pode aceitar apenas um nome de propriedade.

O alias do cmdlet **Format-Wide** é **fw**.

```powershell
Get-GPO -all | fw -Property DisplayName -Column 3
```
