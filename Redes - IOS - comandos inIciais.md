## Comandos de configuração inicial
Devemos fazer algumas configurações iniciais nos devices. :

**`Router> enable`**
Entra no modo privilegiado.

**`Router(config)# enable secret P@ssw0rd`**
Define uma senha criptografada para o device.

**``Router(config-line)# logging syncronous``**
Previne o syslog e os eventos de mensagem da interrupção da entrada CLI.

**`Router(config)# no ip domain-lookup`**
Previne a resolução DNS.

**`Router(config-line)# hostname Lab-1`** 
Define o nome do device.

