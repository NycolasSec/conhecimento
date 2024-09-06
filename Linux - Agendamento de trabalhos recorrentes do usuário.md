### Descrição de trabalhos recorrentes do usuário
Os trabalhos recorrentes no Red Hat Enterprise Linux são agendados para execução repetida usando o daemon `crond`, que já vem habilitado por padrão. O daemon lê arquivos de configuração, tanto do sistema quanto individuais para cada usuário, que podem ser editados com o comando `crontab -e`. Esses arquivos permitem que usuários e administradores controlem o agendamento de tarefas. Se o trabalho não usar redirecionamento, a saída ou erros são enviados por e-mail ao proprietário da tarefa.

### Agendamento de trabalhos recorrentes do usuário
Use o comando `crontab` para gerenciar os trabalhos agendados. A seguinte lista mostra os comandos que um usuário local pode usar para gerenciar seus trabalhos:

|Comando|Uso pretendido|
|:--|:--|
|`crontab -l`|Lista os trabalhos do usuário atual.|
|`crontab -r`|Remove todos os trabalhos do usuário atual.|
|`crontab -e`|Edita os trabalhos do usuário atual.|
|``crontab _`filename`_``|Remove todos os trabalhos e os substitui por aqueles lidos de _filename_. Esse comando usa a entrada `stdin` quando nenhum arquivo é especificado.|

Um usuário com privilégios pode usar a opção `-u` do comando `crontab` para gerenciar trabalhos para outro usuário. O comando `crontab` nunca é usado para gerenciar trabalhos do sistema, e usar os comandos `crontab` com o usuário `root` não é recomendado devido à capacidade de explorar trabalhos pessoais configurados para serem executados com `root`.

### Descrição do formato de trabalho do usuário
O comando `crontab -e` invoca o editor `vim` por padrão, a menos que a variável de ambiente `EDITOR` esteja definida para outro editor. Cada trabalho deve usar uma linha exclusiva no arquivo `crontab`. Siga estas recomendações para entradas válidas ao escrever trabalhos recorrentes:

- Linhas vazias para facilitar a leitura
- Comentários em linhas que começam com o sinal numérico (`#`)
- Variáveis de ambiente com um formato `NAME=value`, que afeta todas as linhas após a linha em que são declaradas

As configurações de variável padrão incluem a variável `SHELL` para declarar o shell que é usado para interpretar as linhas restantes do arquivo `crontab`. A variável `MAILTO` determina quem deve receber a saída por e-mail.

>[!faq] A capacidade de enviar um e-mail exige configuração adicional do sistema para um servidor de e-mail local ou uma retransmissão SMTP.

Os campos no arquivo `crontab` aparecem na seguinte ordem:
- Minutos
- Horas
- Dia do mês
- Mês
- Dia da semana
- Comando

Por exemplo, para executar um comando no dia 11 de cada mês, e toda sexta-feira às 12:15 (formato de 24 horas), use o seguinte formato de trabalho:
```txt
15 12 11 * Fri command
```

Os primeiros cinco campos usam as mesmas regras de sintaxe:
- Use o caractere `*` para executar em todas as instâncias possíveis do campo.
- Um número para especificar o número de minutos ou horas, uma data ou um dia da semana. No caso de dias da semana, `0` corresponde ao domingo, `1` corresponde à segunda-feira, `2` corresponde à terça-feira e assim por diante. `7` também corresponde ao domingo.
- Use `x-y` para um intervalo, que inclui os valores `x` e `y`.
- Use `x,y` para listas. As listas também podem incluir intervalos. Por exemplo, `5,10-13,17` na coluna `Minutes` para um trabalho ser executado aos 5, 10, 11, 12, 13 e 17 minutos de todas as horas.
- O `*/x` indica um intervalo de `x`. Por exemplo, `*/7` na coluna `Minutes` executa um trabalho a cada sete minutos

Além disso, é possível usar abreviação em inglês de três letras para dias da semana e meses, por exemplo, Jan ou Feb (janeiro ou fevereiro) e Tue ou Wed (terça-feira e quarta-feira).

Se o comando contiver um sinal de porcentagem sem escape (`%`), ele é tratado como caractere em uma nova linha, e tudo depois do sinal de porcentagem é transmitido ao comando como entrada `stdin` .

#### Exemplo de trabalhos recorrentes de usuários
O trabalho a seguir executa o comando `/usr/local/bin/yearly_backup` exatamente às 09:00 do dia 3 de fevereiro, todos os anos. Fevereiro é representado como o número 2 no exemplo, pois é o segundo mês do ano.
```txt
0 9 3 2 * /usr/local/bin/yearly_backup
```

O trabalho a seguir envia um e-mail com a palavra `Chime` ao proprietário desse trabalho a cada cinco minutos entre e incluindo às 09:00 e às 16:00, mas somente às sextas-feiras no mês de julho.
```txt
*/5 9-16 * Jul 5 echo "Chime"
```

O intervalo de horas `9-16` anterior significa que o temporizador do trabalho começa na hora nona (09:00) e continua até o final da décima sexta hora (16:59). O trabalho começa a ser executado às `09:00` com a última execução às `16:55` porque cinco minutos após às `16:55` serão `17:00`, e isso está além do escopo de horas dado.

Se um intervalo for especificado para as horas em vez de um único valor, todas as horas dentro do intervalo corresponderão. Portanto, com as horas de `9-16`, este exemplo corresponde a cada cinco minutos das 09:00 às 16:55.

O trabalho a seguir executa o comando `/usr/local/bin/daily_report` todos os dias úteis (de segunda a sexta-feira) dois minutos antes de meia-noite.
```txt
58 23 * * 1-5 /usr/local/bin/daily_report
```

O trabalho a seguir executa o comando `mutt` para enviar a mensagem de e-mail `Checking in` para o destinatário `developer@example.com` todos os dias úteis (de segunda a sexta-feira), às 9h.

```txt
0 9 * * 1-5 mutt -s "Checking in" developer@example.com % Hi there, just checking in.
```














