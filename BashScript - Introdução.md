```bash
echo "oi"
```
Escreve na tela

```bash
echo "Sistema rodando por $(uptime -p | cut -d" " -f2,3)"
```
`uptime -p` mostra o tempo que a máquina está ligada

#### ./script.sh
```bash
#! /bin/bash
echo "Deseja iniciar qual serviço ?"

read -r serv

service "$serv" restart

echo "Serviços ativos:"

ps aux | grep "$serv"

echo "Portas abertas:"

netstat -nlpt 2> /dev/null
```