O comando **Where-Object** e seu alias, **Where**, têm duas formas de sintaxe: básica e avançada. Essas duas formas são definidas por uma extensa lista de conjuntos de parâmetros: um para cada operador de comparação

Por exemplo, para exibir uma lista somente dos serviços em execução, insira o seguinte comando no console e pressione a tecla Enter:

```powershell
Get-Service | Where Status –eq Running
```

Caso erre o nome da propriedade, ou fornecer uma propriedade inexistente, o powershell não gerará um erro; apenas não haverá saída.

## Limitações da sintaxe básica
Você pode usar a sintaxe básica para apenas uma única comparação. Você não pode, por exemplo, exibir uma lista dos serviços que são interrompidos e ter um modo inicial de **Automático**, pois isso exige duas comparações.

Você não pode usar a sintaxe básica com expressões complexas. Por exemplo, a propriedade **Name** de um objeto de serviço consiste em uma cadeia de caracteres. O PowerShell usa um objeto **System.String** para conter essa cadeia de caracteres, e um objeto **System.String** tem uma propriedade **Length**. O comando a seguir não funcionará com a sintaxe de filtragem básica:

```powershell
Get-Service | Where Name.Length –gt 5
```

A intenção é exibir todos os serviços que têm um nome maior que cinco caracteres. No entanto, esse comando nunca produzirá saída. Assim que você exceder os recursos da sintaxe básica, deverá passar para a sintaxe de filtragem avançada.

























