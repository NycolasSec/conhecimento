Uma solução disponível para usuários do Red Hat Enterprise Linux para agendar tarefas adiadas é o comando `at`, que é instalado e habilitado por padrão. O pacote `at` fornece o daemon do sistema `atd` e os comandos `at` e `atq` para interagir com o daemon.

Qualquer usuário pode enfileirar trabalhos para o daemon `atd` usando o comando `at`. O daemon `atd` fornece 26 filas, identificadas de `a` a `z`, e os trabalhos posteriores, organizados em ordem alfabética, recebem prioridade menor no sistema (com valores de _nice_ mais altos, como discutido em um capítulo posterior).

### Agendamento de tarefas adiadas do usuário

Para agendar um trabalho utilizando o comando `at`, você precisa especificar o horário e, opcionalmente, a data em que deseja que o trabalho seja executado. Após especificar o tempo com o argumento `TIMESPEC`, o `at` entra em modo de entrada de comando, onde você insere os comandos que deseja executar. Quando terminar, você finaliza a entrada pressionando `Ctrl+D` em uma linha vazia.

#### Exemplo de uso:

1. **Agendar um trabalho para daqui a 5 minutos:**
```bash
at now +5min
```
   Em seguida, você pode digitar os comandos que deseja executar e finalizar com `Ctrl+D`.
--- ---
2. **Agendar um trabalho com um script:**
 Se você tiver um script chamado `myscript` e deseja executá-lo em 5 minutos, pode usar:
   ```bash
   at now +5min < myscript
   ```
   Isso agenda o script `myscript` para ser executado daqui a 5 minutos, sem precisar digitar os comandos manualmente.
--- ---
3. **Especificando horários e datas:**

   Você pode utilizar diferentes formatos de tempo para o argumento `TIMESPEC`:

   - **Hora específica:** `at 02:00pm`
   - **Hora e data específicas:** `at 02:00pm tomorrow` ou `at 15:59 Dec 25`
   - **Meia-noite:** `at midnight`
   - **Um tempo específico no futuro:** `at now +2 days`
   - **Teatime:** `at teatime` (equivalente a 16:00)
--- ---
#### Detalhes adicionais:

- **Hora sem data:** Se você apenas fornecer a hora, o trabalho será agendado para a próxima ocorrência dessa hora. Por exemplo, `at 02:00pm` agendará o trabalho para as 14:00 de hoje, ou de amanhã, se a hora atual já passou.
  
- **Data sem hora:** Se você fornecer uma data sem especificar a hora, o trabalho será agendado para a hora atual na data fornecida.

Este comando é útil para automatizar tarefas que precisam ser executadas em horários específicos, sem a necessidade de supervisão contínua.

O exemplo a seguir mostra um agendamento de trabalho sem fornecer a data. O comando `at` agenda o trabalho para hoje ou amanhã, dependendo se o tempo passou.

```shell-session
[user@host ~]$ date
Wed May 18 21:01:18 CDT 2022

[user@host ~]$ at 21:03 < myscript
job 3 at Wed May 18 21:03:00 2022

[user@host ~]$ at 21:00 < myscript
job 4 at Thu May 19 21:00:00 2022
```

Você pode usar somente letras minúsculas, letras maiúsculas no início da frase ou somente letras maiúsculas. Aqui estão alguns exemplos de especificações de tempo que você pode usar:

- `now +5min`
    
- `teatime tomorrow` (a hora do chá é `16:00`)
    
- `noon +4 days`
    
- `5pm august 3 2021`

Para ver outras especificações de tempo válidas, consulte o documento `timespec` local listado nas referências.


## Inspeção e gerenciamento de trabalhos adiados do usuário
Para obter uma visão geral dos trabalhos pendentes para o usuário atual, use o comando `atq` ou `at -l`.

```shell-session
[user@host ~]$ atq
```
![[Pasted image 20240902155220.png]]
Na saída anterior, cada linha representa uma tarefa futura diferente programada. A seguinte descrição se aplica à primeira linha da saída:

- **`28`** é o número do trabalho único.
- **`Mon May 16 05:13:00 2022`** é data e hora de execução do trabalho agendado.
- **`a`** indica que o trabalho está agendado com a fila padrão `a`.
- **`user`** é o proprietário do trabalho (e o usuário com o qual ele é executado).

Use o comando ``at -c _`JOBNUMBER`_`` para inspecionar os comandos que são executados quando o daemon `atd` executa um trabalho. Esse comando mostra o ambiente do trabalho, que é definido a partir do ambiente do usuário quando ele criou o trabalho, e a sintaxe do comando a ser executado.

> [!IMPORTANT] Usuários sem privilégios só podem ver e controlar seus próprios trabalhos. O usuário `root` pode ver e gerenciar todos os trabalhos.

#### Remoção de trabalhos do agendamento

O comando `atrm JOBNUMBER` remove um trabalho agendado. Remova o trabalho agendado quando ele não for mais necessário, por exemplo, quando uma configuração remota de firewall tiver sido realizada com êxito e não precisar ser redefinida.
































