## Agendamento de processos Linux

Os sistemas de computador modernos utilizam CPUs com múltiplos núcleos e capacidade de multithreading, permitindo a execução simultânea de vários threads de instrução. Supercomputadores podem ter milhares de CPUs, processando milhões de threads em paralelo.

Enquanto workstations pessoais são projetadas para corresponder à carga de trabalho de um único usuário, servidores corporativos enfrentam demandas muito maiores, podendo sofrer saturação de CPU. Para gerenciar essa carga, sistemas operacionais como Linux utilizam a técnica de time-slicing, alternando rapidamente entre threads nos núcleos disponíveis, criando a impressão de execução simultânea de muitos processos.

### Prioridades do processo
O texto explica como o Linux gerencia a prioridade dos processos para determinar a ordem em que eles recebem tempo de CPU. Ele menciona que o Linux usa diferentes políticas de agendamento, incluindo as voltadas para aplicativos interativos, processamento em lote e em tempo real.

O Completely Fair Scheduler (CFS) é o algoritmo padrão para processos normais, que organiza os processos em uma árvore de pesquisa binária baseada no tempo de CPU que cada processo já utilizou. Isso garante que processos com menor tempo de CPU acumulado tenham prioridade para execução.

### Valor de nice

O valor de **nice** em um sistema Linux é usado para ajustar a prioridade dos processos na árvore binária do agendador. Esse valor varia de -20 (mais prioridade) a 19 (menos prioridade), com um padrão de 0. Um valor de nice mais baixo aumenta a prioridade do processo, enquanto um valor mais alto a reduz. Todos os usuários podem diminuir a prioridade, mas somente o usuário root pode aumentá-la. Os processos herdam o valor de nice de seus processos pai.

Aumentar o valor de nice abaixa a posição do thread e diminuir o valor sobe a posição do thread.

### Permissão para modificar valores de nice

Usuários com privilégios podem reduzir o valor de `nice` de um processo, aumentando sua prioridade e fazendo com que o processo receba mais tempo de CPU, o que pode reduzir o tempo disponível para outros processos em sistemas saturados.

Já usuários sem privilégios só podem aumentar o valor de `nice`, tornando seus próprios processos menos prioritários e, portanto, menos exigentes em relação ao tempo de CPU, sem a capacidade de ajustar o `nice` de processos de outros usuários.

### Visualização de valores de nice
Os valores de nice mapeiam para um valor de prioridade, e ambos os valores estão disponíveis para visualização em comandos de listagem de processos. Um valor de nice de -20 mapeia para uma prioridade 0 no comando `top`. Um valor de nice de 19 mapeia para uma prioridade 39 no comando `top`.

![[Pasted image 20240830144553.png]]

O comando `top` exibe a prioridade do processo na coluna `PR` e o valor de nice na coluna `NI`

A seguinte saída é o sumário e uma listagem parcial do processo no comando `top`:
![[Pasted image 20240830144639.png]]
O comando `ps` exibe os valores de nice do processo ao usar as opções de formatação padrão.

 Os processos são classificados em ordem decrescente por valor de nice. Na coluna da classe de agendamento `CLS`, `TS` significa _compartilhamento de tempo_, que é outro nome para as políticas de agendamento normais, incluindo `SCHED_NORMAL`. Outros valores `CLS`, como `FF` para _primeiro a entrar, primeiro a sair_ e `RR` para _round-robin_, indicam processos em tempo real.

Os processos em tempo real não recebem valores de nice, conforme indicado pelo traço (`-`) na coluna `NI`. As políticas de agendamento avançadas são ensinadas no curso.

O comando `ps` a seguir lista todos os processos com a ID do processo, o nome do processo, o valor de nice e a classe de agendamento.
![[Pasted image 20240830144843.png]]

## Início de processos com valores de nice definidos pelo usuário

Quando um processo é criado, ele herda o valor de nice de seu pai. Quando um processo é iniciado a partir da linha de comando, ele herda seu valor de nice do processo de shell no qual ele foi iniciado. Normalmente, novos processos são executados com o valor de nice padrão de 0.

O exemplo a seguir inicia um processo a partir do shell e exibe o valor de nice do processo. Observe o uso da opção PID no comando `ps` para especificar a saída solicitada.

```shell-session
[user@host ~]$ sleep 60 &

[user@host ~]$ **`ps -o pid,comm,nice 2667`**
```
![[Pasted image 20240830145046.png]]

Todos os usuários podem utilizar o comando `nice` para iniciar comandos com um valor de nice padrão ou superior.

O seguinte exemplo inicia o mesmo comando como um trabalho em segundo plano com o valor de nice padrão e exibe o valor de nice do processo:
```shell-session
[user@host ~]$ nice sleep 60 &

[user@host ~]$ ps -o pid,comm,nice 2736
```
![[Pasted image 20240830145151.png]]

Use a opção `-n` do comando `nice` para aplicar um valor de nice definido pelo usuário para o processo inicial. O padrão é adicionar 10 ao valor de nice atual do processo.
```shell-session
[user@host ~]$ nice -n 15 sleep 60 &

[user@host ~]$ ps -o pid,comm,nice 2740
```
![[Pasted image 20240830145258.png]]

### Alteração do valor de nice de um processo existente
Você pode alterar o valor de nice de um processo existente com o comando `renice` Este exemplo usa a ID do processo de um exemplo anterior para mudar do valor de nice atual de 15 para um novo valor de nice de 19.
```shell-session
[user@host ~]$ renice -n 19 2740
```
![[Pasted image 20240830145433.png]]

Você também pode usar o comando `top` para alterar o valor de nice de um processo existente. Na interface interativa `top`, pressione a tecla **r** para acessar o comando `renice`. Insira a ID do processo e, depois, o novo valor de nice.






































