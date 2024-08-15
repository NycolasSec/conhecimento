## Noções básicas do Sysmon

Na investigação de **eventos maliciosos**, certos IDs de eventos no Windows são indicadores comuns de comprometimento. Por exemplo:

- **Event ID 4624**: Fornece insights sobre novos eventos de logon, permitindo o monitoramento e a detecção de padrões de acesso suspeitos.
- **Event ID 4688**: Fornece informações sobre processos recém-criados, ajudando a identificar lançamentos de processos incomuns ou maliciosos.

Para aprimorar o monitoramento e a análise de logs de eventos, o **System Monitor (Sysmon)** pode ser incorporado. O Sysmon é um serviço do Windows que monitora e registra atividades detalhadas do sistema, como a criação de processos, conexões de rede e alterações em arquivos, permanecendo ativo mesmo após reinicializações do sistema. 

### Principais componentes do Sysmon:
- **Serviço do Windows**: Monitora a atividade do sistema.
- **Driver de dispositivo**: Captura dados de atividade do sistema.
- **Log de eventos**: Exibe dados das atividades monitoradas.

O Sysmon é especialmente valioso porque registra informações detalhadas que normalmente não aparecem nos logs de eventos de segurança, tornando-o uma ferramenta poderosa para monitoramento profundo e análise forense em cibersegurança.

### IDs de eventos do Sysmon:
- **Event ID 1**: Criação de Processos.
- **Event ID 3**: Conexões de Rede.

O Sysmon utiliza um arquivo de configuração baseado em XML que permite um controle granular sobre quais eventos são registrados, possibilitando a inclusão ou exclusão de eventos com base em atributos específicos, como nomes de processos ou endereços IP.
A lista completa de IDs de eventos do Sysmon pode ser encontrada [aqui](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) .

Arquivos de configuração populares para o Sysmon podem ser usados para otimizar o monitoramento de eventos e melhorar a segurança do sistema.

- Para uma configuração abrangente, podemos visitar: [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) . <--`We will use this one in this section!`
- Outra opção é: [https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular) , que fornece uma abordagem modular.

Para começar, você pode instalar o Sysmon baixando-o da documentação oficial da Microsoft ( [https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) ). Após o download, abra um prompt de comando de administrador e execute o seguinte comando para instalar o Sysmon.

```shell-session
C:\Tools\Sysmon> sysmon.exe -i -accepteula -h md5,sha256,imphash -l -n
```
![[Pasted image 20240815171355.png]]

Para utilizar uma configuração Sysmon personalizada, execute o seguinte após instalar o Sysmon.
```shell-session
C:\Tools\Sysmon> sysmon.exe -c filename.xml
```

