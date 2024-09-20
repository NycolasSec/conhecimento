### Install ``httpd``
```shell
dnf -y install httpd


# rename or remove welcome page
mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.org
```

### Configure httpd. Substitua o nome do servidor pelo seu próprio ambiente.

```shell
vim /etc/httpd/conf/httpd.conf
```

##### httpd.conf
```txt
# line 91 : change to admin's email address
ServerAdmin root@srv.world

# line 100 : change to your server's name
ServerName www.srv.world:80

# line 149 : change (remove [Indexes])
Options FollowSymLinks

# line 156 : change
AllowOverride All

# line 169 : add file name that it can access only with directory's name
DirectoryIndex index.html index.php index.cgi

# add follows to the end
# server's response header
ServerTokens Prod
```

```shell
systemctl enable --now httpd
```

### Firewall
Se estiver rodando o firewall, permita o serviço http. HTTP usa a porta 80

```shell
firewall-cmd --add-service=http --permanent
```

## Conteúdo do site
```shell
vim /var/www/html/index.html
```