Fuzzing se refere ao processo de descobrir pastas, arquivos e parâmetros que não temos conhecimento.

Existem vários programas como o wfuzz e ffuf. Aqui utilizaremos o wfuzz.

### wfuzz
No wfuzz devemos indicar onde vamos fazer a enumeração colocando `FUZZ` onde vamos fazer a enumeração.

#### -c
Indica que queremos que a saída seja colorida.

#### -u
Indica qual a url que queremos fazer FUZZING.

#### -z
Indica se queremos fazer a enumeração por range ou por arquivos.

 Para fazer a enumeração por range usamos `-z range,<RANGE DA PESQUISA>`
```sh
wfuzz -c -z range,1-1000 -u http://corp.ht/noticias.php?id=FUZZ
```

Para fazer a enumeração por arquivos usamos `-z fle,<CAMINHO DA WORDLIST>`
```sh
wfuzz -c -z file,wordlist.txt -u http://corp.ht/FUZZ
```

Para arquivos também podemos usar `-w <CAMINHO DA WORDLIST>`.
```sh
wfuzz -c -w wordlist.txt -u http://corp.ht/FUZZ
```

#### -t
Aqui podemos indicar quantas conexões simultâneas iremos fazer com o servidor.

Aqui estamos fazendo 10 conexões simultâneas com o servidor.
```sh
wfuzz -c -t 10 -w wordlist.txt --hc 404 -u http://corp.ht/FUZZ
```

#### --hc
Assim podemos ocultar as respostas de acordo com seu status code separados por vírgula.

Aqui estamos ocultando os saídas com o status code de 404 e 403
```sh
wfuzz -c -w wordlist.txt --hc 404,403 -u http://corp.ht/FUZZ
```

Temos alguns outros parâmetros que escondem os resultados como : 
- ``-hc`` : Hide Code
- ``-hl`` : Hide Lines
- ``-hw`` : Hide Words
- ``-hh`` : Hide Chars

#### --sc
Assim podemos exibir somente as respostas de acordo com seu status code separados por vírgula.

Aqui estamos exibindo os saídas com o status code de 200 e 500
```sh
wfuzz -c -w wordlist.txt --hc 200,500 -u http://corp.ht/FUZZ
```

#### -b
Aqui podemos indicar qual o cookie da nossa requisição, para colocar mais e um basta repetir o parâmetro `-b`. `-b cookie`

```sh
wfuzz -c -w wordlist.txt -b "id=19283746" -u http://corp.ht/FUZZ
```

#### -H
Aqui podemos especificar algum header na nossa requisição. Para indicarmos mais de um header basta repetirmos o parâmetro `-H`. 
`-H "Cookie:id=1312321&user=FUZZ"`

```sh
wfuzz -c -w users.txt -H "Cookie:id=1312321&user=FUZZ" -u http://corp.ht/FUZZ
```