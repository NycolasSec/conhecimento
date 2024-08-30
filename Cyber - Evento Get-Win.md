A análise em massa dos logs de eventos do Windows e Sysmon é crucial na segurança cibernética, especialmente em resposta a incidentes e caça a ameaças. Esses logs oferecem informações valiosas sobre o estado dos sistemas, atividades do usuário e ameaças potenciais, mas podem ser volumosos e difíceis de gerenciar. Em grandes organizações, milhões de logs podem ser gerados diariamente, exigindo ferramentas e técnicas eficientes para extrair informações úteis desses dados.

Uma dessas ferramentas é o [cmdlet Get-WinEvent](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.3) no PowerShell.

## Usando Get-WinEvent
O cmdlet Get-WinEvent é uma ferramenta do PowerShell para consultar logs de eventos do Windows em massa. Fornece a capacidade de recuperar diferentes tipos de logs de eventos, incluindo logs de eventos clássicos do Windows, como logs de Sistema e Aplicativo, logs gerados pela tecnologia Windows Event Log e logs de Rastreamento de Eventos para Windows (ETW).

Podemos usar o parâmetro ``-ListLog`` junto com o cmdlet Get-WinEvent. Ao especificar ``*`` como valor do parâmetro, recuperamos todos os logs sem aplicar nenhum critério de filtragem.

```powershell-session
PS C:\Users\Administrator> Get-WinEvent -ListLog * | Select-Object LogName, RecordCount, IsClassicLog, IsEnabled, LogMode, LogType | Format-Table -AutoSize

LogName                                                                                RecordCount IsClassicLog IsEnabled  LogMode        LogType
-------                                                                                ----------- ------------ ---------  -------        -------
Windows PowerShell                                                                            2916         True      True Circular Administrative
System                                                                                        1786         True      True Circular Administrative
Security                                                                                      8968         True      True Circular Administrative
Key Management Service                                                                           0         True      True Circular Administrative
Internet Explorer                                                                                0         True      True Circular Administrative
HardwareEvents                                                                                   0         True      True Circular Administrative
Application                                                                                   2079         True      True Circular Administrative
Windows Networking Vpn Plugin Platform/OperationalVerbose                                                 False     False Circular    Operational
Windows Networking Vpn Plugin Platform/Operational                                                        False     False Circular    Operational
SMSApi                                                                                           0        False      True Circular    Operational
Setup                                                                                           16        False      True Circular    Operational
OpenSSH/Operational                                                                              0        False      True Circular    Operational
OpenSSH/Admin                                                                                    0        False      True Circular Administrative
<SNIP>
```

Este comando nos fornece informações valiosas sobre cada log, incluindo o nome do log, o número de registros presentes, se o log está no formato `.evt` clássico ou no formato `.evtx` mais recente, seu status habilitado, o modo de log (Circular, Reter ou Backup Automático) e o tipo de log (Administrativo, Analítico, Depuração ou Operacional).

Além disso, podemos explorar os provedores de log de eventos associados a cada log usando o parâmetro `-ListProvider`. Os provedores de log de eventos servem como fontes de eventos dentro dos logs. Executar o comando a seguir nos permite recuperar a lista de provedores e seus respectivos logs vinculados.

![[Pasted image 20240829151736.png]]

Este comando nos fornece uma visão geral dos provedores disponíveis e suas associações com logs específicos. Ele nos permite identificar provedores de interesse para fins de filtragem.

Agora, vamos nos concentrar em recuperar logs de eventos específicos usando o cmdlet Get-WinEvent. Em seu nível mais básico, o Get-WinEvent recupera logs de eventos de computadores locais ou remotos. Os exemplos abaixo demonstram como recuperar eventos de vários logs.