**Nota** : É importante ressaltar que [o Sysmon para Linux](https://github.com/Sysinternals/SysmonForLinux) também existe.

## Exemplo de detecção 1: Detectando sequestro de DLL

Em nosso caso de uso específico, pretendemos detectar um sequestro de DLL. Os IDs de log de eventos Sysmon relevantes para sequestro de DLL podem ser encontrados na documentação do Sysmon ( [https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) ). 

Para detectar um sequestro de DLL, precisamos nos concentrar em `Event Type 7`, que corresponde a eventos de carregamento de módulo. Para conseguir isso, precisamos modificar o `sysmonconfig-export.xml`arquivo de configuração Sysmon que baixamos de [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) .

Ao examinar a configuração modificada, podemos observar que o comentário "include" significa eventos que devem ser incluídos.

![[image14.webp]]

No caso de detecção de sequestros de DLL, alteramos o "include" para "exclude" para garantir que nada seja excluído, permitindo-nos capturar os dados necessários.

![[Pasted image 20240815171846.png]]

Para utilizar a configuração atualizada do Sysmon, execute o seguinte.
```shell-session
C:\Tools\Sysmon> sysmon.exe -c sysmonconfig-export.xml
```

![[Pasted image 20240815171934.png]]

Com a configuração Sysmon modificada, podemos começar a observar eventos de carregamento de imagem. Para visualizar esses eventos, navegue até o Event Viewer e acesse "Applications and Services" -> "Microsoft" -> "Windows" -> "Sysmon". Uma verificação rápida revelará a presença do ID do evento alvo.

![[Pasted image 20240815172144.png]]

Vamos agora ver como se parece um evento Sysmon ID 7.

![[Pasted image 20240815172209.png]]

O log de eventos contém o status de assinatura da DLL (nesse caso, é assinada pela Microsoft), o processo ou imagem responsável por carregar a DLL e a DLL específica que foi carregada.

Em nosso exemplo, observamos que "MMC.exe" carregou "psapi.dll", que também é assinada pela Microsoft. Ambos os arquivos estão localizados no diretório System32.

Agora, vamos prosseguir com a construção de um mecanismo de detecção. Para obter mais insights sobre sequestros de DLL, conduzir pesquisas é essencial. Nós tropeçamos em uma [postagem de blog](https://www.wietzebeukema.nl/blog/hijacking-dlls-in-windows) informativa que fornece uma lista exaustiva de várias técnicas de sequestro de DLL. Para o propósito de nossa detecção, vamos nos concentrar em um sequestro específico envolvendo o executável vulnerável calc.exe e uma lista de DLLs que podem ser sequestradas.

![[Pasted image 20240815172537.png]]

Vamos tentar o hijack usando "calc.exe" e "WININET.dll" como exemplo. Para simplificar o processo, podemos utilizar [a DLL reflexiva](https://github.com/stephenfewer/ReflectiveDLLInjection/tree/master/bin) "hello world" de Stephen Fewer . Deve-se notar que o hijacking de DLL não requer DLLs reflexivas.

Seguindo os passos necessários, que envolvem renomear `reflective_dll.x64.dll`para `WININET.dll`, mover `calc.exe`de `C:\Windows\System32`junto com `WININET.dll`para um diretório gravável (como a `Desktop`pasta) e executar `calc.exe`, obtemos sucesso. Em vez do aplicativo Calculator, uma [MessageBox](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-messageboxa) é exibida.

![[Pasted image 20240815172831.png]]

Em seguida, analisamos o impacto do sequestro. Primeiro, filtramos os logs de eventos para focar em `Event ID 7`, que representa eventos de carga de módulo, clicando em "Filtrar Log Atual...".

![[Pasted image 20240815172908.png]]

Posteriormente, procuramos por instâncias de "calc.exe", clicando em "Localizar...", para identificar a carga de DLL associada ao nosso sequestro.

![[Pasted image 20240815173012.png]]

A saída do Sysmon fornece insights valiosos. Agora, podemos observar vários indicadores de comprometimento (IOCs) para criar regras de detecção eficazes. Antes de prosseguir, vamos comparar isso a uma carga autenticada de "wininet.dll" por "calc.exe".

![[Pasted image 20240815173035.png]]

**Vamos explorar esses IOCs:**
1. "calc.exe", originalmente localizado em System32, não deve ser encontrado em um diretório gravável. Portanto, uma cópia de "calc.exe" em um diretório gravável serve como um IOC, pois ele deve sempre residir em System32 ou potencialmente Syswow64.
    
2. "WININET.dll", originalmente localizado no System32, não deve ser carregado fora do System32 pelo calc.exe. Se instâncias de carregamento de "WININET.dll" ocorrerem fora do System32 com "calc.exe" como processo pai, isso indica um sequestro de DLL dentro do calc.exe. Embora seja necessário cuidado ao alertar sobre todas as instâncias de carregamento de "WININET.dll" fora do System32 (pois alguns aplicativos podem empacotar versões específicas de DLL para estabilidade), no caso de "calc.exe", podemos afirmar com segurança um sequestro devido ao nome imutável da DLL, que os invasores não podem modificar para evitar a detecção.
    
3. O "WININET.dll" original é assinado pela Microsoft, enquanto nossa DLL injetada permanece não assinada.

Esses três IOCs poderosos fornecem um meio eficaz de detectar um sequestro de DLL envolvendo calc.exe. É importante observar que, embora Sysmon e logs de eventos ofereçam telemetria valiosa para caçar e criar regras de alerta, eles não são as únicas fontes de informação.

## Exemplo de detecção 2: Detectando injeção de PowerShell/C-Sharp não gerenciada

Antes de nos aprofundarmos nas técnicas de detecção, vamos entender brevemente o C# e seu ambiente de tempo de execução. O C# é considerado uma linguagem "gerenciada", o que significa que ele requer um tempo de execução de backend para executar seu código. O [Common Language Runtime (CLR)](https://docs.microsoft.com/en-us/dotnet/standard/clr) serve como esse ambiente de tempo de execução. [O código gerenciado](https://docs.microsoft.com/en-us/dotnet/standard/managed-code) não é executado diretamente como assembly; em vez disso, ele é compilado em um formato de bytecode que o tempo de execução processa e executa. Consequentemente, um processo gerenciado depende do CLR para executar o código C#.

Como defensores, podemos aproveitar esse conhecimento para detectar injeções ou execuções incomuns de C# em nosso ambiente. Para fazer isso, podemos utilizar um utilitário útil chamado [Process Hacker](https://processhacker.sourceforge.io/) .

![[image24.webp]]

Ao usar o Process Hacker, podemos observar uma variedade de processos em nosso ambiente. Classificando os processos por nome, podemos identificar distinções interessantes codificadas por cores. Notavelmente, "powershell.exe", um processo gerenciado, é destacado em verde em comparação a outros processos. Passar o mouse sobre powershell.exe revela o rótulo "Process is managed (.NET)", confirmando seu status gerenciado.

![[image25.webp]]

Examinando as cargas do módulo para `powershell.exe`, clicando com o botão direito do mouse em `powershell.exe`, clicando em "Propriedades" e navegando até "Módulos", podemos encontrar informações relevantes.

![[Pasted image 20240815173546.png]]

A presença de "Microsoft .NET Runtime...", `clr.dll`e `clrjit.dll`deve atrair nossa atenção. Essas 2 DLLs são usadas quando o código C# é executado como parte do tempo de execução para executar o bytecode. Se observarmos essas DLLs carregadas em processos que normalmente não as exigem, isso sugere um potencial ataque [de execute-assembly](https://www.cobaltstrike.com/blog/cobalt-strike-3-11-the-snake-that-eats-its-tail/) ou [de injeção de PowerShell não gerenciado](https://www.youtube.com/watch?v=7tvfb9poTKg&ab_channel=RaphaelMudge) .

Para demonstrar a injeção de PowerShell não gerenciada, podemos injetar uma [DLL não gerenciada do tipo PowerShell](https://github.com/leechristensen/UnmanagedPowerShell) em um processo aleatório, como `spoolsv.exe`. Podemos fazer isso utilizando o [projeto PSInject](https://github.com/EmpireProject/PSInject) da seguinte maneira.

```powershell
 powershell -ep bypass
 Import-Module .\Invoke-PSInject.ps1
 Invoke-PSInject -ProcId [Process ID of spoolsv.exe] -PoshCode "V3JpdGUtSG9zdCAiSGVsbG8sIEd1cnU5OSEi"
```

![[Pasted image 20240815174206.png]]

Após a injeção, observamos que "spoolsv.exe" passa de um estado não gerenciado para um estado gerenciado.

![[Pasted image 20240815174230.png]]

Além disso, consultando as guias "Módulos" relacionadas do Process Hacker e do Sysmon `Event ID 7`, podemos examinar as informações de carga da DLL para validar a presença das DLLs mencionadas acima.

![[Pasted image 20240815174251.png]]

![[Pasted image 20240815174257.png]]

## Exemplo de detecção 3: Detectando Dumping de Credenciais

Outro aspecto crítico da segurança cibernética é detectar atividades de dumping de credenciais. Uma ferramenta amplamente usada para dumping de credenciais é [o Mimikatz](https://github.com/gentilkiwi/mimikatz) , que oferece vários métodos para extrair credenciais do Windows.

Um comando específico, "sekurlsa::logonpasswords", permite o dumping de hashes de senha ou senhas de texto simples acessando o [Local Security Authority Subsystem Service (LSASS)](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) . O LSASS é responsável por gerenciar credenciais de usuário e é um alvo principal para ferramentas de dumping de credenciais como o Mimikatz.

O ataque pode ser executado da seguinte forma.

```cmd-session
C:\Tools\Mimikatz> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #18362 Feb 29 2020 11:13:36
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 1128191 (00000000:001136ff)
Session           : RemoteInteractive from 2
User Name         : Administrator
Domain            : DESKTOP-NU10MTO
Logon Server      : DESKTOP-NU10MTO
Logon Time        : 5/31/2023 4:14:41 PM
SID               : S-1-5-21-2712802632-2324259492-1677155984-500
        msv :
         [00000003] Primary
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * NTLM     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
         * SHA1     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX0812156b
        tspkg :
        wdigest :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        kerberos :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        ssp :   KO
        credman :
```

Como podemos ver, a saída do comando "sekurlsa::logonpasswords" fornece informações importantes sobre credenciais comprometidas.

Para detectar essa atividade, podemos confiar em um evento Sysmon diferente. Em vez de focar em cargas de DLL, mudamos nossa atenção para eventos de acesso ao processo. Ao verificar `Sysmon event ID 10`, que representa eventos "ProcessAccess", podemos identificar quaisquer tentativas suspeitas de acessar o LSASS.

![Imagem](https://academy.hackthebox.com/storage/modules/216/image32.png) 

![Imagem](https://academy.hackthebox.com/storage/modules/216/image33.png)

Por exemplo, se observarmos um arquivo aleatório ("AgentEXE" neste caso) de uma pasta aleatória ("Downloads" neste caso) tentando acessar o LSASS, isso indica um comportamento incomum.

Além disso, o `SourceUser`ser diferente do `TargetUser`(por exemplo, "waldo" como o `SourceUser`e "SYSTEM" como o `TargetUser`) enfatiza ainda mais a anormalidade. Também vale a pena notar que, como parte do processo de despejo de credenciais baseado em mimikatz, o usuário deve solicitar [SeDebugPrivileges](https://devblogs.microsoft.com/oldnewthing/20080314-00/?p=23113) . Como o nome sugere, ele é usado principalmente para depuração. Isso pode ser outro Indicador de Comprometimento (IOC).


















































































































































































