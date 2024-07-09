A máscara de rede define qual a parte do endereço é destinada a rede e qual parte do endereço é destinado ao host.


### Classes de IP

O que define a classe de um IP é o valor do primeiro octeto da máscara.

| Classe | Variação                    | Val. 1° octeto |
| ------ | --------------------------- | -------------- |
| A      | 0.0.0.0 á 127.255.255.255   | 0              |
| B      | 128.0.0.0 á 191.255.255.255 | 10             |
| C      | 192.0.0.0 á 223.255.255.255 | 110            |
| D      | 224.0.0.0 á 239.255.255.255 | 1110           |
| E      | 240.0.0.0 á 255.255.255.255 | 11110          |

### Máscara de sub-rede

| Classe | Default Sub Mask | CIDR |
| ------ | ---------------- | ---- |
| A      | 255.0.0.0        | /8   |
| B      | 255.255.0.0      | /16  |
| C      | 255.255.255.0    | /24  |
