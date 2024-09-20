O **Tcpdump** é uma ferramenta de linha de comando usada para capturar e interpretar pacotes de rede em sistemas Unix e seus derivados. Ela utiliza as bibliotecas **pcap** e **libpcap** para capturar tráfego de rede diretamente da interface em modo promíscuo, permitindo monitorar todo o tráfego na rede local.

Embora pareça complexa inicialmente, a ferramenta se torna mais fácil de usar após dominar as funções básicas. Tcpdump é amplamente utilizado em sistemas como Linux, BSD e Solaris, e requer privilégios de **root** ou **sudo** para ser executado devido ao acesso direto ao hardware.

#### Localizar Tcpdump
Para validar se o pacote existe em nosso host, use o seguinte comando:
```shell-session
NycolasRamos@htb[/htb]$ which tcpdump
```

Frequentemente ele pode ser encontrado em `/usr/sbin/tcpdump`. No entanto, se o pacote não existir, podemos instalá-lo com:

#### Instalar Tcpdump
```shell-session
NycolasRamos@htb[/htb]$ sudo apt install tcpdump 
```

Podemos executar o pacote tcpdump com a `--version`opção para verificar nossa instalação e a versão atual do pacote para validar nossa instalação.

#### Validação da versão do Tcpdump
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump --version
```
![[Pasted image 20240918094422.png]]

## Capturas de tráfego com Tcpdump
De início devemos aprender os recursos essenciais da ferramenta. Vamos discutir algumas opções básicas do TCPDump, demonstrar alguns comandos e mostrar como salvar tráfego em `PCAP`arquivos e ler a partir deles.

#### Opções básicas de captura
Abaixo está uma tabela das flags básicas do Tcpdump que podemos usar para manipular as capturas. Eles podem ser encadeados para criar como a saída da ferramenta é mostrada no STDOUT e o que é o alvo no arquivo de captura.

| **Comando de troca** | **Resultado**                                                                                                                  |
| :------------------: | ------------------------------------------------------------------------------------------------------------------------------ |
|          D           | Serão exibidas todas as interfaces disponíveis para captura.                                                                   |
|          i           | Seleciona uma interface para capturar. ex. -i eth0                                                                             |
|          n           | Não resolva nomes de host.                                                                                                     |
|          nn          | Não resolva nomes de host ou portas conhecidas.                                                                                |
|          e           | Pegará o cabeçalho Ethernet junto com os dados da camada superior.                                                             |
|          X           | Mostrar conteúdo dos pacotes em hexadecimal e ASCII.                                                                           |
|          XX          | O mesmo que X, mas também especificará cabeçalhos Ethernet. (como usar Xe)                                                     |
|      v, vv, vvv      | Aumenta a verbosidade da saída mostrada e salva.                                                                               |
|          c           | Pegue um número específico de pacotes e saia do programa.                                                                      |
|          s           | Define a quantidade de um pacote que deve ser pega.                                                                            |
|          S           | alterar números de sequência relativos na exibição de captura para números de sequência absolutos. (13248765839 em vez de 101) |
|          q           | Imprima menos informações de protocolo.                                                                                        |
|    r arquivo.pcap    | Ler de um arquivo.                                                                                                             |
|    w arquivo.pcap    | Escrever em um arquivo                                                                                                         |

#### Utilização da página de manual
Para ver a lista completa de opções, podemos utilizar as páginas de manual:

#### Página de manual do Tcpdump
```shell-session
NycolasRamos@htb[/htb]$ man tcpdump
```

Aqui estão alguns exemplos de uso básico do switch Tcpdump, juntamente com descrições do que está acontecendo:

#### Listando Interfaces Disponíveis
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -D
```
![[Pasted image 20240918100716.png]]

O comando acima chama tcpdump usando privilégios sudo e lista as interfaces de rede utilizáveis. Podemos escolher uma dessas interfaces de rede e dizer ao tcpdump quais interfaces ele deve escutar.

#### Escolhendo uma interface para capturar
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0
```
![[Pasted image 20240918100800.png]]

Neste terminal, estamos chamando tcpdump e selecionando a interface eth0 para capturar o tráfego. Assim que emitirmos esse comando ele começará a escutar o tráfego e mostrará os pacotes pela interface. Ao emitirmos a chave ``-nn`` como visto abaixo, dizemos ao TCPDump para se abster de resolver endereços IP e números de porta para seus nomes de host e nomes de porta comuns. Nesta representação, o último octeto é a porta de/para a qual a conexão vai.

#### Desativar resolução de nomes
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 -nn
```
![[Pasted image 20240918101510.png]]

