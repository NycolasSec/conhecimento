

## Domínio de broadcast 

Cada rede ou sub rede detém um domínio de broadcast, onde os dispositivos conseguem se comunicar dentro da rede.
**Um domínio de Broadcast é um tipo de domínio em que o tráfego flui por toda a rede.**

## Domínio de colisão

Um segmento lógico da rede onde os pacotes transmitidos por elementos da rede podem colidir uns com os outros. 

O domínio de colisão refere-se a um conjunto de dispositivos nos quais pode ocorrer colisão de pacotes.

## HUB

Hub é um equipamento de camada 1, que tem apenas um domínio de broadcast e de colisão, pois como apenas repete o sinal para as outras portas, não mapeia onde estão os dispositivos.

Sendo:
- `HALF DUPLEX` : Somente um dispositivo mandando pacotes por vez, enquanto os outros recebem. Sendo sensível ao meio.
- `Colision Detected` : Ele tem um cronômetro interno, para saber se houve colisão de pacotes, caso haja dobra-se o tempo do cronômetro para evitar colisão

Como é sensível ao meio, consegue saber quando o meio está transmitindo o um dado ou não.

Utiliza o protocolo `CSMA/CD`

## Switch

O número de domínios de colisão do Switch é equivalente ao seu número de portas, tendo somente um domínio de broadcast, podendo ser aumentado com a tecnologia de ``VLANS``

Sendo:
- `FULL DUPLEX` : Os dispositivos podem mandar e receber pacotes no mesmo cabo.

---

### TDM

`Time Division Multiplier` é um método de colocar múltiplos fluxos de dados em um único sinal separando o sinal em muitos segmentos, cada um com uma duração muito curta.

### FDM

`Frequency Division Multiplier` é a técnica pelo qual há um múltiplo envio de dados em um meio comum, mas em diferentes frequências.





































