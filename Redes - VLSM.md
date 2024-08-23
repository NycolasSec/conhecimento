VLSM se trata de criarmos sub-redes de tamanho variável. Assim não precisamos fazer sub redes sempre do mesmo tamanho, podemos fazer uma de 250, outra de 100 e outra de 40 hosts.

## Exemplo 1
Temos um prédio com 4 departamentos, um com 4 hosts, outro como 30, outro com 220 e outro com 510. Assim precisamos de 4 sub-redes. Recebemos o endereço de rede **192.168.0.0/16**.

##### Organização:
Primeiro organizamos as sub-redes da maior para a menor.

| 510 | 220 | 30  | 4   |
| --- | --- | --- | --- |

##### Quantidade de bits
Usando o resultado das potências de **base 2**, definimos o resultado que mais se aproxima da quantidade de hosts, podendo ser maior, mas não menor.

| N. Hosts |  Bits de host   |  Bits de rede   |  CIDR  |
| :------: | :-------------: | :-------------: | :----: |
|   510    | 2 ^ **9** = 512 | 32 - 9 = **23** | **23** |
|   220    | 2 ^ **8** = 256 | 32 - 8 = **24** | **24** |
|    30    | 2 ^ **5** = 32  | 32 - 5 = **27** | **27** |
|    4     |  2 ^ **3** = 8  | 32 - 3 = **29** | **29** |

### Calculando as sub redes
**192.168.0.0/16**

| 128 | 64  | 32  | 16  |  8  |  4  |  2  |  1  |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
|  7  |  6  |  5  |  4  |  3  |  2  |  1  |  0  |
Antes de fazermos a próxima VLSM, somamos **1** nos bits de rede, para que não façamos a VLSM dentro da rede anterior.

#### 510 Hosts - /23
| ==Rede==      | 11000000 | 10101000 | 0000000**0** | **00000000** |
| :------------ | :------: | :------: | :----------: | :----------: |
| -- -- --      |   192    |   168    |      0       |      0       |
| ==Broadcast== | 11000000 | 10101000 | 0000000**1** | **11111111** |
| -- -- --      |   192    |   168    |      1       |     255      |

#### 220 Hosts - /24
| ==Rede==     | 11000000 | 10101000   | 00000010 | **00000000** |
| ------------ | -------- | ---------- | -------- | ------------ |
| -- -- --     | 192      | 168        | 2        | 0            |
| ==Bradcast== | 11000000 | 1010100000 | 00000010 | **11111111** |
| -- -- --     | 192      | 168        | 2        | 255          

#### 30 Hosts - /27
| ==Rede==      | 11000000 | 10101000 | 00000011 | 000**00000** |
| ------------- | -------- | -------- | -------- | ------------ |
| -- -- --      | 192      | 168      | 3        | 0            |
| ==Broadcast== | 11000000 | 10101000 | 00000011 | 000**11111** |
| -- -- --      | 192      | 168      | 3        | 31           |

#### 4 Hosts - /29
| ==Rede==      | 11000000 | 10101000 | 00000011 | 00100**000** |
| ------------- | -------- | -------- | -------- | ------------ |
| -- -- --      | 192      | 168      | 3        | 32           |
| ==Broadcast== | 11000000 | 10101000 | 00000011 | 00100**111** |
| -- -- --      | 192      | 168      | 3        | 39           |


































