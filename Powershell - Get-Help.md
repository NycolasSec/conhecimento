Você pode acessar essa ajuda usando o comando **Get-Help**. **Get-Help** exibe todo o conteúdo da ajuda na tela e permite rolar por ele. Você também pode usar a função **Help** ou o alias **Man**, que é mapeado para o comando **Get-Help**.

Por exemplo, para exibir as informações de ajuda para o cmdlet **Get-ChildItem**, insira o seguinte comando no console e pressione a tecla Enter:
```powershell
Get-Help Get-ChildItem
```

### Get-Help parameters

O comando **Get-Help** aceita parâmetros que permitem encontrar informações adicionais além das informações exibidas por padrão. Um motivo comum para buscar ajuda adicional é para identificar exemplos de uso para um comando. Os comandos do Windows PowerShell geralmente incluem muitos desses exemplos. Por exemplo, executar o comando **Get-Help Stop-Process –Examples** fornecerá exemplos de uso do cmdlet **Stop-Process**.

O parâmetro _-Full_ fornece informações detalhadas sobre um cmdlet, incluindo:
	- Uma descrição de cada parâmetro.
	- Se cada parâmetro tem um valor padrão (embora essas informações não sejam consistentemente documentadas em todos os comandos).
	- Se um parâmetro é obrigatório.
	- Se um parâmetro pode aceitar um valor em uma posição específica (nesse caso, o número de posição, a partir de 1, é fornecido) ou se você deve inserir o nome do parâmetro (nesse caso, é exibido **named**).
	- Se um parâmetro aceita a entrada de pipeline e, se for o caso, como.

Outros parâmetros de **Get-Help** incluem:
	- _‑ShowWindow_. Exibe o tópico de ajuda em uma janela separada, o que facilita muito o acesso à ajuda ao inserir comandos.
	- _‑Online_. Exibe a versão online do tópico de ajuda (normalmente as informações mais atualizadas) em uma janela do navegador.
	- _‑Parameter ParameterName_. Exibe a descrição de um parâmetro nomeado.
	- _‑Category_. Exibe a ajuda apenas para determinadas categorias de comandos, como cmdlets e funções.

### Usando Get-Help para localizar comandos
O comando **Get-Help** pode ser muito útil para localizar comandos. Ele aceita caracteres curinga (*, ?), notavelmente o caractere curinga asterisco (*).

Por exemplo, se você quiser todos os cmdlets que operam em processos, poderá inserir o comando **Get-Help *process*** no console e pressionar a tecla Enter.

Quando nenhum resultado é encontrado, o sistema de ajuda executa uma pesquisa de texto completo de descrições e sinopses de comando disponíveis. Essa pesquisa localiza todos os arquivos de ajuda que contêm _beep_. Se houver apenas um único arquivo com uma correspondência, o sistema de ajuda exibirá o conteúdo dele em vez de exibir uma lista de um item.



