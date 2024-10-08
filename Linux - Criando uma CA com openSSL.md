## Criando a CA

#### Instalação
Caso o `openssl` não esteja instalado pode-se instalar com o seguinte comando :
```shell
dnf install openssl
```

---
---
#### Estrutura de pastas
Como ``CA`` não é um serviço e sim um membro da ``PKI`` (Public Key Infrastructure), devemos configurar uma estrutura para armazenar os certificados. Essa estrutura de pasta pode ser criada de acordo com o seu gosto, mas saiba que existe um padrão definido :

`/root/ca/` : Pasta raiz da CA.
`/root/ca/newcerts/` : Onde será armazenado os arquivos de requisição de certificados(csr).
`/root/ca/certs/` : Onde será armazenado os certificados criados.
`/root/ca/private/` : Armazenará o par de chaves raiz, e o certificado da ca.
`/root/ca/crl/` : Armazenará os certificados revogados.

Eles podem ser criados com o seguinte comando :
```shell
mkdir /root/ca
cd /root/ca
mkdir certs crl newcerts private
chmod 700 private
```

---
---
#### Arquivos iniciais
Alguns arquivos também fazem parte da estrutura inicial como :
`/root/ca/index.txt` : 
`/root/ca/serial` : Contará o número de vezes que as 
`/root/ca/openssl.cnf` : Será a configuração que usaremos para criar os certificados.

Eles podem ser criados com o seguinte comando :
```shell
touch index.txt
echo 1000 > serial
cp /etc/ssl/openssl.cnf /root/ca/openssl.cnf
```

Devemos indicar qual a raiz da nossa CA no arquivo de configuração :
###### /root/ca/openssl.cnf
```cnf
<SNIP>
[ CA_default ]
dir		= /root/ca
<SNIP>
```

---
---
#### Par de chaves privada
Devemos gerar o par de chaves raiz que será usado para assinar os cerificados :

```shell
openssl genrsa -aes256 -out private/cakey.pem 4096
```
Irá criar uma chave privada criptografada e também irá solicitar uma senha (opcional).

>[!warning] A `private key` deve ser guardada com extrema segurança, pois qualquer um que tiver essa chave, poderá assinar certificados em nome da `CA`. 

---
---
#### Certificado raiz
Devemos também criar o certificado raiz que será importado nos serviços, e que será usado para assinar outros certificados.

```shell
openssl req -config ./openssl.cnf -new -x509 -days 3650 -key private/cakey.pem -sha256 -out cacert.pem
```
Caso tenha colocado uma senha na chave privada, ele irá solicitar ela novamente.

>[!important] Sempre que usarmos o `-req` devemos indicar o caminho do nosso arquivo de configuração com o `-config`

---
---
#### Requisição de certificados de dispositivos finais
Todo dispositivo final que quiser um certificado assinado pela CA, deve emitir um csr com a sua chave pública.

###### Geração da chave
```shell
openssl genrsa -out apache_key.pem 4096
```

###### Geração do csr
```shell
openssl req -new -key apache_key.pem -out apache.csr
```

Esse arquivo csr deverá se enviado a CA, para a validação.

---
---
#### Assinando um certificado
O arquivo csr para a validação deverá ser armazenado em `/root/ca/newcerts/` na nossa CA.

```shell
openssl ca -config ./openssl.cnf -in newcerts/apache.csr -out certs/apache_cert.pem
```





















