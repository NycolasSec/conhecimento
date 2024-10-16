## Firewall > Rules > WAN
---
### Descrição
Usamos as regras de firewall para restringir o tráfego de entrada e saída das interfaces.

![[Pasted image 20241015125802.png]]
Devemos definir a interface a ser configurada e selecionar `Add`.

---
---
## Firewall > Rules > Edit
---
##### Edit Firewall Rule
###### Action
Aqui definimos a ação que o firewall fará caso o tráfego corresponda.
`Pass` - Permite o tráfego. `Block` - Bloqueia o tráfego, não emitindo nenhuma mensagem ao cliente. `Reject` - Bloqueia o tráfego, emitindo ao cliente que a conexão foi recusada.
###### Disabled
Caso esteja marcada, desabilita a regra sem retira-la da lista.
###### Interface
Define a qual interface será aplicada a regra.
###### Address Family
Define a qual tipo de endereço a regra será aplicada.
###### Protocol
Define a qual protocolo a regra será aplicada.

##### Source
###### Source
Indica qual a fonte do tráfego que será filtrado filtrado.

##### Destination
###### Destination
Indica qual o destino do tráfego que será filtrado. Para onde este tráfego foi destinado.

##### Extra options
###### Log
Caso habilitado, irá gerar logs para essa regra.
###### Description
Define alguma descrição, para facilitar o entendimento da regra, sem que seja necessário abrir a regra de fato.

---
---
## Exemplo
---
### Permissão do PING
![[Pasted image 20241015140422.png]]
Com essa regra permitimos a entrada de tráfego a interface WAN do firewall.




















































































