
No PowerShell, alguns comandos produzem saída ordenada de forma específica, como `Get-Process` e `Get-Service`, que listam resultados em ordem alfabética, ou `Get-EventLog`, que os classifica por tempo.

Se você quiser alterar essa ordem de classificação padrão, pode usar o comando `Sort-Object` (também conhecido como `sort`), que permite organizar a saída conforme sua necessidade.

## Sort-Object
O comando **Sort-Object** aceita um ou mais nomes de propriedade para usar como classificação. Por padrão, o comando classifica em ordem crescente. Se você quiser reverter a ordem de classificação, adicione o parâmetro _-Descending_.

Os comandos a seguir são exemplos de classificação:
```powershell
Get-Service | Sort-Object –Property Name –Descending
Get-Service | Sort Name –Desc
Get-Service | Sort Status,Name
```

Por padrão, o `Sort-Object` no PowerShell não diferencia maiúsculas de minúsculas ao classificar propriedades de cadeia de caracteres. No entanto, você pode ajustar a classificação para diferenciar maiúsculas e minúsculas, seguir regras culturais específicas e outras opções. Detalhes e exemplos adicionais estão disponíveis na ajuda do `Sort-Object`.

## Agrupar objetos por propriedade
Classificar objetos também possibilita exibir objetos em grupos. Os cmdlets de formatação **Format-List, Format-Table** e **Format-Wide** têm um parâmetro _-GroupBy_ que aceita um nome de propriedade. Usando o parâmetro _-GroupBy_, você pode agrupar a saída pela propriedade especificada.

Por exemplo, o comando a seguir exibe os nomes dos serviços em execução no computador local em duas listas de duas colunas agrupadas pela propriedade **Status**:

```powershell
Get-Service | Sort-Object Status,Name | fw -GroupBy Status
```

O parâmetro _-GroupBy_ funciona de forma semelhante ao comando **Group-Object**. O comando **Group-Object** aceita a entrada no pipe e fornece mais controle sobre o agrupamento dos objetos. **Group-Object** tem o alias **group**.











































