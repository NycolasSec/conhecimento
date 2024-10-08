### Switch
Um switch é um dispositivo de camada 2, ou seja, trabalha com endereço endereço físico MAC.

###### Half duplex
Indica que ele pode tanto mandar quanto receber mensagens ao mesmo tempo.

#### Tabela CAM

O switch tem uma tabela que armazena a os endereços e a interface usada para cada endereço. Ele pode fazer duas operações, sendo elas `flooding` e `broadcast`.

No `flooding`, o endereço de destino é em unicast, como não se sabe em que interface o host está ele manda para todos, mas somente o host do endereço processa .

No `broadcast`, o endereço de destino é em unicast, nesse caso todos os hosts irão processa a informação.

#### Aging Time
Seria um período de tempo em que o mac Address seria armazenado na tabela CAM.


































