[Log4J Unifi](https://github.com/puzzlepeaches/Log4jUnifi)

**Queries no mongodb pelo terminal**
`mongo --port 27117 ace --eval "db.admin.find().forEach(printjson);"`


Criptografia - https://cryptohack.org/courses/

```bash
hashcat --force list.list -r custom.rule --stdout | sort -u > mut_password.list 

```
O sort vai tirar os repetidos

```bash
sed -n '/^[[:alnum:][:punct:]]\{11,\}$/p' mut_password.list > mut_pass.list
```
Vai retiraras linhas que tiverem menos de 11 caracteres.

[[Cyber - Filtrar pacotes tcp]]

[[Cyber - Exibição de logs do Linux no Linux]]
[[Cyber - Exibição de dumps de time]]
