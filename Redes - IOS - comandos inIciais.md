## Comandos de configuração inicial
Devemos fazer algumas configurações iniciais nos devices. :

**`Router> enable`**
Entra no modo privilegiado.

**`Router># configure terminal`**
Entra no modo de configuração global.

**`Router(config)# interface f0/0`**
Entra na configuração da interface.

**`Router(config-if)# no shutdown`**
Liga uma interface.

**`Router(config-if)# shutdown`**
Liga uma interface.

>[!important] O roteador vem com todas as interfaces desligadas, enquanto o switch vem com todas as interfaces ligadas.

**`Router(config)# line console 0`**
Entra na configuração da interface console 0.

**``Router(config-line)# logging syncronous``**
Previne o syslog e os eventos de mensagem da interrupção da entrada CLI.

**`Router(config)# line vty 0 4`**
Entra na configuração das portas de terminal virtual 0 a 4.

**`Router(config)# no ip domain-lookup`**
Previne a resolução DNS.

**`Router(config)# hostname Lab-1`** 
Define o nome do device.

---
---
#### Segurança do device
**`Router(config)# enable secret P@ssw0rd`**
Define uma senha criptografada para o modo `enable`.

**`Router(config)# enable password P@ssw0rd`**
Define uma senha para o modo `enable`.

**`Router(config-line)# password P@ssw0rd`**
Define uma senha para a interface console 0.

**``Router(config)# service password-encryption``**
Criptografa todas as senha definidas. Mas o hash criado por esse serviço é um hash fraco.

**`Switch(config)# username cisco privilege 15 password cisco`**
Cria um usuário `cisco` com a senha `cisco` e com privilégio `15`, sendo que os privilégios vão de `0` á `15`

---
---
#### Telnet password
```IOS
Switch(config)# line vty 0 4
Switch(config-line)# password P@ssw0rd
Switch(config-line)# login
```
**-**
```IOS
Switch(config)# username cisco privilege 15 password cisco
Switch(config)# line vty 0 4
Switch(config-line)# login local
```

Quando definimos o parâmetro `login`, dizemos que deverá ser exigido um usuário existente no device.

**`Router# telnet 192.168.1.1`** 
Acessa o device de IP 192.168.1.1 via telnet.

---
---
#### Monitorando o sistema
**`Router# show version`** 
Imprime informações da caixa, como versão, NVRAM, configuration register e etc.
   
**``Router(config)# config-register 0x2101``**
[Lista das opções do configuration register](https://www.cisco.com/c/en/us/support/docs/routers/10000-series-routers/50421-config-register-use.html)

**`Router# dir all`** 
Mostra toda árvore de arquivos e diretórios presentes na caixa.

**`Router# show users`** 
Mostra todas as sessões ativas no device.

**`Router# show startup-config`** 
Mostra as configurações na NVRAM.

**`Router# show running-config`** 
Mostra as configurações na DRAM.

---
---
#### Salvar e deletar configurações
Podemos tanto deletar quanto salvar nossas configurações atuais.

**``Router# copy running-config startup-config``**
Copia as configurações atuais para as informações de inicialização, salvando em algum arquivo especifico.

**``Router# write memory``**
**-**
**``Router# write``**
Copia as configurações atuais para as informações de inicialização.

**``Router# erase startup-config``**
**-**
**``Router# write erase``**
Deletam as configurações de startup.

**``Router# reload``**
Reinicia a caixa.






































