##### 1. **Recuperando eventos do log do sistema**
```powershell
PS C:\Users\Administrator> Get-WinEvent -LogName 'System' -MaxEvents 50 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
TimeCreated            Id ProviderName                             LevelDisplayName Message
-----------            -- ------------                             ---------------- -------
6/2/2023 9:41:42 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\MicrosoftWindows.Client.CBS_cw5...
6/2/2023 9:38:32 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.Windows.ShellExperien...
6/2/2023 9:38:32 AM 10016 Microsoft-Windows-DistributedCOM         Warning          The machine-default permission settings do not grant Local Activation permission for the COM Server applicat...
6/2/2023 9:37:31 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.WindowsAlarms_8wekyb3...
6/2/2023 9:37:31 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\microsoft.windowscommunications...
6/2/2023 9:37:31 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.Windows.ContentDelive...
6/2/2023 9:36:35 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.YourPhone_8wekyb3d8bb...
6/2/2023 9:36:32 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n...
6/2/2023 9:36:30 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.Windows.Search_cw5n1h...
6/2/2023 9:36:29 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Packages\Microsoft.Windows.StartMenuExpe...
6/2/2023 9:36:14 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\AppData\Local\Microsoft\Windows\UsrClass.dat was clear...
6/2/2023 9:36:14 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Users\Administrator\ntuser.dat was cleared updating 2366 keys and creating...
6/2/2023 9:36:14 AM  7001 Microsoft-Windows-Winlogon               Information      User Logon Notification for Customer Experience Improvement Program	
6/2/2023 9:33:04 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Windows\AppCompat\Programs\Amcache.hve was cleared updating 920 keys and c...
6/2/2023 9:31:54 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Windows\ServiceProfiles\NetworkService\AppData\Local\Microsoft\Windows\Del...
6/2/2023 9:30:23 AM    16 Microsoft-Windows-Kernel-General         Information      The access history in hive \??\C:\Windows\System32\config\COMPONENTS was cleared updating 54860 keys and cre...
6/2/2023 9:30:16 AM    15 Microsoft-Windows-Kernel-General         Information      Hive \SystemRoot\System32\config\DRIVERS was reorganized with a starting size of 3956736 bytes and an ending...
6/2/2023 9:30:10 AM  1014 Microsoft-Windows-DNS-Client             Warning          Name resolution for the name settings-win.data.microsoft.com timed out after none of the configured DNS serv...
6/2/2023 9:29:54 AM  7026 Service Control Manager                  Information      The following boot-start or system-start driver(s) did not load: ...
6/2/2023 9:29:54 AM 10148 Microsoft-Windows-WinRM                  Information      The WinRM service is listening for WS-Management requests. ...
6/2/2023 9:29:51 AM 51046 Microsoft-Windows-DHCPv6-Client          Information      DHCPv6 client service is started
--- SNIP ---
</pre>
Este exemplo recupera os primeiros 50 eventos do log do Sistema. Ele seleciona propriedades específicas, incluindo o tempo de criação do evento, ID, nome do provedor, nome de exibição do nível e mensagem. Isso facilita a análise e a solução de problemas.

##### 2. **Recuperando eventos de Microsoft-Windows-WinRM/Operational**
```powershell
PS C:\Users\Administrator> Get-WinEvent -LogName 'Microsoft-Windows-WinRM/Operational' -MaxEvents 30 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
TimeCreated            Id ProviderName            LevelDisplayName Message
-----------            -- ------------            ---------------- -------
6/2/2023 9:30:15 AM   132 Microsoft-Windows-WinRM Information      WSMan operation Enumeration completed successfully
6/2/2023 9:30:15 AM   145 Microsoft-Windows-WinRM Information      WSMan operation Enumeration started with resourceUri...
6/2/2023 9:30:15 AM   132 Microsoft-Windows-WinRM Information      WSMan operation Enumeration completed successfully
6/2/2023 9:30:15 AM   145 Microsoft-Windows-WinRM Information      WSMan operation Enumeration started with resourceUri...
6/2/2023 9:29:54 AM   209 Microsoft-Windows-WinRM Information      The Winrm service started successfully
--- SNIP ---
</pre>

Neste exemplo, os eventos são recuperados do log Microsoft-Windows-WinRM/Operational. O comando recupera os primeiros 30 eventos e seleciona propriedades relevantes para exibição.

Para recuperar os eventos mais antigos, em vez de classificar manualmente os resultados, podemos utilizar o parâmetro `-Oldest` com o cmdlet Get-WinEvent. Este parâmetro nos permite recuperar os primeiros eventos com base em sua ordem cronológica.

O comando a seguir demonstra como recuperar os 30 eventos mais antigos do log 'Microsoft-Windows-WinRM/Operational'.

