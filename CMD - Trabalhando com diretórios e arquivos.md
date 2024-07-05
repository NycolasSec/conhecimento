Para obter uma listagem de quais arquivos estão dentro de um diretório, podemos usar o `dir`comando, e `tree`fornece uma listagem completa de todos os arquivos e pastas dentro do caminho especificado. Então é bom ver que já temos uma vantagem.

Tanto o comando `md` quanto o `mkdir` criam diretórios, pode-se usar qualquer um.

Tanto o comando `rd` quanto o `rmdir` deletam diretórios, pode-se usar qualquer um. Para apagarmos os diretórios e seus arquivo temos de usar o opção `/S` em qualquer um deles.

## Modificando

Nesse caso temos algumas opções

- `Move`
- `Robocopy`
- `xcopy`

Pra usar o `move` temos de usar a seguinte sintaxe:
`move <source> <destination>` 

```cmd
C:\Users\htb\Desktop> move example C:\Users\htb\Documents\example
```

Assim movemos o diretório de exemplo e todos os seus arquivos na pasta Documents do usuário.

### xcopy

Apesar de ainda existir, foi descontinuado para o `robocopy`

Onde xcopy brilha é que ele pode remover o bit Somente leitura de arquivos ao movê-los.

Sua sintaxe é:
`xcopy <source> <destination> <options>`

Assim como era com move, podemos usar curingas para arquivos de origem, não para arquivos de destino.

```cmd
C:\Users\htb\Desktop> xcopy C:\Users\htb\Documents\example C:\Users\htb\Desktop\ /E
```
![[Pasted image 20240704172753.png]]

Utilizando a opção  `/E`, dissemos ao Xcopy para copiar quaisquer arquivos e subdiretórios para incluir diretórios vazios.

Tenha em mente que isso não excluirá a cópia no diretório anterior. Ao executar a duplicação, o xcopy redefinirá quaisquer atributos que o arquivo tinha.

Se você deseja manter os atributos do arquivo (como somente leitura ou oculto), você pode usar a opção `/K`.

### robocopy
É o sucessor do `xcopy`, com mais capacidade

O Robocopy pode copiar e mover arquivos localmente, para diferentes unidades e até mesmo através de uma rede, mantendo os dados e atributos do arquivo para incluir registros de data e hora, propriedade, ACLs e quaisquer sinalizadores definidos como ocultos ou somente leitura.

Precisamos estar cientes de que o Robocopy foi feito para diretórios grandes e sincronização de unidades, então ele não gosta de copiar ou mover arquivos singulares por padrão.

```cmd
C:\Users\htb\Desktop> robocopy C:\Users\htb\Desktop C:\Users\htb\Documents\
```
![[Pasted image 20240704173346.png]]

O Robocopy pegou tudo em nosso diretório Desktop e fez uma cópia no diretório Documents.

Não apresentou nenhum problema, pois temos permissão sobre esses diretórios.

Robocopy também pode trabalhar com arquivos de sistema, somente leitura e ocultos.

Como usuário, isso pode ser problemático se não tivermos os atributos `SeBackupPrivilege`and `auditing privilege`. Isso pode nos impedir de duplicar ou mover arquivos e diretórios.

No entanto, há uma pequena solução alternativa. Podemos utilizar a opção `/MIR` para nos permitir copiar os arquivos de que precisamos temporariamente.

### Falha no modo de backup do Robocopy

```cmd
C:\Users\htb\Desktop> robocopy /E /B /L C:\Users\htb\Desktop\example C:\Users\htb\Documents\Backup\
```
![[Pasted image 20240704174148.png]]

Da saída acima, podemos ver que nossas permissões são insuficientes. Utilizar a opção ``/MIR`` concluirá a tarefa para nós, ela marcará os arquivos como um backup do sistema e os ocultará da visualização, ela também  espelhará o diretório de destino para a origem. Qualquer arquivo que exista dentro do destino será removido. Certifique-se de colocar a nova cópia em uma pasta limpa.

Podemos limpar os atributos adicionais se adicionarmos a opção `/A-:SH` ao nosso comando.

Com a opção `/L`, ele apenas processa o comando que você emitir, mas não o executa. Apenas mostra o resultado potencial.

## Arquivos

Se desejarmos visualizar o conteúdo de um arquivo? Podemos utilizar os comandos `more`, `openfiles`, e `type`.

Com a ferramenta interna`more`, podemos visualizar o conteúdo de um arquivo ou os resultados de outro comando impresso nele, uma tela de cada vez. Como um armazenamento em buffer texto de rolagem que, poderia transbordar o buffer do terminal

#### more

```cmd
C:\Users\htb\Documents\Backup> more secrets.txt
```
![[Pasted image 20240704174945.png]]

