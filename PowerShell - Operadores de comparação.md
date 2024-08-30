A tabela a seguir lista os operadores básicos de comparação e o que eles significam.

##### Tabela de operadores
| Operador | Descrição        |
| -------- | ---------------- |
| **-eq**  | Igual a          |
| **-ne**  | Não igual a      |
| **-gt**  | Maior que        |
| **-lt**  | Menor que        |
| **-le**  | Menor ou igual a |
| **-ge**  | Maior ou igual a |

Por padrão eles não diferenciam maiúsculas de minúsculas, para diferenciar maiúsculas de minúsculas de cada operador pode começar com a letra **c**, por exemplo, **-ceq** e **-cne**.

O PowerShell também contém o operador **-like** e seu companheiro que diferencia maiúsculas de minúsculas, **-clike**. O operador **-like** se assemelha a **-eq**, mas dá suporte ao uso dos caracteres curinga ponto de interrogação (?) e asterisco (*) em comparações de cadeia de caracteres.

Existem outros operadores mais avançados que estão além do escopo deste curso. Essas operadores incluem:

- Os operadores **-in** e **-contains**, que testam se um objeto existe em uma coleção.
- O operador **-as**, que testa se um objeto é de um tipo especificado.
- Os operadores **-match** e **-cmatch**, que comparam uma cadeia de caracteres a uma expressão regular.

O PowerShell também contém muitos operadores que invertem a lógica da comparação, como **-notlike** e **-notin**.

