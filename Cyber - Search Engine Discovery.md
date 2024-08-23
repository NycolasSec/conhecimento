Os mecanismos de busca não só ajudam a navegar pela vasta internet, mas também são ferramentas valiosas para coleta de informações e reconhecimento, um processo conhecido como descoberta de mecanismos de busca ou OSINT (Open Source Intelligence).

Essa prática usa algoritmos de busca para descobrir dados que podem não ser imediatamente visíveis, como informações de funcionários, documentos confidenciais e páginas de login ocultas. Profissionais de segurança e pesquisadores utilizam operadores e técnicas especializadas para extrair essas informações importantes.

## Por que a descoberta de mecanismos de busca é importante

A descoberta de mecanismos de busca é um componente crucial do reconhecimento da web por vários motivos:

- `Open Source`:As informações coletadas são publicamente acessíveis, o que as torna uma forma legal e ética de obter insights sobre um alvo.
    
- `Breadth of Information`: Os mecanismos de busca indexam uma grande parte da web, oferecendo uma ampla gama de possíveis fontes de informação.
    
- `Ease of Use`:Os mecanismos de busca são fáceis de usar e não exigem habilidades técnicas especializadas.
    
- `Cost-Effective`:É um recurso gratuito e facilmente disponível para coleta de informações.
    

As informações que você pode reunir dos mecanismos de busca também podem ser aplicadas de diversas maneiras diferentes:

- `Security Assessment`: Identificação de vulnerabilidades, dados expostos e potenciais vetores de ataque.
- `Competitive Intelligence`: Coleta de informações sobre produtos, serviços e estratégias dos concorrentes.
- `Investigative Journalism`: Descobrindo conexões ocultas, transações financeiras e práticas antiéticas.
- `Threat Intelligence`: Identificar ameaças emergentes, rastrear agentes maliciosos e prever possíveis ataques.

No entanto, é importante observar que a descoberta de mecanismos de busca tem limitações. Os mecanismos de busca não indexam todas as informações, e alguns dados podem ser deliberadamente ocultados ou protegidos.

## Operadores de pesquisa

Os operadores de busca são como códigos secretos de mecanismos de busca. Esses comandos e modificadores especiais desbloqueiam um novo nível de precisão e controle, permitindo que você localize tipos específicos de informação em meio à vastidão da web indexada.

Embora a sintaxe exata possa variar um pouco entre mecanismos de busca, os princípios subjacentes permanecem consistentes. Vamos nos aprofundar em alguns operadores de busca essenciais e avançados:

|Operador|Descrição do Operador|Exemplo|Exemplo Descrição|
|:--|:--|:--|:--|
|`site:`|Limita os resultados a um site ou domínio específico.|`site:example.com`|Encontre todas as páginas acessíveis publicamente em example.com.|
|`inurl:`|Encontra páginas com um termo específico no URL.|`inurl:login`|Pesquise páginas de login em qualquer site.|
|`filetype:`|Pesquisa por arquivos de um tipo específico.|`filetype:pdf`|Encontre documentos PDF para download.|
|`intitle:`|Encontra páginas com um termo específico no título.|`intitle:"confidential report"`|Procure por documentos intitulados "relatório confidencial" ou variações semelhantes.|
|`intext:`ou`inbody:`|Pesquisa um termo no corpo do texto das páginas.|`intext:"password reset"`|Identifique páginas da web que contenham o termo “redefinição de senha”.|
|`cache:`|Exibe a versão em cache de uma página da web (se disponível).|`cache:example.com`|Veja a versão em cache de example.com para ver seu conteúdo anterior.|
|`link:`|Encontra páginas que possuem links para uma página da web específica.|`link:example.com`|Identifique sites com links para example.com.|
|`related:`|Encontra sites relacionados a uma página da web específica.|`related:example.com`|Descubra sites semelhantes a example.com.|
|`info:`|Fornece um resumo de informações sobre uma página da web.|`info:example.com`|Obtenha detalhes básicos sobre example.com, como título e descrição.|
|`define:`|Fornece definições de uma palavra ou frase.|`define:phishing`|Obtenha uma definição de "phishing" de várias fontes.|
|`numrange:`|Pesquisa números dentro de um intervalo específico.|`site:example.com numrange:1000-2000`|Encontre páginas no example.com contendo números entre 1000 e 2000.|
|`allintext:`|Encontra páginas que contêm todas as palavras especificadas no corpo do texto.|`allintext:admin password reset`|Procure por páginas que contenham "admin" e "redefinição de senha" no corpo do texto.|
|`allinurl:`|Encontra páginas que contêm todas as palavras especificadas no URL.|`allinurl:admin panel`|Procure por páginas com "admin" e "painel" no URL.|
|`allintitle:`|Encontra páginas contendo todas as palavras especificadas no título.|`allintitle:confidential report 2023`|Procure por páginas com "confidencial", "relatório" e "2023" no título.|
|`AND`|Limita os resultados exigindo que todos os termos estejam presentes.|`site:example.com AND (inurl:admin OR inurl:login)`|Encontre páginas de administração ou login especificamente em example.com.|
|`OR`|Amplia os resultados incluindo páginas com qualquer um dos termos.|`"linux" OR "ubuntu" OR "debian"`|Pesquise por páginas da web que mencionem Linux, Ubuntu ou Debian.|
|`NOT`|Exclui resultados que contêm o termo especificado.|`site:bank.com NOT inurl:login`|Encontre páginas no bank.com, excluindo páginas de login.|
|`*`(curinga)|Representa qualquer caractere ou palavra.|`site:socialnetwork.com filetype:pdf user* manual`|Procure manuais do usuário (guia do usuário, manual do usuário) em formato PDF em socialnetwork.com.|
|`..`(busca por intervalo)|Encontra resultados dentro de um intervalo numérico especificado.|`site:ecommerce.com "price" 100..500`|Procure produtos com preços entre 100 e 500 em um site de comércio eletrônico.|
|`" "`(aspas)|Pesquisa por frases exatas.|`"information security policy"`|Encontre documentos que mencionem a frase exata "política de segurança da informação".|
|`-`(sinal de menos)|Exclui termos dos resultados da pesquisa.|`site:news.com -inurl:sports`|Pesquise artigos de notícias no news.com, excluindo conteúdo relacionado a esportes.|

### Google Dorking

Google Dorking, também conhecido como Google Hacking, é uma técnica que aproveita o poder dos operadores de pesquisa para descobrir informações confidenciais, vulnerabilidades de segurança ou conteúdo oculto em sites, usando a Pesquisa Google.

Aqui estão alguns exemplos comuns de Google Dorks. Para mais exemplos, consulte o [Google Hacking Database](https://www.exploit-db.com/google-hacking-database) :

- Encontrando páginas de login:
    - `site:example.com inurl:login`
    - `site:example.com (inurl:login OR inurl:admin)`
- Identificando arquivos expostos:
    - `site:example.com filetype:pdf`
    - `site:example.com (filetype:xls OR filetype:docx)`
- Descobrindo arquivos de configuração:
    - `site:example.com inurl:config.php`
    - `site:example.com (ext:conf OR ext:cnf)`(procura extensões comumente usadas para arquivos de configuração)
- Localizando backups de banco de dados:
    - `site:example.com inurl:backup`
    - `site:example.com filetype:sql`







































