Todo sistema operacional tem um shell, e para interagir com ele, precisamos usar um aplicativo conhecido como `terminal emulator`. Aqui estão alguns dos emuladores de terminal mais comuns:

| **Emulador de Terminal**                                       | **Sistema operacional** |
| :------------------------------------------------------------- | :---------------------- |
| [Terminal do Windows](https://github.com/microsoft/terminal)   | Windows                 |
| [comandante](https://cmder.app/)                               | Windows                 |
| [Massa de vidraceiro](https://www.putty.org/)                  | Windows                 |
| [gatinha](https://sw.kovidgoyal.net/kitty/)                    | Windows, Linux e MacOS  |
| [Alacritty](https://github.com/alacritty/alacritty)            | Windows, Linux e MacOS  |
| [xterm](https://invisible-island.net/xterm/)                   | Linux                   |
| [Terminal GNOME](https://en.wikipedia.org/wiki/GNOME_Terminal) | Linux                   |
| [Terminal MATE](https://github.com/mate-desktop/mate-terminal) | Linux                   |
| [Console](https://konsole.kde.org/)                            | Linux                   |
| [terminal](https://en.wikipedia.org/wiki/Terminal_(macOS))     | Mac OS                  |
| [iTermo2](https://iterm2.com/)                                 | Mac OS                  |

Esta lista não é de forma alguma todos os emuladores de terminal disponíveis, mas inclui alguns dignos de nota

Selecionar o emulador de terminal adequado para o trabalho é principalmente uma preferência pessoal e estilística com base em nossos fluxos de trabalho que se desenvolvem à medida que nos familiarizamos com nosso sistema operacional de escolha. Portanto, não deixe que ninguém faça você se sentir mal por selecionar uma opção em vez da outra.

## Interpretador de Linguagem de Comando

Assim como um intérprete de linguagem humana traduzirá a linguagem falada ou de sinais em tempo real, um `command language interpreter` é um programa que trabalha para interpretar as instruções fornecidas pelo usuário e emitir as tarefas para o sistema operacional para processamento.

 Então, quando discutimos interfaces de linha de comando, sabemos que é uma combinação do sistema operacional, aplicativo emulador de terminal e o interpretador de linguagem de comando. Muitos intérpretes de linguagem de comando diferentes podem ser usados, alguns dos quais também são chamados `shell scripting languages`ou `Command and Scripting interpreters`conforme definidos nas [Técnicas de execução](https://attack.mitre.org/techniques/T1059/) do `MITRE ATT&CK Matrix`.
 
Entender o interpretador de linguagem de comando em uso em qualquer sistema também nos dará uma ideia de quais comandos e scripts devemos usar.

## Prática com emuladores de terminal e shells

Vamos usar nosso `Parrot OS`Pwnbox para explorar mais a anatomia de um shell. Clique no `green`ícone quadrado no topo da tela para abrir o `MATE`emulador de terminal e então digite algo aleatório e aperte enter.

#### Exemplo de terminal
![[Pasted image 20240828091150.png]]

Ele abriu o aplicativo emulador de terminal MATE, que foi pré-configurado para usar um interpretador de linguagem de comando.

Neste caso, somos "informados" sobre qual interpretador de linguagem está em uso ao ver o sinal `$`. Este sinal ``$`` é usado em Bash, Ksh, POSIX e muitas outras linguagens de shell para marcar o início de `shell prompt` onde o usuário pode começar a digitar comandos e outras entradas.

Outra maneira de identificar o interpretador de linguagem é visualizando os processos em execução na máquina. No Linux, podemos fazer isso usando o seguinte comando:

#### Validação de Shell de 'ps'
```shell-session
NycolasES6@htb[/htb]$ ps

    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps
```

Também podemos descobrir qual linguagem de shell está em uso visualizando as variáveis ​​de ambiente usando o comando `env`:

```shell-session
NycolasES6@htb[/htb]$ env

SHELL=/bin/bash
```

Agora vamos selecionar o ícone quadrado azul no topo da tela no Pwnbox.

![[Pasted image 20240828091452.png]]

Selecionar este ícone também abre o aplicativo terminal MATE, mas usa um interpretador de linguagem de comando diferente desta vez. Compare-os conforme são colocados lado a lado.

- `What differences can we identify?`
- `Why would we use one over the other on the same system?`
























