## Verbos de cmdlet
A parte que corresponde ao verbo do nome do cmdlet indica o que ele faz. Há um conjunto de verbos aprovados usados pelos criadores de cmdlet que gera consistência no uso de nomes de cmdlet. Alguns verbos comuns:

- **Get**. Recupera um recurso, como um arquivo ou um usuário.
- **Set**. Altera os dados associados a um recurso, como uma propriedade de arquivo ou de usuário.
- **New**. Cria um recurso, como um arquivo ou um usuário.
- **Add**. Adiciona um recurso a um contêiner de vários recursos.
- **Remove**. Exclui um recurso de um contêiner de vários recursos.

Esses são alguns verbos que os cmdlet usam, alguns podem ter a mesma função em alguns casos, como o **Add** que pode criar um recurso assim como o **New**.

Já alguns são parecidos mas tem funções diferentes como verbo **Read** recupera as informações que um recurso contém, como o conteúdo de um arquivo de texto, enquanto o verbo **Get** recupera o arquivo em si.

## Substantivos de cmdlet
A parte que corresponde ao substantivo do nome do cmdlet indica que tipos de recursos ou objetos o cmdlet afeta. Todos os cmdlets que operarem no mesmo recurso devem usar o mesmo substantivo.

Por exemplo, o substantivo **Service** é usado por cmdlets que funcionam com serviços do Windows, e o substantivo **Process** é está relacionado a gerenciar processos em um computador.

Os substantivos também podem ter prefixos que ajudam no agrupamento de substantivos relacionados em famílias.
Por exemplo, os substantivos do Active Directory começam com as letras **AD** (como **ADUser**, **ADGroup** e **ADComputer**). Os cmdlets do Microsoft SharePoint Server começam com o prefixo **SP** e os cmdlets do Microsoft Azure começam com o prefixo **Az**.

## Parâmetros para usar os cmdlets do PowerShell
Os parâmetros modificam as ações executadas por um cmdlet. Você não pode especificar parâmetros, um parâmetro ou muitos parâmetros para um cmdlet.

### Formato do parâmetro
Nomes de parâmetros começam com hífen (**-**). Se usa um espaço para separar o parâmetro. Se o parâmetro contiver espaços será necessário encapsular o texto com aspas. Caso um parâmetro aceite vários valores deve-se separar eles por vírgula.

### Parâmetros opcionais versus necessários

Os parâmetros podem ser opcionais ou necessários. Se um parâmetro for necessário e você executar o cmdlet sem fornecer um valor para esse parâmetro, o Windows PowerShell solicitará que você forneça um valor para ele.

Em alguns casos, inserir o nome do parâmetro é opcional e você pode apenas inserir o valor do parâmetro.

Em alguns casos, inserir o nome do parâmetro é opcional e você pode apenas inserir o valor do parâmetro. Se você executar o comando **Get-ChildItem C:\\**, será o mesmo que executar o comando **Get-ChildItem -Path C:\\** porque o parâmetro *-Path* é definido como o primeiro parâmetro na definição do cmdlet.

Omitir o nome do parâmetro só funciona quando uma posição de parâmetro foi definida. Nem todos os comandos têm parâmetros posicionais.

### Comutadores

_Opções_ são um caso especial. Elas são basicamente parâmetros que aceitam um valor booliano (**true** ou **false**). Elas são diferentes dos parâmetros boolianos reais, pois o valor só será definido como **true** se a opção for incluída ao executar o comando. Um exemplo é o parâmetro _-Recurse_ ou a opção do cmdlet **Get-ChildItem**. O comando **Get-ChildItem c:\ -Recurse** retorna não apenas os itens no diretório C:\, mas também aqueles em todos os seus subdiretórios. Sem a opção **-Recurse**, somente os itens em C:\diretório são retornados.


















































































































































