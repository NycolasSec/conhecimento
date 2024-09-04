Se um literal do tipo _char_ é fornecido como o caractere entre apóstrofos, como codificamos o próprio apóstrofo?

A linguagem “C” usa uma convenção especial que também se estende a outros caracteres, não apenas aos apóstrofos. Vamos começar com um apóstrofo de qualquer maneira. O formato correto é o seguinte:

```c
Character = '\'';
```

O ``\`` caractere (chamado _barra invertida_ ) atua como um **caractere de escape** , porque ao usar o ``\`` podemos escapar do significado normal do caractere que segue a barra. Neste exemplo, escapamos **do** papel usual do apóstrofo (ou seja, delimitar os literais do tipo ``Char``).

Você também pode usar o caractere de escape para **escapar do caractere de escape** . Sim, parece estranho, mas o exemplo abaixo deve deixar claro. É assim que colocamos uma barra invertida em uma variável do tipo `Char`:

```c
Character = '\\';
```