[[Cyber - IPMI]]

Intelligence Platform Management Interface é um conjunto de especificações padronizadas para sistemas de gerenciamento de host baseados em hardware usados ​​para gerenciamento e monitoramento de sistemas. Ele atua como um subsistema autônomo e funciona independentemente do BIOS, CPU, firmware e sistema operacional subjacente do host. 

Fornece a capacidade de gerenciar e monitorar sistemas mesmos se eles estiverem desligados ou em um estado de sem resposta. ele opera usando uma conexão de rede direta ao hardware do sistema e não requer acesso ao sistema operacional por meio de um shell de login.

O IPMI também pode ser usado para atualizações remotas de sistemas sem exigir acesso físico ao host de destino. O IPMI é normalmente usado de três maneiras:

- Antes que o sistema operacional seja inicializado para modificar as configurações do BIOS
- Quando o host estiver totalmente desligado
- Acesso a um host após uma falha do sistema

Quando não estiver sendo usado para essas tarefas, o IPMI pode monitorar uma série de coisas diferentes, como temperatura do sistema, voltagem. Também pode ser usado para consultar informações de inventário, revisar logs de hardware e alertar usando SNMP. 

O sistema host pode ser desligado, mas o módulo IPMI requer uma fonte de alimentação e uma conexão LAN para funcionar corretamente.

Os sistemas que usam IPMI versão 2.0 podem ser administrados via serial over LAN, dando aos administradores de sistemas a capacidade de visualizar a saída do console serial em banda. Para funcionar, o IPMI requer os seguintes componentes:

- Baseboard Management Controller (BMC) - Um microcontrolador e componente essencial de um IPMI
- Intelligent Chassis Management Bus (ICMB) - Uma interface que permite a comunicação de um chassi para outro
- Intelligent Platform Management Bus (IPMB) - estende o BMC
- Memória IPMI - armazena coisas como o log de eventos do sistema, dados de repositório e muito mais
- Interfaces de comunicação - interfaces de sistema local, interfaces seriais e LAN, ICMB e barramento de gerenciamento PCI

---
## Referências

https://academy.hackthebox.com/module/112/section/1245