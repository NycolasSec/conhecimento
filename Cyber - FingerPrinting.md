**Fingerprinting** é a prática de identificar as tecnologias que alimentam um site ou aplicativo web, semelhante a como uma impressão digital identifica uma pessoa. Ele revela detalhes sobre servidores, sistemas operacionais e softwares, ajudando invasores a personalizar ataques e explorar vulnerabilidades específicas das tecnologias detectadas.

A impressão digital serve como base do reconhecimento da web por vários motivos:

- `Targeted Attacks`: Ao conhecer as tecnologias específicas em uso, os invasores podem concentrar seus esforços em explorações e vulnerabilidades que são conhecidas por afetar esses sistemas. Isso aumenta significativamente as chances de um comprometimento bem-sucedido.
- `Identifying Misconfigurations`:A impressão digital pode expor softwares mal configurados ou desatualizados, configurações padrão ou outras fraquezas que podem não ser aparentes por meio de outros métodos de reconhecimento.
- `Prioritising Targets`:Quando confrontado com múltiplos alvos potenciais, a impressão digital ajuda a priorizar esforços ao identificar sistemas com maior probabilidade de serem vulneráveis ​​ou conterem informações valiosas.
- `Building a Comprehensive Profile`:A combinação de dados de impressão digital com outras descobertas de reconhecimento cria uma visão holística da infraestrutura do alvo, auxiliando na compreensão de sua postura geral de segurança e potenciais vetores de ataque.

## Técnicas de Fingerprinting
Existem várias técnicas usadas para impressão digital de servidores web e tecnologia:

- `Banner Grabbing`: Banner grabbing envolve analisar os banners apresentados por servidores web e outros serviços. Esses banners frequentemente revelam o software do servidor, números de versão e outros detalhes.
- `Analysing HTTP Headers`: Os cabeçalhos HTTP transmitidos com cada solicitação e resposta de página da web contêm uma riqueza de informações. O `Server`cabeçalho normalmente revela o software do servidor web, enquanto o `X-Powered-By`cabeçalho pode revelar tecnologias adicionais, como linguagens de script ou frameworks.
- `Probing for Specific Responses`: Enviar solicitações especialmente elaboradas para o alvo pode provocar respostas únicas que revelam tecnologias ou versões específicas. Por exemplo, certas mensagens de erro ou comportamentos são característicos de servidores web ou componentes de software específicos.
- `Analysing Page Content`: O conteúdo de uma página da web, incluindo sua estrutura, scripts e outros elementos, pode frequentemente fornecer pistas sobre as tecnologias subjacentes. Pode haver um cabeçalho de direitos autorais que indica um software específico sendo usado, por exemplo.

Existe uma variedade de ferramentas que automatizam o processo de impressão digital, combinando várias técnicas para identificar servidores web, sistemas operacionais, sistemas de gerenciamento de conteúdo e outras tecnologias:

|Ferramenta|Descrição|Características|
|---|---|---|
|`Wappalyzer`|Extensão do navegador e serviço online para criação de perfil de tecnologia de sites.|Identifica uma ampla gama de tecnologias da web, incluindo CMSs, estruturas, ferramentas de análise e muito mais.|
|`BuiltWith`|Criador de perfil de tecnologia da Web que fornece relatórios detalhados sobre a pilha de tecnologia de um site.|Oferece planos gratuitos e pagos com diferentes níveis de detalhes.|
|`WhatWeb`|Ferramenta de linha de comando para impressão digital de sites.|Utiliza um vasto banco de dados de assinaturas para identificar diversas tecnologias da web.|
|`Nmap`|Scanner de rede versátil que pode ser usado para diversas tarefas de reconhecimento, incluindo impressão digital de serviço e sistema operacional.|Pode ser usado com scripts (NSE) para executar impressões digitais mais especializadas.|
|`Netcraft`|Oferece uma variedade de serviços de segurança na web, incluindo impressão digital de sites e relatórios de segurança.|Fornece relatórios detalhados sobre a tecnologia de um site, provedor de hospedagem e postura de segurança.|
|`wafw00f`|Ferramenta de linha de comando projetada especificamente para identificar firewalls de aplicativos da Web (WAFs).|Ajuda a determinar se um WAF está presente e, em caso afirmativo, seu tipo e configuração.|

## Fingerprinting inlanefright.com
Vamos aplicar nosso conhecimento de fingerprinting para descobrir o DNA digital do nosso host criado para esse propósito, `inlanefreight.com`.

## Pegando o Banner
Nosso primeiro passo é coletar informações diretamente do próprio servidor web. Podemos fazer isso usando o `curl`comando com a flag `-I` (ou `--head`) para buscar apenas os cabeçalhos HTTP, não o conteúdo inteiro da página.

```shell-session
NycolasES6@htb[/htb]$ curl -I inlanefreight.com
```

A saída incluirá o banner do servidor, revelando o software do servidor web e o número da versão:

```shell-session
NycolasES6@htb[/htb]$ curl -I inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

Neste caso, vemos que `inlanefreight.com` está sendo executado em `Apache/2.4.41`, especificamente a versão `Ubuntu`. Esta informação é nossa primeira pista, sugerindo a pilha de tecnologia subjacente. Ele também está tentando redirecionar para `https://inlanefreight.com/` então pegue esses banners também

