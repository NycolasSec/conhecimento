Além de selecionar o número de linhas de uma coleção, `Select-Object` também permite especificar quais propriedades exibir usando o parâmetro `-Property` seguido por uma lista das propriedades desejadas. Isso é como escolher quais colunas exibir em uma tabela ou planilha, removendo as demais. Se você quiser classificar por uma propriedade mas não exibi-la, deve usar `Sort-Object` para ordenar e, em seguida, `Select-Object` para escolher as propriedades a serem exibidas.

O comando a seguir exibe uma tabela que contém o nome, a ID do processo, o tamanho da memória virtual, o tamanho da memória paginada e o uso da CPU para todos os processos em execução no computador local:
```powershell
Get-Process | Select-Object –Property Name,ID,VM,PM,CPU | Format-Table
```

O parâmetro _-Property_ funciona com o parâmetro _-First_ ou _-Last_. O comando a seguir retorna o nome e o uso da CPU para os 10 processos com a maior quantidade de uso da CPU:
```powershell
Get-Process | Sort-Object –Property CPU –Descending | Select-Object –Property Name,CPU –First 10
```

Ao usar `Select-Object`, é importante usar os nomes reais das propriedades, pois a exibição padrão do PowerShell pode rotular colunas de maneira diferente. Por exemplo, a coluna "CPU(s)" exibida pela saída de `Get-Process` na verdade corresponde à propriedade real "CPU". Para verificar os nomes reais das propriedades, você pode canalizar a saída do comando para `Get-Member`.