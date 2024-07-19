Um shell restrito limita o conjunto de comandos que um usuário pode executar e os diretórios onde pode operar. Esses shells são usados para criar ambientes seguros, restringindo o acesso e as ações dos usuários para proteger o sistema. Exemplos incluem o `rbash` no Linux e o "Shell de acesso restrito" no Windows.

**RBASH**
	- O **Bourne shell restrito (rbash)** é uma versão limitada do Bourne shell no Linux.
	- Ele restringe o uso de certos recursos, como mudar de diretório, definir variáveis de ambiente e executar comandos fora de diretórios específicos.
	- É usado para criar um ambiente seguro e controlado para usuários, minimizando o risco de danos acidentais ou intencionais ao sistema.

**RKSH**
	- O **Korn shell restrito (rksh)** é uma versão limitada do Korn shell, que restringe o uso de recursos como executar comandos fora de diretórios específicos, criar ou modificar funções do shell e alterar o ambiente do shell.
	- Ele é projetado para fornecer um ambiente mais controlado e seguro para o usuário.

**RZSH**
	- O shell Z restrito (rzsh) é uma versão limitada do shell Z, projetado para restringir o uso de certos recursos como execução de scripts, definição de aliases e modificação do ambiente do shell.
	- Administradores o utilizam para criar ambientes controlados, protegendo o sistema de usuários que poderiam causar danos acidentais ou intencionais.
	- Em uma rede corporativa, diferentes tipos de shells restritos (rbash, rksh, rzsh) são atribuídos a usuários com base no nível de acesso necessário: parceiros externos, contratados e funcionários. Isso garante que os usuários acessem apenas os recursos necessários e reduz o risco para o sistema.
	- Métodos para escapar de um shell restrito incluem explorar vulnerabilidades do próprio shell ou usar técnicas para contornar as restrições impostas.

## Escapando

Em alguns casos, é possível escapar de um shell restrito injetando comandos através da linha de comando ou outras entradas aceitas pelo shell. Por exemplo, se o shell permite a execução de comandos ao passar argumentos para comandos internos, pode-se injetar comandos adicionais nos argumentos para escapar das restrições do shell.

### Injeção de comando

Para escapar de um shell restrito que só permite a execução do comando `ls` com argumentos específicos, você pode usar injeção de comandos através desses argumentos.

Por exemplo, se o shell só permite `ls -l` ou `ls -a`, você pode injetar um comando adicional, como `pwd`, no argumento do `ls`.

Um exemplo seria usar:
```bash
ls $(pwd)
ou
ls `pwd`
```

Aqui, `$(pwd)` executa o comando `pwd` e insere seu resultado como argumento para `ls`, permitindo obter o diretório atual. Essa técnica explora a forma como o shell trata os argumentos para executar comandos adicionais ou explorar vulnerabilidades.

### Substituição de comando
Um método para escapar de um shell restrito é a **substituição de comando**. Isso envolve usar a sintaxe de substituição de comando, como os acentos graves ( `` ` ``), para executar comandos dentro de outros comandos. Se o shell permite a execução de comandos entre acentos graves, é possível escapar das restrições do shell ao executar comandos não restritos dessa maneira.

### Variáveis de ambiente
Em alguns casos, é possível escapar de um shell restrito usando encadeamento de comandos. Isso envolve executar múltiplos comandos em uma única linha, separados por metacaracteres como ponto e vírgula (`;`) ou barra vertical (`|`). Se o shell permite o uso de um desses metacaracteres, pode-se combinar comandos para executar ações além das restrições do shell. Por exemplo, usar um ponto e vírgula para separar dois comandos, sendo um deles um comando não restrito, pode permitir a execução de comandos adicionais.

### Funções do shell
Em alguns casos, escapar de um shell restrito pode ser possível usando funções de shell. Se o shell permite a definição e execução de funções, é possível criar uma função que execute comandos não restritos. Ao definir e chamar uma função que contém o comando desejado, podemos contornar as limitações impostas pelo shell restrito.Z