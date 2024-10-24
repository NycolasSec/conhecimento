
#### Definição
---
O VTP (VLAN Trunk Protocol) é um protocolo proprietário da CISCO que permite a propagação das VLANs entre dispositivos. Em um cenário com esse protocolo, os dispositivos podem operar de 3 formas:

**`Server`**: É o dispositivo que irá distribuir a sua tabela de VLANs .
**`Client`**: É o dispositivo que irá replicar a tabela de VLANs do `Server`.
**`Transparent`**: Ele recebe a tabela do `Server`, mas não á replica, somente transmite essa tabela para os outros `Clients`.

Tanto o `Server` o `Client` e o `Transparent`, tem de estar no mesmo domínio. Mesmo que o `Transparent` não vá atualizar a sua tabela, se ele não estiver no mesmo domínio que o `Server`, ele não irá transmitir a mensagem aos outros dispositivos.

---
---
#### Revision Number
---
Em um ambiente com clients e servers `VTP`, para manter suas tabelas atualizadas, esses dispositivos detém um `Revision Number`, que é adicionado `+1` sempre que sua tabela de `VLANs` é modificada.

Independente se o equipamento está como client ou server, se o seu `Revision Number` for o maior daquela rede, é a sua tabela que será replicada nos outros dispositivos.

A diferença de um Server pra um Client, é que não é possível modificar a tabela de VLANs de um Client.

---
---
#### Info
---
Podemos definir um `VTP password` para impedir que qualquer dispositivo se associe ao domínio.

































