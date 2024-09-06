Muitas pessoas criam senhas com foco na simplicidade, o que compromete a segurança. Para mitigar essa vulnerabilidade, os sistemas implementam políticas de senha que exigem combinações complexas de caracteres, incluindo letras maiúsculas, números e símbolos.

Embora essas políticas visem aumentar a segurança, os usuários ainda tendem a escolher senhas previsíveis relacionadas a seus interesses ou ao ambiente de trabalho, como o nome da empresa. O uso de **OSINT** (Open Source Intelligence) pode ajudar a coletar informações sobre o usuário, facilitando a adivinhação de suas senhas.

|**Descrição**|**Sintaxe da senha**|
|---|---|
|A primeira letra é maiúscula.|`Password`|
|Somando números.|`Password123`|
|Adicionando ano.|`Password2022`|
|Adicionando mês.|`Password02`|
|O último caractere é um ponto de exclamação.|`Password2022!`|
|Adicionando caracteres especiais.|`P@ssw0rd2022!`|

Com base nas estatísticas fornecidas pelo [WPengine](https://wpengine.com/resources/passwords-unmasked-infographic/) , a maioria dos comprimentos de senha é `not longer`de `ten`caracteres. Então, o que podemos fazer é escolher termos específicos que tenham pelo menos `five`caracteres e pareçam ser os mais familiares aos usuários.

#### Lista de senhas
```shell-session
NycolasRamos@htb[/htb]$ cat password.list
```
![[Pasted image 20240906081331.png]]

O **Hashcat** é uma ferramenta eficaz para ataques de força bruta e adivinhação de senhas, permitindo criar listas de palavras personalizadas com regras de mutação. Para explorar seu potencial, é recomendado estudar o módulo "Cracking Passwords with Hashcat" e consultar a documentação oficial, que contém a sintaxe completa para modificar palavras e caracteres.

|**Função**|**Descrição**|
|---|---|
|`:`|Não faça nada.|
|`l`|Coloque todas as letras em minúsculas.|
|`u`|Todas as letras devem ser maiúsculas.|
|`c`|Coloque a primeira letra em maiúscula e as demais em minúscula.|
|`sXY`|Substitua todas as instâncias de X por Y.|
|`$!`|Adicione o caractere de exclamação no final.|
Cada regra é escrita em uma nova linha que determina como a palavra deve ser mutada. Se escrevermos as funções mostradas acima em um arquivo e considerarmos os aspectos mencionados, esse arquivo pode ficar assim:

#### Arquivo de regras do Hashcat
```shell-session
NycolasRamos@htb[/htb]$ cat custom.rule
```
![[Pasted image 20240906081630.png]]

Hashcat aplicará as regras de `custom.rule`para cada palavra em `password.list`e armazenará a versão mutada em nosso `mut_password.list`de acordo. Assim, uma palavra resultará em quinze palavras mutadas neste caso.

#### Gerando lista de palavras baseada em regras
```shell-session
NycolasRamos@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

NycolasRamos@htb[/htb]$ cat mut_password.list
```
![[Pasted image 20240906081722.png]]

Ferramentas como Hashcat e John incluem listas de regras pré-construídas para a geração e quebra de senhas, sendo a "best64.rule" uma das mais populares. A quebra de senhas, muitas vezes, envolve tentativa e erro, mas pode ser mais eficiente ao considerar informações como a política de senhas, nome da empresa, localização geográfica e setor de atuação. Em casos específicos, senhas vazadas podem facilitar o processo.

#### Regras existentes do Hashcat
```shell-session
NycolasRamos@htb[/htb]$ ls /usr/share/hashcat/rules/
```
![[Pasted image 20240906081926.png]]

A ferramenta **CeWL** permite escanear palavras de um site e criar uma lista personalizada para adivinhação de senhas. Ela oferece parâmetros como a profundidade de varredura (`-d`), comprimento mínimo da palavra (`-m`), armazenamento de palavras em minúsculas (`--lowercase`), e define o arquivo de saída para os resultados (`-w`). Dessa forma, é possível gerar uma lista mais direcionada e com maior chance de sucesso ao tentar quebrar senhas.

#### Gerando listas de palavras usando CeWL
```shell-session
NycolasRamos@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist

NycolasRamos@htb[/htb]$ wc -l inlane.wordlist
```
![[Pasted image 20240906082130.png]]



