Na parte de baixo ele mostra em que porcentagem do arquivo estamos, se pressionarmos `enter` ou `space bar`, ele rolará o texto do documento.

Com a opção `/S` podemos reduzir o espaço em branco em uma única linha em cada ponto. Isso não modifica o arquivo.

#### more /S

```cmd
C:\Users\htb\Documents\Backup> more /S secrets.txt
```
![[Pasted image 20240704175251.png]]
Aqui o `more` tomou uma grande quantidade de espaço em branco e o comprimiu.

### Enviando uma saída de comando para more

```cmd
C:\Users\htb\> ipconfig /all | more
```
![[Pasted image 20240704180004.png]]

### type

Pode exibir o conteúdo de vários arquivos de texto de uma vez, é uma ferramenta simples mais útil, uma coisa interessante sobre type é que ele não bloqueia arquivos, então não há preocupação em bagunçar alguma coisa.

#### type

```cmd
C:\Users\htb\Desktop>type bio.txt
```
![[Pasted image 20240704180548.png]]

É só isso. Type fornece saída de arquivo Simple. Também podemos usá-lo para enviar saída para outro arquivo. Esta pode ser uma maneira rápida de escrever um novo arquivo ou anexar dados a outro arquivo.

### Redireciona com type

```cmd
C:\Users\htb\Desktop>type passwords.txt >> secrets.txt
C:\Users\htb\Desktop>type secrets.txt
```
![[Pasted image 20240704180658.png]]

Com o exemplo acima, anexamos o arquivo passwords.txt ao final do arquivo secrets.txt com `>>`.

### Criar e modificar um arquivo

Criar e modificar um arquivo a partir da linha de comando é relativamente fácil. Temos várias opções que incluem `echo`, `fsutil`, `ren`, `rename`, e `replace`

![[Pasted image 20240704181028.png]]

### Fsutil para criar um arquivo

Com `fsutil`, podemos fazer muitas coisas, mas neste caso, vamos usá-lo para criar um arquivo.

```cmd
C:\Users\htb\Desktop>fsutil file createNew for-sure.txt 222
File C:\Users\htb\Desktop\for-sure.txt is created

C:\Users\htb\Desktop>echo " my super cool text file from fsutil "> for-sure.txt

C:\Users\htb\Desktop>type for-sure.txt
" my super cool text file from fsutil "
```

### Renomear

```cmd
ren demo.txt superdemo.txt
```

Pode ser usado como `ren` ou `rename`

### Entrada e saída

Já vimos isso algumas vezes, mas vamos tirar um minuto para falar sobre E/S. Podemos utilizar `<`, `>`, `|`, e `&`para enviar entrada e saída do console e arquivos para onde precisamos. Com `>`podemos enviar a saída de um comando para um arquivo.

```cmd
C:\Users\htb\Documents>ipconfig /all > details.txt

C:\Users\htb\Documents>type details.txt
```
![[Pasted image 20240704181505.png]]

Usar `>`esse método criará o arquivo se ele não existir ou substituirá o conteúdo do arquivo especificado. Para anexar a um arquivo já preenchido, podemos utilizar `>>`.

### Passar um arquivo de texto para um comando

Estávamos alimentando a entrada de um comando antes; vamos alimentar a entrada em um comando agora. Faremos isso com `<`.

```cmd
C:\Users\htb\Documents>find /i "see" < test.txt
```
![[Pasted image 20240704181856.png]]

Na sessão acima, pegamos o conteúdo de `test.txt`e o alimentamos em nosso comando find.

Isso pode ser extremamente útil para nós, como um hacker que procura informações importantes.

### Saída de Pipe entre Comandos

Outra rota que podemos tomar é alimentar a saída de um comando diretamente em outro comando com o `|`pipe chamado.

```cmd
C:\Users\htb\Documents>ipconfig /all | find /i "IPV4"
```
![[Pasted image 20240704182052.png]]

Com `pipe`, poderíamos emitir o comando `ipconfig /all`e enviá-lo `find`para procurar uma string específica.

### Execute **A** e depois **B**

Digamos que desejamos ter dois comandos executados em sucessão. Podemos emitir o comando e segui-lo com `&`e então nosso próximo comando. Isso garantirá que, nessa instância, nosso comando `A`seja executado primeiro e então a sessão executará command `B`. Não importa se o comando foi bem-sucedido ou falhou. Ele apenas os emite.

```cmd
C:\Users\htb\Documents>ping 8.8.8.8 & type test.txt
```
![[Pasted image 20240704182316.png]]

### Dependendo do estado &&

Se nos importamos com o resultado ou estado dos comandos que estão sendo executados, podemos utilizar `&&`o comando A e, se for bem-sucedido, executar o comando B.

