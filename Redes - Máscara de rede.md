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

### Máscara CIDR
Serve para sabermos quais bits do endereço pertencem a rede. Cada octeto representa 8 bits

| 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2^7 | 2^6 | 2^5 | 2^4 | 2^3 | 2^2 | 2^1 | 2^0 |

Devemos somar da esquerda para a direita. A soma de todos os bits é igual a 255.

#### Exemplo:

##### /16
São 16 bits para identificar a rede. A máscara tem um total de 24 bits, como estamos usando 16, o resto completamos com zero. Lembrando que o endereço é dividido em octetos.

``11111111`` ``11111111`` ``00000000`` ``00000000``
`255`          `255`           `0`              `0`

##### /17
é semelhante ao `/16` mas acrescentamos um bit a rede.

`11111111` `11111111` `10000000` `0000000`
``255``          `255`           `128`          `00000000`

### Máscara de sub-rede
Antigamente os protocolos não tinham capacidade de divulgar a máscara padrão de um equipamento, então foram criadas máscaras padrão pars cada classe de IP.

| Classe | Default Sub Mask | CIDR |
| ------ | ---------------- | ---- |
| A      | 255.0.0.0        | /8   |
| B      | 255.255.0.0      | /16  |
| C      | 255.255.255.0    | /24  |
