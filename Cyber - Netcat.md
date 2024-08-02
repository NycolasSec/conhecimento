Vem instalado na maioria dos sistemas linux, mas também existem versões para windows (**``nc.exe``**).

```bash
netstat -nlpt
```
Exibe as portas abertas.

```bash
nc -lvnp 4242
```
Com esse comando, colocamos o `netcat` para escutar a porta `4242`.

```bash
nc -lvnp 80 < banner.txt
```
Aqui abrimos a porta 80, e quando alguém se conectar nessa porta irá receber a mensagem escrita no banner.txt

#F119 













