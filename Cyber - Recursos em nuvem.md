O uso de nuvem, como [AWS](https://aws.amazon.com/) , [GCP](https://cloud.google.com/) , [Azure](https://azure.microsoft.com/en-us/) e outros, é agora um dos componentes essenciais para muitas empresas hoje em dia.

As configurações feitas pelos administradores podem, no entanto, tornar vulneráveis ​​os recursos da nuvem da empresa. Isso geralmente começa com `S3 buckets`(AWS), `blobs`(Azure), `cloud storage`(GCP), que pode ser acessado sem autenticação se configurado incorretamente.

## Servidores hospedados pela empresa

```bash
for i in $(cat subdomainlist); do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4; done
```

Frequentemente, o armazenamento em nuvem é adicionado á lista DNS.

Fiquemos com o caso de uma empresa nos ter contratado, e durante a pesquisa de IP, já vimos que um endereço IP pertence ao servidor `s3-website-us-west-2.amazonaws.com`.

Existem muitas maneiras diferentes de encontrar esse armazenamento em nuvem. Uma das mais fáceis e utilizadas é a pesquisa do Google combinada com o Google Dorks.

podemos usar o Google Dorks `inurl:`para `intext:`restringir nossa pesquisa a termos específicos.

### Pesquisa Google para AWS

![[gsearch1.webp]]

### Pesquisa Google para Azure

![[gsearch2.webp]]

Ao procurarmos uma empresa que já conhecemos ou queremos conhecer, também nos depararemos com outros arquivos como documentos de texto, apresentações, códigos e muitos outros.

Esse conteúdo também costuma ser incluído no código-fonte das páginas da web, de onde as imagens, códigos JavaScript ou CSS são carregados. Este procedimento geralmente descarrega o servidor web e não armazena conteúdo desnecessário.

### Site de destino - Código fonte

![[Pasted image 20240627092100.png]]

Provedores terceirizados, como [domain.glass](https://domain.glass/) , também podem nos dizer muito sobre a infraestrutura da empresa.

### Resultados de domínio.Glass

![[cloud1.webp]]

Outro provedor muito útil é [o GrayHatWarfare](https://buckets.grayhatwarfare.com/) . Podemos fazer muitas pesquisas diferentes, descobrir armazenamento em nuvem AWS, Azure e GCP e até mesmo classificar e filtrar por formato de arquivo.

### Resultados do GrayHatWarfare

![[cloud2.webp]]

Muitas empresas usam abreviações do nome da empresa na infraestrutura de TI.
Esses termos também fazem parte de uma excelente abordagem para descobrir novos armazenamentos em nuvem da empresa

### Chaves SSH privadas e públicas

![[ghw1.webp]]





















































