A variável de ambiente `PATH` define os diretórios onde o sistema procura por executáveis. Ela permite que usuários digitem comandos sem fornecer o caminho completo para o binário. Por exemplo, em vez de usar `/bin/cat /tmp/test.txt`, pode-se apenas usar `cat /tmp/test.txt`. Para verificar o conteúdo da variável `PATH`, use `env | grep PATH` ou `echo $PATH`.

```bash
echo $PATH
```
![[Pasted image 20240719172403.png]]

Criar um script ou programa em um diretório especificado no PATH o tornará executável em qualquer diretório do sistema.

```bash
pwd && conncheck
```
![[Pasted image 20240719172523.png]]

Como mostrado abaixo script `conncheck` é executado no diretório `/tmp` pois foi criado em um diretório especificado no PATH.
```bash
pwd && conncheck
```
![[Pasted image 20240719172906.png]]

Adicionar `.` ao `PATH` de um usuário inclui o diretório de trabalho atual na lista de caminhos onde o sistema procura executáveis. Isso permite substituir binários comuns por scripts maliciosos localizados no diretório atual. Por exemplo, ao adicionar `.` ao `PATH` com `PATH=.:$PATH` e exportar, um script chamado `ls` no diretório atual será executado no lugar do binário `/bin/ls`.

```bash
echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

```bash
PATH=.:${PATH}
export PATH
echo $PATH

.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

Aqui modificamos o caminho para executar um comando `echo` simples quando o comando `ls` é digitado.

```bash
touch ls
echo 'echo "PATH ABUSE!!"' > ls
chmod +x ls
```

```bash
ls

PATH ABUSE!!
```














































