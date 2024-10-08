Configure hospedagens virtuais para usar nomes de domínio múltiplos. Vamos tomar como exemplo um segundo domínio para nosso site que se chamará `www.olimpiada.com`.

#### Raiz do documento
Devemos criar a pasta raiz que irá armazenar os arquivos do site:
`/var/www/www.olimpiada.com/`

A estrutura dentro dessa pasta fica de sua preferência, aqui o conteúdo será armazenado em uma pasta `html` :
`/var/www/www.olimpiada.com/html/index.html`

---
#### Inclusão das configurações
No arquivo de configuração `/etc/httpd/conf/httpd.conf`, iremos incluir os arquivos de configurações dos Virtual Hosts que iremos criar, eles ficarão em  `/etc/httpd/sites-enabled/`.

###### /etc/httpd/conf/httpd.conf
```conf
IncludeOptional sites-enabled/*.conf
```
 Adicione esta linha no final.
 
---
#### Configuração dos Virtual Hosts
Embora termos criado a pasta para armazenar os sites ativos, para maior organização, vamos criar uma pasta para as configurações dos sites disponíveis, e criaremos um link para a pasta dos sites ativos quando quisermos ativar o site.

Ficará assim :
**`/etc/httpd/sites-available/`** : Sites disponíveis
**`/etc/httpd/sites-enabled/`** : Sites em atividade

Agora vamos fazer as configurações do site :
###### /etc/httpd/sites-available/www.olimpiada.com.conf
```conf
<VirtualHost *:80>
ServerName      www.olimpiada.com
ServerAlias     www.olimpiada.com
DocumentRoot    /var/www/www.olimpiada.com/html
ErrorLog        /var/www/www.olimpiada.com/log/error.log
CustomLog       /var/www/www.olimpiada.com/log/requests.log combined
</VirtualHost>
```

Essas configurações são de sua preferência.

#### Reiniciando o serviço
Agora devemos reiniciar o apache.
```shell
systemctl restart httpd
```

>[!IMPORTANT] Lembre-se de que precisa ter um serviço DNS apontando para seu site. 

![[Pasted image 20241007154126.png]]


