```cmd-session
C:\Users\student\Documents>cd C:\Users\student\Documents\Backup && echo 'did this work' > yes.txt

C:\Users\student\Documents\Backup>type yes.txt
```
![[Pasted image 20240704182516.png]]

Você também pode fazer o oposto disso com `||`. Ao usar (pipe pipe), estamos dizendo execute o comando A. Se ele falhar, execute o comando B.


## Excluir arquivos

Agora, e se quisermos remover objetos do host? Vamos dar uma olhada nos comandos `del` e .`erase`

Ao utilizar `del`ou `erase`, lembre-se de que podemos especificar um diretório, um nome de arquivo, uma lista de nomes ou até mesmo um atributo específico para direcionar ao tentar excluir arquivos.

```cmd
C:\Users\htb\Desktop\example>del file-1
```

### Usando Del e Erase para remover uma lista de arquivos

```cmd
C:\Users\htb\Desktop\example> erase file-3 file-5
```

Ambos os comandos fazem a mesma coisa. Dessa vez, alimentamos erase com uma lista de dois arquivos, `file-3`e `file-5`. Ele apagou os arquivos sem problemas.

Digamos que queremos nos livrar de um arquivo somente leitura ou oculto. Podemos fazer isso com a opção `/A:`.

### del - Ajuda

```cmd
C:\Users\htb\Desktop\example> help del
```
![[Pasted image 20240704183046.png]]

Então, para excluir um arquivo somente leitura, podemos usar `A:R`. Isso removerá qualquer coisa em nosso caminho que seja somente leitura. No entanto, como identificamos se um arquivo é somente leitura, oculto ou tem algum outro atributo? Dir pode vir ao resgate novamente. Utilizar `dir /A:R`nos mostrará qualquer coisa com o atributo somente leitura. Vamos tentar.

### Exibir arquivos com o atributo de somente leitura

```cmd
C:\Users\htb\Desktop\example> dir /A:R
```
![[Pasted image 20240704183220.png]]

Agora sabemos que um arquivo corresponde ao nosso atributo Read-only no diretório de exemplo. Vamos excluí-lo.

### Excluir um arquivo somente leitura

```cmd
C:\Users\htb\Desktop\example > del /A:R *
```
![[Pasted image 20240704183324.png]]

Observe que costumávamos usar  `*` para especificar qualquer arquivo. Agora, quando olhamos para o diretório de exemplo novamente, o arquivo-66 está faltando, mas os arquivos 2, 4 e 6 ainda estão lá.

Vamos dar uma chance ao del novamente com o atributo hidden. Para identificar se há algum arquivo oculto dentro do diretório, podemos usar`dir /A:H`

### Visualizando arquivos ocultos

```cmd
C:\Users\htb\Desktop\example> dir /A:H
```
![[Pasted image 20240704183452.png]]

### Removendo arquivos ocultos

Para excluir o arquivo oculto, podemos executar o mesmo comando del de antes, apenas alterando o atributo de `R`para `H`.

```cmd
C:\Users\htb\Desktop\example>del /A:H *
C:\Users\htb\Desktop\example\*, Are you sure (Y/N)? Y
```

Agora, excluímos com sucesso um arquivo com o atributo hidden. Para apagar o diretório com o restante de seu conteúdo, podemos alimentar o `del`comando com o nome do diretório para remover o conteúdo e segui-lo com o `rd`comando para eliminar a estrutura do diretório. Se um arquivo residir dentro do diretório com o atributo Read-only ou algum outro, utilizar a `/F`opção forçará a exclusão do arquivo.

### Copiando e movendo arquivos

`Copy` e `move`são as maneiras mais fáceis de copiar e mover arquivos.

### copy

```cmd
C:\Users\student\Documents\Backup>copy secrets.txt C:\Users\student\Downloads\not-secrets.txt
```
![[Pasted image 20240704183851.png]]

### Validação de cópia

Por padrão, `copy`concluirá sua tarefa e fechará. Se quisermos garantir que os arquivos copiados sejam copiados corretamente, podemos usar o `/V`switch para ativar a validação de arquivo.

```cmd
C:\Windows\System32> copy calc.exe C:\Users\student\Downloads\copied-calc.exe /V
Overwrite C:\Users\student\Downloads\copied-calc.exe? (Yes/No/All): A
```
![[Pasted image 20240704183944.png]]

### move

Com `move` , podemos mover arquivos e diretórios de um lugar para outro e renomeá-los. Move difere de copy porque também pode renomear e mover diretórios.

```cmd
C:\Users\student\Desktop>move C:\Users\student\Desktop\bio.txt C:\Users\student\Downloads
```
![[Pasted image 20240704184059.png]]

Acima, pegamos o `bio.txt`arquivo e o movemos para a pasta Downloads. Manipular arquivos é tão fácil quanto isso.

















































































































































































