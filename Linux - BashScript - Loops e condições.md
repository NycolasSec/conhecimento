## Use Loops para Iterar Comandos
Existem algumas tarefas diárias que são repetitivas como fazer backup ou verificações em servidores. Loops são uma construção para a iteração de tarefas.

### Processar itens da linha de comando
 Em bash o loop for usa a seguinte sintaxe.
 
```bash
for item in List; do
COMMANDO VARIADO
done
```

O loop processa as strings que você fornece em _LIST_ e sai após processar a última string na lista. O loop `for` armazena temporariamente cada string da lista como o valor de _VARIÁVEL_, e então executa o bloco de comandos que usam a variável.

Forneça a lista de strings para o loop `for` a partir de uma lista que o usuário insere diretamente ou que é gerada a partir da expansão do shell, como expansão de variável, chave ou nome de arquivo, ou substituição de comando.

Esses exemplos mostram maneiras de fornecer strings para `for`:
![[Pasted image 20240828143518.png]]

## Códigos de saída do script Bash
Depois que um script interpreta e processa todo o seu conteúdo, o processo de script sai e passa o controle de volta para o processo pai que o chamou. Mas  um script pode ser encerrado antes de terminar, como quando o script encontra uma condição de erro.

Use o comando `exit` para sair imediatamente do script e pular o processamento do restante do script. Podemos passar um inteiro entre o e 255 como argumento, para indicar o status. 0 indica a conclusão do script sem erros.

Recupere o código de saída do último comando concluído da variável interna `$?`, como nos exemplos a seguir:

```bash-session
[user@host bin]$ cat hello
#!/usr/bin/bash
echo "Hello, world"
exit 0

[user@host bin]$ ./hello
Hello, world

[user@host bin]$ echo $?
0
```
Quando o comando `exit` é usado em argumento de código, o script retorna o código de saída do último comando executado no script.

## Teste a lógica para strings e diretórios e para comparar valores
Para garantir que condições inesperadas não interrompam os scripts, é recomendável verificar a entrada de comando. Todos os comandos produzem um código de saída ao serem concluídos. Para ver o status de saída, visualize a variável `$?` imediatamente após executar o comando de teste.

Um status de saída 0 indica uma saída bem-sucedida sem nada a relatar. Valores diferentes de zero indicam alguma condição ou falha.


Use vários operadores para testar se um número é maior que ( `gt`), maior que ou igual a ( `ge`), menor que ( `lt`), menor que ou igual a ( `le`) ou igual ( `eq`) a outro número.

Use operadores para testar se uma sequência de texto é a mesma ( `=`ou `==`) ou não a mesma ( `!=`) que outra sequência de texto, ou se a sequência tem comprimento zero ( `z`) ou tem comprimento diferente de zero ( `n`). Você também pode testar se um arquivo regular ( `-f`) ou diretório ( `-d`) existe e tem alguns atributos especiais, como se o arquivo é um link simbólico ( `-L`), ou se o usuário tem permissões de leitura ( `-r`).

Os exemplos a seguir demonstram o `test`comando com operadores de comparação numérica Bash:

```bash-session
[user@host ~]$ test 1 -gt 0 ; echo $?
0

[user@host ~]$ test 0 -gt 1 ; echo $?
1
```

Teste usando a sintaxe do comando Bash test, `[ <TESTEXPRESSION> ]`ou a sintaxe mais recente do comando test estendida, `[[ <TESTEXPRESSION> ]]`, que fornece recursos como globbing de nome de arquivo e correspondência de padrão regex. Na maioria dos casos, use a `[[ <TESTEXPRESSION> ]]`sintaxe.

Os exemplos a seguir demonstram a sintaxe do comando de teste Bash e os operadores de comparação numérica:

```bash-session
[user@host ~]$ [[ 1 -eq 1 ]]; echo $?
0

[user@host ~]$ [[ 1 -ne 1 ]]; echo $?
1

[user@host ~]$ [[ 8 -gt 2 ]]; echo $?
0

[user@host ~]$ [[ 2 -ge 2 ]]; echo $?
0

[user@host ~]$ [[ 2 -lt 2 ]]; echo $?
1

[user@host ~]$ [[ 1 -lt 2 ]]; echo $?
0
```

