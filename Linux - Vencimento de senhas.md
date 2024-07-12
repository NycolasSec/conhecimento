## Senhas shadow e política de senha

Senhas são armazenadas no arquivo **/etc/shadow**, cada usuário tem uma entrada no arquivo `/etc/shadow`. Um exemplo de entrada do arquivo `/etc/shadow` tem nove campos separados por dois-pontos:

```bash
cat /etc/shadow
```
![[Pasted image 20240710152325.png]]

Cada campo deste bloco de código é separado por dois pontos:

- **`user03`** : nome da conta de usuário.

- **`$6$CSsXsd3rwghsdfarf`** : a senha criptografada com hash do usuário.

- **`17933`** : os dias a partir da época em que a senha foi alterada pela última vez, em que a epoch é `1970-01-01` no fuso horário UTC.

- **`0`** : o número mínimo de dias desde a última alteração de senha antes que o usuário possa alterá-la novamente.

- **`99999`** : o número máximo de dias sem uma alteração de senha antes que a senha expire. Um campo vazio significa que a senha nunca expira.

- **`7`** : o número de dias até o usuário ser avisado de que sua senha expirará.

- **`2`** : o número de dias sem atividade, começando com o dia em que a senha expirou, antes de a conta ser automaticamente bloqueada.

- **`18113`** : o dia em que a conta expira em dias desde a epoch. Um campo vazio significa que a conta nunca expira.

- O último campo geralmente está vazio e é reservado para uso futuro.

## Formato de uma senha criptografada com hash

O campo de senha criptografada com hash armazena três informações: o algoritmo de hashing em uso, o _sal_ e o hash criptográfico. O sal adiciona dados aleatórios ao hash criptográfico para criar um hash exclusivo e fortalecer a senha com hash criptográfico. Cada informação é delimitada pelo caractere de dólar `$`.

```txt
$6$CSsXcYG1L/4ZfHr/$2W6evvJahUfzfHpc9X.45Jc6H30E
```

- **`6`** : o algoritmo de hash usado para a senha. Um `6` indica um hash SHA-512, o padrão RHEL 9, um `1` indica MD5 e um `5` indica SHA-256.

- **`CSsXcYG1L/4ZfHr/`** : o sal em uso para criptografar com hash a senha; ele é originalmente escolhido aleatoriamente.

- **`2W6evvJahUfzfHpc9X.45Jc6H30E`** : o hash criptográfico da senha do usuário, combinando o sal e a senha de texto simples e, depois, criptografando com hash para gerar o hash da senha.

## Configuração do vencimento de senha

O diagrama a seguir mostra os parâmetros de vencimento de senha relevantes que podem ser ajustados usando o comando `chage`



![[Pasted image 20240710153259.png]]

O comando define uma idade mínima (`-m`) de zero dias, uma idade máxima (`-M`) de 90 dias, um período de aviso (`-W`) de 7 dias e um período de inatividade (`-I`) de 14 dias.

```bash
chage -m 0 -M 90 -W 7 -I 14 sysadmin05

```


Se você deseja definir a expiração da conta para 30 dias a partir de hoje, então use os seguintes comandos:

![[Pasted image 20240710153656.png]]

|                                                                                                                                                |                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| [![1](https://rha.ole.redhat.com/rol/static/roc/Common_Content/images/1.svg)](https://rha.ole.redhat.com/rha/app/#userspassword-lecture-CO1-1) | Use o comando `date` para obter a data atual.                                                                    |
| [![2](https://rha.ole.redhat.com/rol/static/roc/Common_Content/images/2.svg)](https://rha.ole.redhat.com/rha/app/#userspassword-lecture-CO1-2) | Use o comando `date` para obter a data 30 dias a partir de agora.                                                |
| [![3](https://rha.ole.redhat.com/rol/static/roc/Common_Content/images/3.svg)](https://rha.ole.redhat.com/rha/app/#userspassword-lecture-CO1-3) | Use a opção `-E` do comando `chage` para alterar a data de expiração do usuário `cloudadmin10`.                  |
| [![4](https://rha.ole.redhat.com/rol/static/roc/Common_Content/images/4.svg)](https://rha.ole.redhat.com/rha/app/#userspassword-lecture-CO1-4) | Use a opção `chage` do comando `-l` para exibir a política de vencimento da senha para o usuário `cloudadmin10`. |

Você pode alterar a configuração de expiração de senha padrão no arquivo `/etc/login.defs`. As opções `PASS_MAX_DAYS` e `PASS_MIN_DAYS` definem a idade padrão máxima e mínima da senha, respectivamente.

O `PASS_WARN_AGE` define o período de aviso padrão da senha.


















































