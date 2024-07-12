O Windows PowerShell tem uma ajuda interna abrangente que normalmente inclui exemplos.

## Get-Command
**Get-Command** recupera informações sobre um comando ou vários comandos, como o nome, a categoria, a versão e até mesmo o módulo que o contém. Aceita caracteres curinga. 

o que significa que você pode executar o comando **Get-Command *event*** e recuperar uma lista de comandos que contêm o texto **event** no nome.

você pode usar os parâmetros _-Noun_ e _-Verb_ para filtrar as partes de substantivos e de verbos do nome, respectivamente.

Você pode até combinar os parâmetros para refinar ainda mais os resultados retornados. Execute o comando **Get-Command –Noun event*****–Verb Get** para obter uma lista de comandos que têm substantivos começando com **event** e que usam o verbo **Get**.

## Get-Help
Você pode executar pesquisas semelhantes usando **Get-Help**, incluindo o uso de caracteres curinga.

Uma vantagem do **Get-Help** é que ele executa uma pesquisa de texto completo usando a sua cadeia de caracteres de consulta se não conseguir encontrar um nome de comando que corresponda

## Get-Module
Quando você usa o comando **Get-Module**, ele exibe uma lista parcial de cmdlets que o módulo ao qual você faz referência contém.

Se você descobriu o módulo **NetAdapter**, espera que ele tenha cmdlets para gerenciar adaptadores de rede.

Para encontrar todos os comandos aplicáveis a este módulo use **Get-Command –Module NetAdapter**.

O parâmetro _–Module_ restringe os resultados a apenas esses comandos no módulo designado.

## Galeria do PowerShell
A Galeria do PowerShell é um repositório central para conteúdo relacionado ao Windows PowerShell, incluindo scripts e módulos. A Galeria do PowerShell usa o módulo so Windows PowerShell, **PowerShellGet**.

**PowerShellGet** contém cmdlets para localizar e instalar módulos, scripts e comandos da galeria online. Por exemplo, o cmdlet **Find-Command** procura comandos, funções e aliases.

**Leitura adicional:** Para obter mais informações sobre a Galeria do PowerShell, consulte [Galeria do PowerShell](https://aka.ms/iast9g).