Ao utilizar o switch `-e`, estamos encarregando o tcpdump de incluir os cabeçalhos ethernet na saída da captura junto com seu conteúdo regular. Normalmente, o primeiro e o segundo campos consistem no Timestamp e, em seguida, no início do cabeçalho IP. Agora, ele consiste no Timestamp e no Endereço MAC de origem do host.

#### Exibir o cabeçalho Ethernet
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 -e
```
![[Pasted image 20240918101833.png]]

Ao emitir o switch `-X`, podemos ver o pacote um pouco mais claro agora. Obtemos uma saída ASCII à direita para interpretar qualquer coisa em texto claro que corresponda à saída hexadecimal à esquerda.

#### Incluir saída ASCII e Hex
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 -X
```
![[Pasted image 20240918101912.png]]

Notaremos que temos informações sobre as opções de cabeçalho IP, como tempo de vida, deslocamento e outros sinalizadores, e mais detalhes sobre os protocolos da camada superior. Abaixo, estamos combinando os switches para criar a saída de acordo com nossa preferência.

#### Combinações de Switch Tcpdump
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 -nnvXX
```
![[Pasted image 20240918102013.png]]
Ao utilizar os interruptores, encadeá-los como no exemplo `above`é a melhor prática.

## Saída Tcpdump
A imagem e a tabela abaixo definirão cada campo. Tenha em mente que quanto mais verboso formos com nossos filtros, mais detalhes de cada cabeçalho serão mostrados.

#### Análise do shell Tcpdump

![[Pasted image 20240918102339.png]]

|**Filtro**|**Resultado**|
|---|---|
|Carimbo de data/hora|`Yellow`O campo de registro de data e hora vem primeiro e é configurável para mostrar a hora e a data em um formato que possamos assimilar facilmente.|
|Protocolo|`Orange`Esta seção nos dirá o que é o cabeçalho da camada superior. Em nosso exemplo, ele mostra IP.|
|IP de origem e destino.Porta|`Orange`Isso nos mostrará a origem e o destino do pacote junto com o número da porta usada para conectar. Formato ==`IP.port == 172.16.146.2.21`|
|Bandeiras|`Green`Esta parte mostra todas as bandeiras utilizadas.|
|Números de sequência e reconhecimento|`Red`Esta seção mostra os números de sequência e reconhecimento usados ​​para rastrear o segmento TCP. Nosso exemplo está utilizando números baixos para assumir que números de sequência e ack relativos estão sendo exibidos.|
|Opções de protocolo|`Blue`Aqui, veremos todos os valores TCP negociados estabelecidos entre o cliente e o servidor, como tamanho da janela, confirmações seletivas, fatores de escala da janela e muito mais.|
|Notas / Próximo Cabeçalho|`White`Notas diversas que o dissector encontrado estará presente aqui. Como o tráfego que estamos observando é encapsulado, podemos ver mais informações de cabeçalho para diferentes protocolos. Em nosso exemplo, podemos ver que o dissector TCPDump reconhece o tráfego FTP dentro do encapsulamento para exibi-lo para nós.|
Para uma compreensão mais detalhada do IP e de outros cabeçalhos de protocolo, confira o `Networking Primer`na seção dois ou o `Networking fundamentals`caminho.

## Entrada/saída de arquivo com Tcpdump
Usar `-w`gravará nossa captura em um arquivo. Podemos usar rapidamente o espaço em disco aberto e ter problemas de armazenamento se não tomarmos cuidado. Utilizar os switches demonstrados acima pode ajudar a ajustar a quantidade de dados armazenados em nossos PCAPs.

#### Salvar nossa saída PCAP em um arquivo
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -i eth0 -w ~/output.pcap
```
![[Pasted image 20240918111754.png]]

Esta captura acima gerará a saída para um arquivo chamado `output.pcap`. Ao executar tcpdump dessa forma, a saída não rolará nosso terminal como de costume.

#### Lendo a saída de um arquivo
```shell-session
NycolasRamos@htb[/htb]$ sudo tcpdump -r ~/output.pcap
```
![[Pasted image 20240918111831.png]]

Isto lerá a captura armazenada em `output.pcap`. Observe que está de volta a uma visualização básica. Para obter informações mais detalhadas do arquivo de captura, reaplique nossos switches.




































































































































