Os exemplos a seguir demonstram os operadores de comparação de strings Bash:

```bash-session
[user@host ~]$ [[ abc = abc ]]; echo $?
0

[user@host ~]$ [[ abc == def ]]; echo $?
1

[user@host ~]$ [[ abc != def ]]; echo $?
0
```

Os exemplos a seguir demonstram operadores unários (um argumento) de string Bash:

```bash-session
[usuário@host ~]$ STRING=''; [[ -z "$STRING" ]]; echo $?
0 

[usuário@host ~]$ STRING='abc'; [[ -n "$STRING" ]]; echo $?
0
```

>[!NOTE] Os caracteres de espaço dentro dos colchetes são obrigatórios, poies eles separam as palavras e elementos dentro da expressão de teste

## Estruturas condicionais
Scripts de shell simples representam uma coleção de comandos que são executados do começo ao fim. Os programadores incorporam a tomada de decisões em scripts de shell usando estruturas condicionais. Um script pode executar rotinas específicas quando condições declaradas são atendidas.

### Use a construção If/Then
A estrutura condicional mais simples é a _if/then_ construir, com a seguinte sintaxe:

```bash-session
if <CONDITION>; then
      <STATEMENT>
      ...
      <STATEMENT>
fi
```
Com essa construção, se o script atender à condição fornecida, ele executará o código no bloco de instruções.

Condições de teste comuns nas instruções `if/then` incluem os testes numéricos, de string e de arquivo discutidos anteriormente. A instrução `fi` no final fecha a construção `if/then`.

```bash-session
[usuário@host ~]$ systemctl is-active psacct > /dev/null 2>&1

[usuário@host ~]$if  [[ $? -ne 0 ]]; then sudo systemctl start psacct; fi
```

### Use a construção If/Then/Else
Você pode expandir ainda mais a `if/then`construção para tomar diferentes conjuntos de ações dependendo se uma condição é atendida. Use a construção `if/then/else` para realizar esse comportamento, como neste exemplo

```bash-session
if <CONDITION>; then
      <STATEMENT>
      ...
      <STATEMENT>
else
      <STATEMENT>
      ...
      <STATEMENT>
fi
```

A seção de código a seguir demonstra uma instrução `if/then/else` para iniciar o serviço `psacct` se ele não estiver ativo e para interrompê-lo se estiver ativo:

```bash-session
[user@host ~]$ systemctl is-active psacct > /dev/null 2>&1

[user@host ~]$ if  [[ $? -ne 0 ]]; then \
sudo systemctl start psacct; \
else \
sudo systemctl stop psacct; \
fi
```

### Use a construção If/Then/Elif/Then/Else
Expanda uma `if/then/else`construção para testar mais de uma condição e executar um conjunto diferente de ações quando ela atender a uma condição específica.

```bash-session
if <CONDITION>; then
      <STATEMENT>
      ...
      <STATEMENT>
elif <CONDITION>; then
      <STATEMENT>
      ...
      <STATEMENT>
else
      <STATEMENT>
      ...
      <STATEMENT>
fi
```

Nessa estrutura condicional, o Bash testa as condições conforme elas são ordenadas no script. Quando uma condição é verdadeira, o Bash executa as ações que estão associadas à condição e então pula o restante da estrutura condicional. Se nenhuma das condições for verdadeira, o Bash executa as ações na cláusula `else`.


O exemplo a seguir demonstra uma `if/then/elif/then/else`instrução para executar o `mysql`cliente se o `mariadb`serviço estiver ativo, ou para executar o `psql`cliente se o `postgresql`serviço estiver ativo, ou para executar o `sqlite3`cliente se o `mariadb`e o `postgresql`serviço estiverem inativos:
```bash-session
[user@host ~]$ systemctl is-active mariadb > /dev/null 2>&1

[user@host ~]$ MARIADB_ACTIVE=$?

[user@host ~]$ sudo systemctl is-active postgresql > /dev/null 2>&1

[user@host ~]$ POSTGRESQL_ACTIVE=$?

[user@host ~]$ if  [[ "$MARIADB_ACTIVE" -eq 0 ]]; then \
mysql; \
elif  [[ "$POSTGRESQL_ACTIVE" -eq 0 ]]; then \
psql; \
else \
sqlite3; \
fi
```




























































































