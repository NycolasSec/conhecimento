é comum que administradores não mudem senhas padrão de serviços para facilitar sua configuração, ou por não acreditarem que algum tentaria usa-los. 

## Credencial de enchimento
Existem vários bancos de dados que mantêm uma lista atualizada de credenciais padrão conhecidas. Um deles é o [DefaultCreds-Cheat-Sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) . Credenciais padrão também podem ser encontradas na documentação do produto, pois contêm as etapas necessárias para configurar o serviço com sucesso.

Atacar esses serviços com as credenciais padrão ou obtidas é chamado de [Credential Stuffing](https://owasp.org/www-community/attacks/Credential_stuffing) .

Podemos criar uma nova lista que separa essas credenciais compostas com dois pontos ( `username:password`). Além disso, podemos selecionar as senhas e alterá-las por nós `rules`para aumentar a probabilidade de acertos.

#### Credential Stuffing - Sintaxe Hydra
```shell-session
NycolasRamos@htb[/htb]$ hydra -C <user_pass.list> <protocol>://<IP>
```

#### Credential Stuffing - Hydra
```shell-session
NycolasRamos@htb[/htb]$ hydra -C user_pass.list ssh://10.129.42.197
```

Como o **OSINT** nos dá uma "**sensação**" de como a empresa e sua infraestrutura são estruturadas, entenderemos quais senhas e nomes de usuário podemos combinar. Também podemos usar o **Google** para ver se os aplicativos que encontramos têm credenciais codificadas que podem ser usadas.

#### Pesquisa Google - Credenciais Padrão
![[Pasted image 20240906084903.png]]


Além das credenciais padrão para aplicativos, algumas listas as oferecem para roteadores. Uma dessas listas pode ser encontrada [aqui](https://www.softwaretestinghelp.com/default-router-username-and-password-list/) .