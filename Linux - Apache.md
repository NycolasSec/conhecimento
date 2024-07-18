## Instalação

### Debian
```sh
apt install apache2
systemctl start apache2
```

### RHEL
```sh
dnf install httpd
systemctl start httpd
```

---
## Firewall RHEL

Primeiro verificamos quais serviços estão habilitados,
```sh
firewall-cmd --list-all
```
![[Pasted image 20240715155756.png]]

Como o serviço `http` não está incluído, precisamos adicioná-lo. E depois recarregar o firewall
```sh
firewall-cmd --add-service=http --permanent

#Reload firewall
firewall-cmd --reload
```

---

## Adicionando inicialização automática
Os serviços que não são necessários ao sistema não são inicializados automaticamente. Então precisamos fazer isso automaticamente.

### Debian
```sh
systemctl enable apache2
```

### RHEL
```sh
systemctl enable httpd
```

Ao acessarmos o servidor web na porta 80, obteremos a imagem padrão do apache.

![[Pasted image 20240715162001.png]]

## Mudando o site
Para mudarmos o conteúdo do site, devemos adicionar nossos arquivos na pasta :  **`/var/www/html`**

---
## Configurações
As configurações do servidor ficam armazenadas no arquivo : 

**`/etc/httpd/conf/httpd.conf`** : RHEL
**`/etc`** : Debian

### RHEL - Algumas configurações padrão
```sh
cat /etc/httpd/conf/httpd.conf
```
```txt
ServerRoot "/etc/httpd"
Listen 80

User apache
Group apache

DocumentRoot "/var/www/html"

```

---
## Permissões

### firewalld
Permitindo um serviço :
```sh
firewall-cmd --add-service=http --permanent
```

Recarregando o firewall :
```sh
firewall-cmd --reload
```

Listando os serviços permitidos :
```sh
sudo firewall-cmd --list-all
```
![[Pasted image 20240715181039.png]]

---

## Referências

https://opensource.com/article/18/2/apache-web-server-configuration

https://exampleconfig.com/view/apache-rhel8-etc-httpd-conf-httpd-conf

















