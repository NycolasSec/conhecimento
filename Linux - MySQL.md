[[Redes - MySQL]] | [[Cyber - MySQL]]

---

## Configuração padrão

Gerenciamento e configuração do banco de dados é um assunto tão vasto que profissões inteiras lidam com quase nada além de bancos de dados.

```bash
sudo apt install mysql-server -y
cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'
```
```output
[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
skip-host-cache
skip-name-resolve
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/
```

### Configurações perigosas
Podemos olhar mais detalhadamente para a [referência do MySQL](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html) para determinar quais opções podem ser feitas na configuração do servidor. As principais opções que são relevantes para a segurança são:

|**Configurações**|**Descrição**|
|---|---|
|`user`|Define como usuário o serviço MySQL será executado.|
|`password`|Define a senha para o usuário MySQL.|
|`admin_address`|O endereço IP no qual escutar conexões TCP/IP na interface de rede administrativa.|
|`debug`|Esta variável indica as configurações de depuração atuais|
|`sql_warnings`|Esta variável controla se as instruções INSERT de linha única produzem uma sequência de informações caso ocorram avisos.|
|`secure_file_priv`|Esta variável é usada para limitar o efeito das operações de importação e exportação de dados.|

As configurações como user, password e admin_address são críticas para a segurança, pois geralmente são armazenadas em texto simples. Se houver acesso não autorizado ao servidor MySQL devido a permissões inadequadas no arquivo de configuração ou outras vulnerabilidades, um invasor pode ler essas informações sensíveis, incluindo nomes de usuário, senhas e detalhes pessoais dos clientes. Isso permite acesso e até mesmo manipulação de todo o banco de dados.

Configurações como debug e sql_warnings fornecem saídas detalhadas de erros, úteis para administradores, mas que podem revelar informações sensíveis se não forem protegidas corretamente. Essas mensagens de erro podem ser exploradas por ataques de injeção de SQL para executar comandos do sistema no servidor MySQL, aumentando o risco de comprometimento de segurança.

---

## Referências

**`Referência dp MySQL`** : https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html
**`HTB - Footiprinting`** : https://academy.hackthebox.com/module/112/section/1238