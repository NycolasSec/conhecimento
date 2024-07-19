
### Enumeração e Credenciais

**Credenciais:**
	- Importante anotar credenciais encontradas em diversos locais, como arquivos de configuração (.conf, .config, .xml), scripts de shell, histórico bash, arquivos de backup (.bak), bancos de dados e arquivos de texto.
	- Credenciais podem ser usadas para escalar privilégios, acessar outros usuários, bancos de dados e sistemas no ambiente.

**Diretório /var:**
	- Geralmente contém a raiz da web para servidores web no host.
	- Pode incluir credenciais importantes, como as de banco de dados MySQL em arquivos de configuração do WordPress.

```bash
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'
```
![[Pasted image 20240719152722.png]]

Os diretórios spool ou mail, se acessíveis, também podem conter informações valiosas ou mesmo credenciais.

É comum encontrar credenciais armazenadas em arquivos na raiz da web (por exemplo, strings de conexão MySQL, arquivos de configuração do WordPress).

```bash
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null
```
![[Pasted image 20240719152906.png]]

## chaves SSH

**Chaves Privadas SSH:**
	- Verifique se há chaves privadas SSH acessíveis no sistema.
	- Chaves privadas encontradas podem permitir acesso a usuários mais privilegiados ou a outros hosts no ambiente.

**Arquivo `known_hosts`:**
    - Revise o arquivo `known_hosts` para identificar chaves públicas de hosts aos quais o usuário se conectou anteriormente.
    - Pode ser útil para movimentação lateral e para encontrar dados que ajudem na escalada de privilégios.

```bash
ls ~/.ssh
```
![[Pasted image 20240719152949.png]]


























