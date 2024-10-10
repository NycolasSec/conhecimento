## Device Startup
Como os dispositivos usam o mesmo OS, normalmente eles tem os mesmos processos de inicialização.
#### Sequence
Cisco routers and switches generally perform the same steps upon initial startup.

- Discover device hardware.
- Find and load IOS image.
- Find and load configuration file.
#### Tipos de memória
Flash - NVRAM e DRAM

---
---
## Acessando o Device
Temos alguns métodos para acessar e configurar um dispositivo.
#### Métodos
- CLI
- GUI
#### Console port
Como o device vem sem uma configuração de IP para ser acessado, temos que usar a porta console, para usarmos é preciso de  :
- Cabo console
- Emulador de terminal

---
---
## Estrutura de comandos IOS
Para controle de fluxo, organização e segurança o IOS nos concede alguns modos de configuração, para cada aspecto.

#### Modos de permissão
###### `Router>` - User or exec mode
Aqui nossos privilégios são baixos e só podemos ver algumas informações do device, não podendo configura-lo.
###### `Router#` - Privileged EXEC (or Enable) mode
Aqui temos acesso total ao device. Dependendo de como o usuário estiver configurado, mesmo se estiver em modo privilegiado, será limitado as configurações daquele usuário.

#### Modos de configuração
Dentro do modo privilegiado, ainda temos alguns modos de configuração.
###### `Router(config)` - Global Configuration Mode
###### `Router(config-if)#` - Interface Configuration Mode
###### `Router(config-router)#` - Router configuration Mode

Para sair de algum modo podemos usar `exit`, `end` ou `ctrl-z`

































