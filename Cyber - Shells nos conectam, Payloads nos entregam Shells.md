![[nonono.gif]]

A é um programa que fornece ao usuário de computador uma interface para inserir instruções no sistema e exibir a saída de texto. Um shell geralmente é o resultado da exploração de uma vulnerabilidade ou do desvio de medidas de segurança para obter acesso interativo a um host.

## Por que obter um shell?

o shell nos dá acesso direto ao `OSsystem`, `system commands` e `file system`. Portanto, se obtivermos acesso, podemos começar a enumerar o sistema em busca de vetores que podem nos permitir escalar privilégios, dinamizar, transferir arquivos e muito mais.

Estabelecer um shell também nos permite manter a persistência no sistema, dando-nos mais tempo para trabalhar. É importante notar que estabelecer um shell quase sempre significa que estamos acessando a CLI do SO, e isso pode nos tornar mais difíceis de perceber do que se estivéssemos acessando remotamente um shell gráfico por VNC ou RDP .

Outro benefício significativo de se tornar habilidoso com interfaces de linha de comando é que elas podem ser harder to detect than graphical shells, faster to navigate the OS, e easier to automate our actions. Vemos os shells através das lentes das seguintes perspectivas ao longo deste módulo:

|**Perspectiva**|**Descrição**|
|---|---|
|`Computing`|O ambiente userland baseado em texto que é utilizado para administrar tarefas e enviar instruções em um PC. Pense em Bash, Zsh, cmd e PowerShell.|
|`Exploitation` `&` `Security`|Um shell é frequentemente o resultado da exploração de uma vulnerabilidade ou do desvio de medidas de segurança para obter acesso interativo a um host. Um exemplo seria acionar [o EternalBlue](https://www.cisecurity.org/wp-content/uploads/2019/01/Security-Primer-EternalBlue.pdf) em um host Windows para obter acesso ao cmd-prompt em um host remotamente.|
|`Web`|Isso é um pouco diferente. Um web shell é muito parecido com um shell padrão, exceto que ele explora uma vulnerabilidade (geralmente a capacidade de carregar um arquivo ou script) que fornece ao invasor uma maneira de emitir instruções, ler e acessar arquivos e potencialmente executar ações destrutivas no host subjacente. O controle do web shell geralmente é feito chamando o script dentro de uma janela do navegador.|

## Payloads nos entregam Shells
Dentro do setor de TI como um todo, um `payload` pode ser definido de algumas maneiras diferentes:

- `Networking`:A porção de dados encapsulados de um pacote que atravessa redes de computadores modernas.
- `Basic Computing`: Um payload é a porção de um conjunto de instruções que define a ação a ser tomada. Cabeçalhos e informações de protocolo removidos.
- `Programming`: A porção de dados referenciada ou transportada pela instrução da linguagem de programação.
- `Exploitation & Security`: Um payload é criado por código com a intenção de explorar uma vulnerabilidade em um sistema de computador. O termo payload pode descrever vários tipos de malware, incluindo, mas não se limitando a, ransomware.























































