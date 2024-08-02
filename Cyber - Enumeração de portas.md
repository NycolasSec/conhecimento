é uma parte muito importante, pois diz quais portas estão abertas, e quais serviços podem estar rodando nessas portas.

## nmap
O nmap é um dos melhores scanners de portas, pois além de fazer esse escaneamento, ele tem alguns scripts para detecção de vulnerabilidades e fraquezas nos serviços.

`-sV`
Traz algumas informações a mais dos serviços rodando.

`-sC`
Executa alguns scripts padrão.

`-A`
Entra em cada serviço e faz uma enumeração mais profunda.

`-Pn`
Não faz requisições com ping.

`-ST`
Traz somente as portas com serviços TCP.

`-sU`
Traz somente as portas com serviços UDP.

`-p-`
Escaneia todas as portas da conexão.

`-p [PORT],[PORT]`
Escaneia as portas que especificarmos.