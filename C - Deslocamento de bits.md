A linguagem "C" oferece ainda outra operação relacionada a bits únicos: **deslocamento**. Isso é aplicado apenas a valores **inteiros** e você não deve usar floats.

A mudança de bits pode ser:
- Lógico, se todos os bits da variável forem deslocados; a mudança ocorre quando você a aplica aos inteiros sem sinal;
- Aritmética, se o deslocamento omite o bit de **sinal** - na notação complementar de dois, o papel do **bit de sinal** é desempenhado pelo **bit mais alto** de uma variável; se for igual a "1", o valor é tratado como negativo; Isso significa que o deslocamento aritmético não pode alterar o sinal do valor deslocado.

Os operadores de deslocamento na linguagem "C" são um par de dígrafos, << e >>:

**Valor << Bits** , **Valor >> Bits**

sugerindo claramente em que direção a mudança agirá. O argumento esquerdo desses operadores é o valor inteiro cujos bits são deslocados. O argumento a direita determina o tamanho do deslocamento.

A prioridade desses operadores é muito alta. Você os verá na tabela atualizada de prioridades que mostraremos no final desta seção.

Vamos supor que as seguintes declarações existam:
```c
int Signed = -8, VarS;
unsigned Unsigned = 6, VarU;
```

Dê uma olhada nessas mudanças, elas são mostradas no editor:
```c
/* equivalent to division by 2 –> VarS == -4 */
VarS = Signed >> 1;

/* equivalent to multiplication by 4 –> VarS == -32 */
VarS = Signed << 2;

/* equivalent to division by 4 –> VarU == 1 */
VarU = Unsigned >> 2;

/* equivalent to multiplication by 2 –> VarU == 12 */
VarU = Unsigned << 1;
```

Ambos os operadores podem ser usados na forma de atalho conforme abaixo:
```c
Signed >>= 1; /* division by 2 */
Unsigned <<= 1; /* multiplication by 2 */
```

E aqui está a tabela de prioridade atualizada, contendo todos os operadores apresentados nesta seção.

|                                             |             |
| ------------------------------------------- | ----------- |
| !, ~ (tipo), ++, --, +, -                   | **Unário**  |
| *, /, %                                     |             |
| +, -                                        | **binário** |
| <<, >>                                      |             |
| <, <=, >, >=                                |             |
| ==, !=                                      |             |
| &                                           |             |
| \|                                          |             |
| &&                                          |             |
| \|,                                         |             |
| =, +=, -=, *=, /=, %=, &=, ^=, \|, >>=, <<= |             |














