O comando **Measure-Object** pode aceitar qualquer tipo de objeto em uma coleção. Por padrão, o comando conta o número de objetos na coleção e produz um objeto de medida que inclui a contagem.

>  O comando **Measure-Object** tem o alias **Measure**.

O parâmetro _-Property_ do **Measure-Object** permite que você especifique uma única propriedade, que deve conter valores numéricos. Em seguida, você pode incluir os parâmetros _-Sum_, _-Average_, _-Minimum_ e _-Maximum_ para calcular esses valores agregados para a propriedade especificada.

> O PowerShell permite que você _trunque_ um nome de parâmetro ou use apenas uma parte do nome do parâmetro, se o nome truncado identificar claramente o parâmetro. Você observará frequentemente os parâmetros _–Sum_, _-Minimum_ e _–Maximum_ truncados para _-Sum_, _-Min_ e _-Max_, correspondentes às abreviações comuns em inglês para essas palavras. No entanto, você não pode reduzir _-Average_ para _-Avg_, embora iniciantes frequentemente tentem fazer isso. Você pode reduzir o parâmetro _-Average_ para _-Ave_, que é um truncamento válido do nome.

O comando a seguir conta o número de arquivos em uma pasta e exibe os menores, maiores e a média de tamanhos de arquivo:

```powershell
Get-ChildItem -File | Measure -Property Length -Sum -Average -Minimum -Max
```