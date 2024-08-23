O rastreamento da web é vasto e complexo, mas há muitas ferramentas disponíveis para ajudar. Essas ferramentas automatizam o processo, tornando-o mais rápido e eficiente, e permitem que você se concentre na análise dos dados extraídos.

## Rastreadores da Web populares

1. `Burp Suite Spider`: Burp Suite, uma plataforma de teste de aplicativos da web amplamente usada, inclui um poderoso rastreador ativo chamado Spider. O Spider se destaca em mapear aplicativos da web, identificar conteúdo oculto e descobrir vulnerabilidades potenciais.
2. `OWASP ZAP (Zed Attack Proxy)`: ZAP é um scanner de segurança de aplicativo web gratuito e de código aberto. Ele pode ser usado em modos automatizado e manual e inclui um componente spider para rastrear aplicativos web e identificar vulnerabilidades potenciais.
3. `Scrapy (Python Framework)`: Scrapy é um framework Python versátil e escalável para construir web crawlers personalizados. Ele fornece recursos avançados para extrair dados estruturados de sites, lidar com cenários complexos de crawling e automatizar o processamento de dados. Sua flexibilidade o torna ideal para tarefas de reconhecimento personalizadas.
4. `Apache Nutch (Scalable Crawler)`: Nutch é um web crawler de código aberto altamente extensível e escalável escrito em Java. Ele foi projetado para lidar com rastreamentos massivos em toda a web ou focar em domínios específicos. Embora exija mais conhecimento técnico para instalar e configurar, seu poder e flexibilidade o tornam um recurso valioso para projetos de reconhecimento em larga escala.

Aderir a práticas de rastreamento éticas e responsáveis ​​é crucial, não importa qual ferramenta você escolher. Sempre obtenha permissão antes de rastrear um site, especialmente se você planeja executar varreduras extensas ou intrusivas. Esteja atento aos recursos do servidor do site e evite sobrecarregá-los com solicitações excessivas.


## Scrapy

Aproveitaremos o Scrapy e um spider personalizado feito sob medida para reconhecimento em `inlanefreight.com`. Se você estiver interessado em mais informações sobre técnicas de crawling/spidering, consulte o módulo " [Usando Web Proxies](https://academy.hackthebox.com/module/details/110) ", pois ele também faz parte do CBBH.

### Instalando Scrapy
Antes de começarmos, certifique-se de ter o Scrapy instalado no seu sistema. Se não tiver, você pode instalá-lo facilmente usando pip, o instalador de pacotes Python:

```shell-session
NycolasES6@htb[/htb]$ pip3 install scrapy
```

Este comando baixará e instalará o Scrapy junto com suas dependências, preparando seu ambiente para construir nosso spider.

### ReconSpider
Primeiro, execute este comando no seu terminal para baixar o scrapy spider personalizado, `ReconSpider`e extraia-o para o diretório de trabalho atual.

```shell-session
NycolasES6@htb[/htb]$ wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
NycolasES6@htb[/htb]$ unzip ReconSpider.zip 
```

Com os arquivos extraídos, você pode executar `ReconSpider.py`usando o seguinte comando:
```shell-session
NycolasES6@htb[/htb]$ python3 ReconSpider.py http://inlanefreight.com
```

Substitua `inlanefreight.com`pelo domínio que você quer rastrear. O spider rastreará o alvo e coletará informações valiosas.

### results.json
Após executar `ReconSpider.py`, os dados serão salvos em um arquivo JSON, `results.json`. Este arquivo pode ser explorado usando qualquer editor de texto. Abaixo está a estrutura do arquivo JSON produzido:

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```

Cada chave no arquivo JSON representa um tipo diferente de dados extraídos do site de destino:

|Chave JSON|Descrição|
|---|---|
|`emails`|Lista os endereços de e-mail encontrados no domínio.|
|`links`|Lista URLs de links encontrados dentro do domínio.|
|`external_files`|Lista URLs de arquivos externos, como PDFs.|
|`js_files`|Lista URLs de arquivos JavaScript usados ​​pelo site.|
|`form_fields`|Lista os campos de formulário encontrados no domínio (vazio neste exemplo).|
|`images`|Lista URLs de imagens encontradas no domínio.|
|`videos`|Lista URLs de vídeos encontrados no domínio (vazio neste exemplo).|
|`audio`|Lista URLs de arquivos de áudio encontrados no domínio (vazio neste exemplo).|
|`comments`|Lista comentários HTML encontrados no código-fonte.|
Ao explorar essa estrutura JSON, você pode obter insights valiosos sobre a arquitetura, o conteúdo e possíveis pontos de interesse do aplicativo web para investigação posterior.


















































