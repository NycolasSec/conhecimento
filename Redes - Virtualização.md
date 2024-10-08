Usamos a virtualização para podermos ter mais liberdade na manipulação do hardware. Em um sistema operacional não podemos manipular o hardware, a menos que executemos essa tarefa manualmente.

Com os hypervisors podemos manipular recursos de forma livre ( dentro dos limites do hardware físico ) e virtualizar SOs com base nos recursos virtualizados.

## Tipos de hypervisors
Existem dois tipos de hypervisors :

### Hypervisor tipo 1
É instalado direto na máquina e tem acesso direto aos recursos, sendo assim mais performático e versátil, no que diz direito ao manuseio dos recursos.

![[Pasted image 20241003101106.png]]

#### Exemplos 
- ESXI
- KVM

### Hypervisor tipo 2
É instalado em cima de um SO e as solicitações ao hardware são traduzidas pelo SO, sendo menos performático e versátil no que diz direito ao manuseio do hardware, pois além de suas solicitações serem controladas e traduzidas pelo SO o SO também usará recursos.

![[Pasted image 20241003101320.png]]

#### Exemplos 
- Virtual box
- VM Ware
- Hyper-V

## Outros

#### Overlaying
É a camada de virtualização que irá traduzir as requisições ao recursos do hardware.

#### Underlaying
Se refere aos recursos do hardware como memória, CPU, disco rígido e outros.

