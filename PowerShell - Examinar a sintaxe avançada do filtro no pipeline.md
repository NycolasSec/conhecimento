A sintaxe avançada de **Where-Object** usa um script de filtro. Um _script de filtro_ é um bloco de script que contém a comparação e que você passa usando o parâmetro _-FilterScript_

Dentro desse bloco de script, você pode usar a variável interna `$PSItem` (ou`$_`, que também é válida em versões do Windows PowerShell anteriores a 3.0) para fazer referência a qualquer objeto que tenha sido canalizado para o comando. Seu script de filtro é executado uma vez para cada objeto que é canalizado para o comando. Quando o script de filtro retorna True, esse objeto é passado para baixo do pipeline como saída. Quando o script de filtro retorna False, esse objeto é removido do pipeline.

```powershell
Get-Service | Where Status –eq Running

Get-Service | Where-Object –FilterScript { $PSItem.Status –eq 'Running' }
```

O parâmetro _-FilterScript_ é posicional, e a maioria dos usuários o omite. A maioria dos usuários também usa o alias **Where** ou o alias **?**, que é ainda mais curto.

Usuários experientes do Windows PowerShell também usam a variável `$_` em vez de `$PSItem` porque só `$_` é permitida no Windows PowerShell 1.0 e no Windows PowerShell 2.0. Os comandos a seguir executam a mesma tarefa que os dois comandos anteriores:

```powershell
Get-Service | Where {$PSItem.Status –eq 'Running'}

Get-Service | ? {$_.Status –eq 'Running'}
```

## Combinando vários critérios
A sintaxe avançada permite combinar vários critérios usando os operadores boolianos, ou lógicos, **-and** e **-or**. Veja um exemplo:

```powershell
Get-EventLog –LogName Security –Newest 100 |
Where { $PSItem.EventID –eq 4672 –and $PSItem.EntryType –eq 'SuccessAudit' }
```
O operador lógico deve ter uma comparação completa em ambos os lados. No exemplo anterior, a primeira comparação verifica a propriedade **EventID**, e a segunda comparação verifica a propriedade **EntryType**.

O exemplo a seguir é aquele que muitos usuários iniciantes tentam. Está incorreto, pois a segunda comparação está incompleta:
```powershell
Get-Process | Where { $PSItem.CPU –gt 30 –and VM –lt 10000 } 
```
O problema é que **VM** não tem significado, mas `$PSItem.VM` estaria correto. 

Aqui está outro erro comum:
```powershell
Get-Service | Where { $PSItem.Status –eq 'Running' –or 'Starting' }
```

O problema é que **'Starting'** não é uma comparação completa. É apenas um valor de cadeia de caracteres, portanto `$PSItem.Status -eq 'Starting'` seria a sintaxe correta para o resultado pretendido.

## Acessando propriedades que contêm True ou False

Lembre-se de que a única finalidade do script de filtro é produzir um valor True ou False. Normalmente, você produz esses valores usando um operador de comparação, como nos exemplos que aprendeu até este ponto.

.No entanto, quando uma propriedade já contém True ou False, você não precisa fazer uma comparação explicitamente. Por exemplo, os objetos produzidos pelo **Get-Process** têm uma propriedade chamada **Responding**.

 Essa propriedade contém True ou False. Esse valor indica se o processo representado pelo objeto está respondendo ao sistema operacional no momento. Para obter uma lista dos processos que estão respondendo, você pode usar um dos seguintes comandos:

```powershell
Get-Process | Where { $PSItem.Responding –eq $True }

Get-Process | Where { $PSItem.Responding }
```

No primeiro comando, a variável de shell especial `$True` é usada para representar o valor booliano True. O segundo comando não tem nenhuma comparação, mas funciona porque a propriedade **Responding** já contém True ou False.

```powershell
Get-Process | Where { -not $PSItem.Responding }
```

No exemplo anterior, o operador lógico **-not** altera True para False e altera False para True. Portanto, se um processo não estiver respondendo, a propriedade **Responding** será False. O operador **-not** altera o resultado para True, o que faz com que o processo seja passado para o pipeline e incluído na saída final do comando.

## Acessando propriedades sem limitações

Embora a sintaxe de filtragem básica possa acessar apenas as propriedades diretas do objeto que está sendo avaliado, a sintaxe avançada não tem essa limitação. Por exemplo, para exibir uma lista de todos os serviços que têm nomes com mais de oito caracteres, use este comando:

```powershell
Get-Service | Where {$PSItem.Name.Length –gt 8}
```