```powershell
PS C:\Users\Administrator> Get-WinEvent -LogName 'Microsoft-Windows-WinRM/Operational' -Oldest -MaxEvents 30 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
TimeCreated           Id ProviderName            LevelDisplayName Message
-----------            -- ------------            ---------------- -------
8/3/2022 4:41:38 PM  145 Microsoft-Windows-WinRM Information      WSMan operation Enumeration started with resourceUri ...
8/3/2022 4:41:42 PM  254 Microsoft-Windows-WinRM Information      Activity Transfer
8/3/2022 4:41:42 PM  161 Microsoft-Windows-WinRM Error            The client cannot connect to the destination specifie...
8/3/2022 4:41:42 PM  142 Microsoft-Windows-WinRM Error            WSMan operation Enumeration failed, error code 215085...
8/3/2022 9:51:03 AM  145 Microsoft-Windows-WinRM Information      WSMan operation Enumeration started with resourceUri ...
8/3/2022 9:51:07 AM  254 Microsoft-Windows-WinRM Information      Activity Transfer
</pre>

##### 3. **Recuperando eventos de arquivos .evtx**
Se você tiver um arquivo exportado um log `.evtx` de outro computador ou tiver feito backup de um log existente, você pode utilizar o cmdlet Get-WinEvent para ler e consultar esses logs. Esse recurso é particularmente útil para fins de auditoria ou quando você precisa analisar logs dentro de scripts.

Para recuperar entradas de log de um `.evtx file`, você precisa fornecer o caminho do arquivo de log usando o parâmetro `-Path` .

```powershell
PS C:\Users\Administrator> Get-WinEvent -Path 'C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Execution\exec_sysmon_1_lolbin_pcalua.evtx' -MaxEvents 5 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow:scroll;">
TimeCreated           Id ProviderName             LevelDisplayName Message
-----------           -- ------------             ---------------- -------
5/12/2019 10:01:51 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
5/12/2019 10:01:50 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
5/12/2019 10:01:43 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
</pre>

##### 4. **Filtrando eventos com FilterHashtable**
Para filtrar logs de eventos do Windows, podemos usar o parâmetro `-FilterHashtable`, que nos permite definir condições específicas para os logs que queremos recuperar.

```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1,3} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
TimeCreated           Id ProviderName             LevelDisplayName Message
-----------           -- ------------             ---------------- -------
6/2/2023 10:40:09 AM   1 Microsoft-Windows-Sysmon Information      Process Create:...
6/2/2023 10:39:01 AM   1 Microsoft-Windows-Sysmon Information      Process Create:...
6/2/2023 10:34:12 AM   1 Microsoft-Windows-Sysmon Information      Process Create:...
6/2/2023 10:33:26 AM   1 Microsoft-Windows-Sysmon Information      Process Create:...
6/2/2023 10:33:16 AM   1 Microsoft-Windows-Sysmon Information      Process Create:...
6/2/2023 9:36:10 AM    3 Microsoft-Windows-Sysmon Information      Network connection detected:...
5/29/2023 6:30:26 PM   1 Microsoft-Windows-Sysmon Information      Process Create:...
5/29/2023 6:30:24 PM   3 Microsoft-Windows-Sysmon Information      Network connection detected:...
</pre>

O comando acima recupera eventos com IDs 1 e 3 do log de eventos `Microsoft-Windows-Sysmon/Operational`, seleciona propriedades específicas desses eventos e os exibe em um formato de tabela.

>[!NOTA] **Nota** : Se observarmos os IDs de eventos Sysmon 1 e 3 (relacionados a binários "perigosos" ou incomuns) ocorrendo em um curto período de tempo, isso pode indicar a presença de um processo se comunicando com um servidor de Comando e Controle (C2).

Para eventos exportados, o comando equivalente é o seguinte.
```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{Path='C:\Tools\chainsaw\EVTX-ATTACK-SAMPLES\Execution\sysmon_mshta_sharpshooter_stageless_meterpreter.evtx'; ID=1,3} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
TimeCreated           Id ProviderName             LevelDisplayName Message
-----------           -- ------------             ---------------- -------
6/15/2019 12:14:32 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
6/15/2019 12:13:44 AM  3 Microsoft-Windows-Sysmon Information      Network connection detected:...
6/15/2019 12:13:42 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
</pre>

>[!NOTA] Observação : esses logs estão relacionados a um processo que se comunica com um servidor de Comando e Controle (C2) logo após sua criação.

