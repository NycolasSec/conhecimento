A maioria dos computadores atualmente em uso armazena ints usando **32 bits** (4 bytes); Isso significa que podemos operar o intS dentro da faixa de \[-2147483648 .. 2147483647]. 

Por essas razões, a linguagem "C" fornece alguns métodos para definir precisamente como pretendemos armazenar números grandes/pequenos. Isso permite que o compilador aloque memória, menor do que o normal (por exemplo, 16 bits em vez de 32) ou maior do que o normal (por exemplo, 64 bits em vez de 32). Também podemos declarar que garantimos que o valor armazenado na variável **não será negativo**.

Nesse caso, a largura do intervalo da variável **não muda**, mas é **deslocada** para os números positivos. Isso significa que, em vez do intervalo de -2.147.483.648 .. 2.147.483.647 obtemos o intervalo de 0 .. 4294967295.

![[Pasted image 20240904080339.png]]

Para especificar nossos requisitos de memória, podemos usar algumas palavras-chave adicionais chamadas **modificadores**:

- _long_ – é usado para declarar que precisamos de uma gama mais ampla de ints do que a padrão;
- _curto_ – é usado para determinar que precisamos de um intervalo mais estreito de ints do que o padrão;
- _unsigned_ – é usado para especificar que uma variável é necessária apenas para números não negativos; Embora possa ser surpreendente, podemos usar esse modificador junto com o tipo char; Explicaremos em quanto tempo.

Observe que alguns compiladores podem não ser capazes de distinguir dados do tipo int e long. Eles podem ser sinônimos. Se você quiser forçar o compilador a usar a representação long real para um número específico, poderá usar o tipo long long.

#### Exemplo #1:
A variável Counter usará menos bits do que o int padrão (por exemplo, pode ter 16 bits de comprimento – neste caso, o intervalo da variável será suprimido para o intervalo de [-32768 a 32767]):
```c
short int Counter;
```

A palavra int pode ser **omitida**, pois todas as declarações são consideradas como especificando int por padrão, assim:
```c
short Counter;
```

#### Example #2:
A variável ``Ants`` ocupará mais bits do que o ``int`` padrão (por exemplo, 64 bits, portanto, pode ser usada para armazenar números do intervalo de [-9223372036854775808 .. 9223372036854775807] - você pode ler números tão grandes?
```c
long long int Ants;
```

Nota – podemos **omitir** novamente a palavra _int_:
```c
long long Ants;
```

#### Exemplo #3:
Se chegarmos à conclusão de que uma variável **nunca será um valor negativo**, podemos usar o modificador ``unsigned``:
```c
unsigned int Positive;
```

Claro, podemos omitir o int como de costume:
```c
unsigned Positive;
```

#### Exemplo #4:
Também podemos misturar alguns dos modificadores – dê uma olhada:
```c
unsigned long long int HugeNumber;
```

Podemos remover a palavra int e a declaração preservará seu significado:
```c
unsigned long long HugeNumber;
```

#### Exemplo #5:
Um exemplo mais modesto está aqui:
```c
unsigned short int Lambs;
```

Sua forma equivalente é:
```c
unsigned short Lambs;
```

Os modificadores _long_ e _short_ **não devem ser usados** em conjunto com o tipo char e não devem ser usados simultaneamente em uma única declaração. Mas não há nada que nos impeça de usar o modificador _unsigned_ com uma variável do tipo char.

Não se esqueça de que não podemos omitir a palavra char. A maioria dos compiladores atualmente em uso pressupõe que os caracteressão armazenados usando 8 bits (1 byte). Isso pode ser suficiente para armazenar um valor pequeno, como o número de meses ou até mesmo o dia do mês.

Se tratarmos a variável char como um número inteiro com sinal, seu intervalo seria \[-128 .. 127]. Se não precisarmos de nenhum valor com sinal (como no exemplo abaixo), seu intervalo muda para \[0 .. 255]. Isso pode ser suficiente para muitos aplicativos e também pode resultar em economias significativas no uso de memória.

```c
unsigned char LittleCounter;
```

Até agora, usamos literais inteiros, assumindo que todos eles são do tipo int. Geralmente, esse é o caso, mas há alguns casos em que o compilador reconhece literais do tipo long. Isso acontecerá se:

- Um valor literal vai **além do intervalo aceitável** do tipo int;
- a **letra L ou l é anexada** ao literal, como ``0L`` ou ``1981l`` – ambos os literais são do tipo long.