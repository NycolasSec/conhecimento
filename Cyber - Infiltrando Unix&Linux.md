A W3Techs mantém um [estudo](https://w3techs.com/technologies/overview/operating_system) contínuo de estatísticas de uso do SO . Este estudo relata que mais `70%`de sites (servidores web) são executados em um sistema baseado em Unix.

## Considerações comuns
Ao considerar como estabeleceremos uma sessão de shell em um sistema Unix/Linux, nos beneficiaremos ao considerar o seguinte:

- Qual distribuição do Linux o sistema está executando?
    
- Quais shell e linguagens de programação existem no sistema?
    
- Qual função o sistema desempenha no ambiente de rede em que está inserido?
    
- Qual aplicativo o sistema está hospedando?
    
- Existem vulnerabilidades conhecidas?

Vamos nos aprofundar mais nesse conceito atacando um aplicativo vulnerável hospedado em um sistema Linux. Mantenha as perguntas em mente e tome notas enquanto avançamos. Você pode respondê-las?

## Obtendo um Shell por meio do ataque a um aplicativo vulnerável
Começamos com a enumeração do sistema alvo :

```shel-session
NycolasRamos@htb[/htb]$ nmap -sC -sV 10.129.201.101
```

Mantendo nosso objetivo `gaining a shell session`em mente, precisamos estabelecer alguns próximos passos após examinar nossa saída de digitalização.

`Quais informações podemos obter da saída`

Considerando que podemos ver que o sistema está escutando nas portas 80 ( `HTTP`), 443 ( `HTTPS`), 3306 ( `MySQL`) e 21 ( `FTP`), pode ser seguro assumir que este é um servidor web hospedando um aplicativo web.

Também podemos ver alguns números de versão revelados associados à pilha web ( `Apache 2.4.6`e `PHP 7.2.34`) e à distribuição do Linux em execução no sistema ( `CentOS`).

#### Ferramenta de gerenciamento rConfig
![[Pasted image 20240830102205.png]]

Descobrimos uma ferramenta de gerenciamento de configuração de rede chamada rConfig, usada para automatizar a configuração de dispositivos de rede, como a configuração remota de interfaces com informações de IP em vários roteadores simultaneamente. Embora facilite o trabalho dos administradores, se comprometida, a ferramenta pode permitir que um invasor malicioso tenha acesso a todos os dispositivos de rede e possua toda a rede. Identificar uma vulnerabilidade no rConfig seria uma descoberta crítica para pentesters.

## Descobrindo uma vulnerabilidade no rConfig
Podemos ver o número da versão do rConfig ( `3.9.6`). Devemos usar essas informações para começar a procurar por qualquer `CVEs`, `publicly available exploits`, e `proof of concepts`( `PoCs`).

Podemos usar as palavras-chave:`rConfig 3.9.6 vulnerability.`
![[Pasted image 20240830102353.png]]
O mesmo pensamento pode ser aplicado às versões Apache e PHP, mas como o aplicativo está sendo executado na pilha da web, vamos ver se podemos obter um shell por meio de um exploit escrito para as vulnerabilidades encontradas no rConfig.

Também podemos usar a funcionalidade de pesquisa do Metasploit para ver se algum módulo de exploração pode nos fornecer uma sessão de shell no alvo.

#### Pesquisar por um módulo de exploração
```shell-session
msf6 > search rconfig
```
<pre style="overflow-x:scroll;">
Matching Modules
================

   #  Name                                             Disclosure Date  Rank       Check  Description
   -  ----                                             ---------------  ----       -----  -----------
   0  exploit/multi/http/solr_velocity_rce             2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1  auxiliary/gather/nuuo_cms_file_download          2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   2  exploit/linux/http/rconfig_ajaxarchivefiles_rce  2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   3  exploit/unix/webapp/rconfig_install_cmd_exec     2019-10-28       excellent  Yes    rConfig install Command Execution
</pre>

O Rapid 7 mantém o código para módulos de exploração em seus [repositórios no github](https://github.com/rapid7/metasploit-framework/tree/master/modules/exploits) . Poderíamos fazer uma pesquisa ainda mais específica usando um mecanismo de pesquisa:`rConfig 3.9.6 exploit metasploit github`

Esta busca pode nos apontar para o código-fonte de um módulo de exploração chamado `rconfig_vendors_auth_file_upload_rce.rb`. Esta exploração pode nos dar uma sessão de shell em uma caixa Linux alvo executando rConfig 3.9.6.

Se esta exploração não aparecer na busca do MSF, podemos copiar o código deste repositório para nossa caixa de ataque local e salvá-lo no diretório que nossa instalação local do MSF está referenciando. Para fazer isso, podemos emitir este comando em nossa caixa de ataque:

#### Localizar
```shell-session
NycolasRamos@htb[/htb]$ locate exploits
```

Queremos procurar os diretórios na saída associada ao Metasploit Framework. No Pwnbox, os módulos de exploração do Metasploit são mantidos em:
`/usr/share/metasploit-framework/modules/exploits`

Podemos copiar o código em um arquivo e salvá-lo em `/usr/share/metasploit-framework/modules/exploits/linux/http` de forma semelhante a onde eles estão armazenando o código no repositório do GitHub.
Também devemos manter o msf atualizado usando os comandos `apt update; apt install metasploit-framework`ou seu gerenciador de pacotes local.

Se o copiarmos para um arquivo em nosso sistema local, certifique-se de que o arquivo tenha a extensão `.rb`. Todos os módulos no MSF são escritos em Ruby.

## Usando o Exploit rConfig e obtendo um Shell
No msfconsole, podemos carregar manualmente o exploit usando o comando:

#### Selecione um Exploit
```shell-session
msf6 > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
```
Com esse exploit selecionado, podemos listar as opções, inserir as configurações adequadas específicas para nosso ambiente de rede e iniciar o exploit.

#### Execute o Exploit
```shell-session
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > exploit
```

Podemos ver pelas etapas descritas no processo de exploração que esta exploração:

- Verifica a versão vulnerável do rConfig
- Autentica com o login da web rConfig
- Carrega uma carga útil baseada em PHP para uma conexão de shell reversa
- Exclui a carga útil
- Deixa-nos com uma sessão de shell do Meterpreter

#### Interaja com o Shell
```shell-session
meterpreter > shell
```

Podemos entrar em um shell do sistema ( `shell`) para obter acesso ao sistema de destino como se estivéssemos logados e abrir um console CMD.exe.

## Gerando um TTY Shell com Python
Quando acessamos o shell do sistema, podemos encontrar um "non-tty shell", onde o prompt não está presente e alguns comandos essenciais podem estar indisponíveis, como `su` e `sudo`. Isso ocorre frequentemente quando o payload é executado pelo usuário do Apache, que não costuma ter um shell completo configurado.

Para superar essas limitações, podemos tentar gerar manualmente um shell TTY usando Python, se estiver disponível no sistema. Primeiro, verifique a presença do Python com o comando `which python`, e então crie uma sessão de shell TTY usando um comando específico em Python.

#### Python interativo
```shell-session
python -c 'import pty; pty.spawn("/bin/sh")' 

sh-4.2$         
sh-4.2$ whoami
whoami
apache
```

Este comando usa python para importar o [módulo pty](https://docs.python.org/3/library/pty.html) e, em seguida, usa a função `pty.spawn` para executar o `bourne shell binary`( `/bin/sh`). Agora temos um prompt ( `sh-4.2$`) e acesso a mais comandos do sistema para navegar pelo sistema como quisermos.













