Se quisermos obter logs de eventos com base em um intervalo de datas ( `5/28/23 - 6/2/2023`), isso pode ser feito da seguinte maneira.

```powershell
PS C:\Users\Administrator> $startDate = (Get-Date -Year 2023 -Month 5 -Day 28).Date

PS C:\Users\Administrator> $endDate   = (Get-Date -Year 2023 -Month 6 -Day 3).Date

PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1,3; StartTime=$startDate; EndTime=$endDate} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

>[!NOTA] **Nota** : O acima filtrará entre a data de início inclusiva e a data de término exclusiva. É por isso que especificamos 3 de junho e não 2.

##### 5. **Filtrando eventos com FilterHashtable e XML**
Considere um cenário de detecção de intrusão onde uma conexão de rede suspeita para um IP específico ( `52.113.194.132` ) foi identificada. Com o Sysmon instalado, você pode usar logs [de ID de Evento 3 (Conexão de Rede)](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90003) para investigar a ameaça potencial.

```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=3} |
`ForEach-Object {
$xml = [xml]$_.ToXml()
$eventData = $xml.Event.EventData.Data
New-Object PSObject -Property @{
    SourceIP = $eventData | Where-Object {$_.Name -eq "SourceIp"} | Select-Object -ExpandProperty '#text'
    DestinationIP = $eventData | Where-Object {$_.Name -eq "DestinationIp"} | Select-Object -ExpandProperty '#text'
    ProcessGuid = $eventData | Where-Object {$_.Name -eq "ProcessGuid"} | Select-Object -ExpandProperty '#text'
    ProcessId = $eventData | Where-Object {$_.Name -eq "ProcessId"} | Select-Object -ExpandProperty '#text'
}
}  | Where-Object {$_.DestinationIP -eq "52.113.194.132"}
```
<pre style="overflow-x:scroll;">
DestinationIP  ProcessId SourceIP       ProcessGuid
-------------  --------- --------       -----------
52.113.194.132 9196      10.129.205.123 {52ff3419-51ad-6475-1201-000000000e00}
52.113.194.132 5996      10.129.203.180 {52ff3419-54f3-6474-3d03-000000000c00}
</pre>

Este script recuperará todos os eventos de conexão de rede Sysmon (ID 3), analisará os dados XML de cada evento para recuperar detalhes específicos (IP de origem, IP de destino, GUID do processo e ID do processo) e filtrará os resultados para incluir apenas eventos em que o IP de destino corresponde ao IP suspeito.

Além disso, podemos usar o `ProcessGuid` para rastrear o processo original que fez a conexão, o que nos permite entender a árvore de processos e identificar quaisquer executáveis ​​ou scripts maliciosos.

Você pode se perguntar como poderíamos estar cientes de `Event.EventData.Data`. O formato Windows XML EventLog (EVTX) pode ser encontrado [aqui](https://github.com/libyal/libevtx/blob/main/documentation/Windows%20XML%20Event%20Log%20(EVTX).asciidoc) .

O comando abaixo está aproveitando o Event ID 7 do Sysmon para detectar o carregamento das DLLs `clr.dll` e `mscoree.dll` de carregamento em processos que normalmente não as exigiriam.

```powershell
PS C:\Users\Administrator> $Query = @"
	<QueryList>
		<Query Id="0">
			<Select Path="Microsoft-Windows-Sysmon/Operational">*[System[(EventID=7)]] and *[EventData[Data='mscoree.dll']] or *[EventData[Data='clr.dll']]
			</Select>
		</Query>
	</QueryList>
	"@
```
```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterXml $Query | ForEach-Object {Write-Host $_.Message `n}
```
![[Pasted image 20240829155459.png]]

##### 6. **Filtrando eventos com FilterXPath**
Para usar consultas XPath com Get-WinEvent, precisamos usar o parâmetro `-FilterXPath`. Isso nos permite criar uma consulta XPath para filtrar os logs de eventos.

Por exemplo, se quisermos obter eventos Process Creation ( [Sysmon Event ID 1](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90001) ) no log Sysmon para identificar a instalação de qualquer ferramenta [Sysinterals](https://learn.microsoft.com/en-us/sysinternals/) , podemos usar o comando abaixo.

>[!NOTA] Nota : Durante a instalação de uma ferramenta Sysinternals, o usuário deve aceitar o EULA apresentado. A ação de aceitação envolve a chave de registro incluída no comando abaixo.

```powershell
 PS C:\Users\Administrator> Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -FilterXPath "*[EventData[Data[@Name='Image']='C:\Windows\System32\reg.exe']] and *[EventData[Data[@Name='CommandLine']='`"C:\Windows\system32\reg.exe`" ADD HKCU\Software\Sysinternals /v EulaAccepted /t REG_DWORD /d 1 /f']]" | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```
