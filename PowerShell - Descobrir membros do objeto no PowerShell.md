_Membros_ são os vários componentes de um objeto e incluem:

- Propriedades, que descrevem atributos do objeto. Exemplos de propriedades incluem um nome de serviço, uma ID de processo e uma mensagem de log de eventos.
- Métodos, que invocam uma ação em um objeto. Por exemplo, um objeto de processo pode ser interrompido e um log de eventos pode ser limpo.
- Eventos, que são disparados quando algo acontece com um objeto. Um arquivo pode disparar um evento quando é atualizado, ou um processo pode disparar um evento quando tem uma saída para produzir.

O PowerShell trabalha principalmente com propriedades e métodos dos objetos. Quando você executa um comando, nem todas as propriedades do objeto resultante são exibidas por padrão, pois alguns objetos podem ter centenas de propriedades, e a tela não comportaria todas elas. Para decidir quais propriedades exibir, o PowerShell utiliza arquivos de configuração que determinam as propriedades padrão a serem mostradas.

Para visualizar todas as propriedades e métodos de um objeto, você pode usar o comando `Get-Member`. Esse comando revela todos os membros do objeto, incluindo aqueles que não são exibidos por padrão, além de listar métodos, eventos e o nome do tipo do objeto. Por exemplo, ao executar `Get-Service`, o tipo de objeto produzido é `System.ServiceProcess.ServiceController`, que você pode usar para buscar documentação e exemplos na internet, embora muitos desses exemplos possam estar em linguagens como Visual Basic ou C#.

Para usar **Get-Member**, basta canalizar qualquer saída de comando para ele. Por exemplo, insira o seguinte comando no console e selecione Enter:

```powershell
Get-Service | Get-Member
```

O primeiro comando é executado, produz a saída e, em seguida, passa essa saída para Get-Member. Tenha cuidado ao executar comandos que podem modificar a configuração do sistema.