```shell-session
NycolasES6@htb[/htb]$ curl -I https://inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```

Agora temos um cabeçalho realmente interessante, o servidor está tentando nos redirecionar novamente, mas desta vez vemos que é ele `WordPress` quem está fazendo o redirecionamento para`https://www.inlanefreight.com/`

```shell-session
NycolasES6@htb[/htb]$ curl -I https://www.inlanefreight.com

HTTP/1.1 200 OK
Date: Fri, 31 May 2024 12:12:26 GMT
Server: Apache/2.4.41 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```

Mais alguns cabeçalhos interessantes, incluindo um caminho interessante que contém `wp-json`. O prefixo `wp-` é comum ao WordPress.

### Wafw00f

`Web Application Firewalls`( `WAFs`) são soluções de segurança projetadas para proteger aplicativos da web de vários ataques. Antes de prosseguir com a impressão digital, é crucial determinar se `inlanefreight.com`emprega um WAF, pois ele pode interferir em nossas sondas ou potencialmente bloquear nossas solicitações.

Para detectar a presença de um WAF, usaremos a  ferramenta`wafw00f`. Para instalar `wafw00f`, você pode usar pip3:

```shell-session
NycolasES6@htb[/htb]$ pip3 install git+https://github.com/EnableSecurity/wafw00f
```

Após a instalação, passe o domínio que você deseja verificar como argumento para a ferramenta:

```shell-session
NycolasES6@htb[/htb]$ wafw00f inlanefreight.com

                ______
               /      \
              (  W00f! )
               \  ____/
               ,,    __            404 Hack Not Found
           |`-.__   / /                      __     __
           /"  _/  /_/                       \ \   / /
          *===*    /                          \ \_/ /  405 Not Allowed
         /     )__//                           \   /
    /|  /     /---`                        403 Forbidden
    \\/`   \ |                                 / _ \
    `\    /_\\_              502 Bad Gateway  / / \ \  500 Internal Error
      `_____``-`                             /_/   \_\

                        ~ WAFW00F : v2.2.0 ~
        The Web Application Firewall Fingerprinting Toolkit
    
[*] Checking https://inlanefreight.com
[+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
[~] Number of requests: 2
```

A verificação `wafw00f` revela que o site `inlanefreight.com` é protegido pelo `Wordfence Web Application Firewall`( `WAF`), desenvolvido pela Defiant.

Isso significa que o site possui uma camada de segurança adicional, como um **Web Application Firewall (WAF)**, que pode bloquear ou filtrar tentativas de reconhecimento. Em um cenário real, é importante estar ciente disso ao prosseguir com a investigação, pois pode ser necessário ajustar as técnicas para contornar ou evitar os mecanismos de detecção do WAF.

### Nikto
`Nikto`é um poderoso scanner de servidor web de código aberto. Além de sua função primária como uma ferramenta de avaliação de vulnerabilidade, os recursos de fingerprinting do `Nikto` fornecem insights sobre a pilha de tecnologia de um site.

Para escanear `inlanefreight.com` usando `Nikto`, executando apenas os módulos de impressão digital, execute o seguinte comando:

```shell-session
NycolasES6@htb[/htb]$ nikto -h inlanefreight.com -Tuning b
```

O sinalizador `-h` especifica o host de destino. O sinalizador `-Tuning b` informa o `Nikto` para executar somente os módulos de Identificação de Software.

`Nikto` iniciará então uma série de testes, tentando identificar software desatualizado, arquivos ou configurações inseguros e outros potenciais riscos de segurança.

```shell-session
NycolasES6@htb[/htb]$ nikto -h inlanefreight.com -Tuning b

- Nikto v2.5.0
---------------------------------------------------------------------------
+ Multiple IPs found: 134.209.24.248, 2a03:b0c0:1:e0::32c:b001
+ Target IP:          134.209.24.248
+ Target Hostname:    www.inlanefreight.com
+ Target Port:        443
---------------------------------------------------------------------------
+ SSL Info:        Subject:  /CN=inlanefreight.com
                   Altnames: inlanefreight.com, www.inlanefreight.com
                   Ciphers:  TLS_AES_256_GCM_SHA384
                   Issuer:   /C=US/O=Let's Encrypt/CN=R3

<SNIP>
```

A varredura de reconhecimento `inlanefreight.com`revela várias descobertas importantes:

- `IPs`: O site resolve para endereços IPv4 ( `134.209.24.248`) e IPv6 ( `2a03:b0c0:1:e0::32c:b001`).
- `Server Technology`: O site é executado em`Apache/2.4.41 (Ubuntu)`
- `WordPress Presence`: A varredura identificou uma instalação do WordPress, incluindo a página de login ( `/wp-login.php`). Isso sugere que o site pode ser um alvo potencial para exploits comuns relacionados ao WordPress.
- `Information Disclosure`: A presença de um `license.txt`arquivo pode revelar detalhes adicionais sobre os componentes de software do site.
- `Headers`: Vários cabeçalhos não padronizados ou inseguros foram encontrados, incluindo um `Strict-Transport-Security`cabeçalho ausente e um `x-redirect-by`cabeçalho potencialmente inseguro.