<pre style="overflow-x:scroll;">
 TimeCreated           Id ProviderName             LevelDisplayName Message
-----------           -- ------------             ---------------- -------
5/29/2023 12:44:46 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
5/29/2023 12:29:53 AM  1 Microsoft-Windows-Sysmon Information      Process Create:...
</pre>

> [!NOTA] Observação : Imagee CommandLinepode ser identificado navegando pela representação XML de qualquer evento Sysmon com ID 1 através, por exemplo, do Visualizador de Eventos.
![[Pasted image 20240829160002.png]]

Por fim, suponha que queremos investigar quaisquer conexões de rede para um endereço IP suspeito específico (`52.113.194.132`) que o Sysmon registrou. Para fazer isso, poderíamos usar o seguinte comando.

```powershell
PS C:\Users\Administrator> Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -FilterXPath "*[System[EventID=3] and EventData[Data[@Name='DestinationIp']='52.113.194.132']]"
```
<pre style="overflow-x:scroll;">
ProviderName: Microsoft-Windows-Sysmon

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
5/29/2023 6:30:24 PM              3 Information      Network connection detected:...
5/29/2023 12:32:05 AM             3 Information      Network connection detected:...
</pre>

##### 7. **Filtrando eventos com base em valores de propriedade**
O parâmetro `-Property *`, quando usado com `Select-Object`, instrui o comando a selecionar todas as propriedades dos objetos passados ​​a ele.

No contexto do comando Get-WinEvent, essas propriedades incluirão todas as informações disponíveis sobre o evento. Vamos ver um exemplo que nos apresentará todas as propriedades dos logs de ID de evento 1 do Sysmon.

```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1} -MaxEvents 1 | Select-Object -Property *
```
![[Pasted image 20240829161033.png]]

Vamos agora ver um exemplo de um comando que recupera eventos `Process Create` do log `Microsoft-Windows-Sysmon/Operational`, verifica a linha de comando pai de cada evento em busca da string `-enc` e, em seguida, exibe todas as propriedades de quaisquer eventos correspondentes como uma lista.

```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; ID=1} | Where-Object {$_.Properties[21].Value -like "*-enc*"} | Format-List
```
![[Pasted image 20240829161246.png]]

- `| Where-Object {$_.Properties[21].Value -like "*-enc*"}`: Esta parte do comando filtra ainda mais os eventos recuperados. O caractere '|' (operador pipe) passa a saída do comando anterior (ou seja, os eventos filtrados) para o cmdlet 'Where-Object'. O cmdlet 'Where-Object' filtra a saída com base no bloco de script que o segue.
	- `$_`: No bloco de script, $_ se refere ao objeto atual no pipeline, ou seja, cada evento individual que foi recuperado e passado do comando anterior.
	- `.Properties[21].Value`: A `Properties`propriedade de um evento Sysmon "Process Create" é um array contendo vários dados sobre o evento. O índice específico `21`corresponde à `ParentCommandLine`propriedade do evento, que contém a linha de comando exata usada para iniciar o processo.
	![[Pasted image 20240829161459.png]]
	- `-like "*-enc*"`: Este é um operador de comparação que corresponde a strings com base em uma string curinga, onde `*` representa qualquer sequência de caracteres. Neste caso, ele está procurando por qualquer linha de comando que contenha `-enc` em qualquer lugar dentro delas. A `-enc` string pode ser parte de comandos suspeitos, por exemplo, é um parâmetro comum em comandos do PowerShell para denotar um comando codificado que pode ser usado para ofuscar scripts maliciosos.
	- `| Format-List`: Finalmente, a saída do comando anterior (os eventos que atendem à condição especificada) é passada para o `Format-List` cmdlet. Este cmdlet exibe as propriedades dos objetos de entrada como uma lista, facilitando a leitura e a análise.

























































































