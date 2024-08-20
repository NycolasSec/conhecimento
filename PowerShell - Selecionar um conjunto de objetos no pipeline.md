Nem todos os comandos possuem uma função interna para limitar sua saída, mas o `Select-Object` pode ser utilizado para restringir a quantidade de resultados produzidos por qualquer comando. Por exemplo, o comando `Get-EventLog` possui o parâmetro `-Newest` para exibir apenas as entradas de log mais recentes, mas quando um comando não tem essa capacidade, `Select-Object` pode ser usado para limitar a saída.

O comando `Select-Object` permite selecionar um subconjunto de objetos no pipeline com base na posição deles, como se fosse uma tabela ou planilha. Ele pode escolher um número específico de linhas, começando do início ou do final da coleção, e ignorar um número determinado de linhas antes de iniciar a seleção.

Para controlar a ordem das linhas antes da seleção, você deve usar o comando `Sort-Object`. Note que `Select-Object` não pode selecionar linhas com base em colunas ou valores de propriedades.


Por exemplo, para selecionar os primeiros 10 processos com base na menor quantidade de uso de memória virtual, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-Process | Sort-Object –Property VM | Select-Object –First 10
```

Para selecionar os últimos 10 serviços em execução e classificá-los pelo nome, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-Service | Sort-Object –Property Name | Select-Object –Last 10
```

Para selecionar os cinco processos usando a menor quantidade de processamento possível da CPU e ignorar o processo usando a CPU o mínimo possível, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-Process | Sort-Object –Property CPU –Descending | Select-Object –First 5 –Skip 1
```

## Selecionar objetos exclusivos
No `Select-Object`, se houver objetos com propriedades e valores duplicados e você usar o parâmetro `-Unique`, ele retornará apenas uma instância de cada objeto duplicado, eliminando os repetidos.

Por exemplo, se você quiser exibir informações do usuário para um usuário em cada departamento, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-ADUser -Filter * -Property Department | Sort-Object -Property Department | Select-Object Department -Unique
```