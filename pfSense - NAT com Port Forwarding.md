## Firewall > NAT > Port Forward
---
### Descrição
Usamos o `Port Forwarding` para redirecionar as portas de acesso de algum endereço, pode ser usado para não denunciar a porta interna usada, ou por questões de padrão da empresa.

![[Pasted image 20241008134645.png]]
Devemos clicar em `Add` para adicionar um novo redirecionamento, ou dar um duplo click na regra que queremos editar.

---
---
## Firewall > NAT > Port Forward > Edit
---
##### Edit Redirect Entry
###### Interface
Define por qual interface entrará o tráfego.
###### Address Family
Define qual a versão do Internet Protocol que será usado.
###### Protocol
Define qual protocolo será incluso na regra, todos os outros serão filtrados.
###### Destination
É o endereço para o qual o tráfego a ser redirecionado é inicialmente destinado. Costuma ser o endereço da Interface ``WAN``.
###### Destination port range
É a porta de destino original do tráfego, como é vinda da internet, antes de ser redirecionada.
###### Redirect target IP
O endereço IP para o qual o tráfego será encaminhado ou redirecionado tecnicamente.
###### Redirect target port
Onde o intervalo de portas encaminhadas começará. Se um intervalo de portas for encaminhado, por exemplo, ``19000-19100``, somente o ponto de partida local será especificado, pois o número de portas deve corresponder um a um.
###### Description
Uma descrição da regra para facilitar a compreensão do objetivo da regra.
###### No XMLRPC Sync
Essa opção só será relevante se uma configuração de cluster de alta disponibilidade estiver em uso, e caso contrário, deve ser ignorado. Ao usar um cluster de alta disponibilidade com configuração sincronização, marcar esta caixa impedirá que a regra seja sincronizado com os outros membros de um cluster (consulte [Alta disponibilidade](https://docs.netgate.com/pfsense/en/latest/highavailability/index.html)). Normalmente, todas as regras devem ser sincronizadas, contudo. Esta opção só é eficaz em nós mestres, _não impede_ uma regra de ser substituída em nós escravos.
###### NAT reflection
Esta opção permite que a reflexão seja ativada ou desabilitado por regra para substituir o padrão global.
###### Filter rule association
Uma entrada de encaminhamento de porta define apenas qual tráfego será redirecionado, uma regra de firewall é necessária para passar qualquer tráfego por meio desse redirecionamento.

---
---
## Interfaces > WAN
---
Caso esteja em uma rede interna, e estiver usando endereços privados ,será necessário desativar o bloqueio de tráfego de redes privadas.

##### Reserved Networks
###### Block private networks and loopback addresses
![[Pasted image 20241008141452.png]]

---
---
### Exemplo
![[Pasted image 20241008143136.png]]
Com essas opções aplicadas, podemos acessar o ``pfSense`` pelo `IP` da interface wan e pela porta `8080`.

![[Pasted image 20241008143305.png]]
















































































