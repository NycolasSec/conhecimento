Crawling, também conhecido como spidering, é o processo automatizado de navegar sistematicamente na web. Um rastreador da web segue links de uma página para outra, coletando informações.

Esses rastreadores, que são bots, utilizam algoritmos para descobrir e indexar páginas, facilitando o acesso por mecanismos de busca ou para outras finalidades, como análise de dados.

## Como funcionam os Web Crawlers
A operação de um rastreador da web começa com uma URL semente, que é a página inicial a ser rastreada. O rastreador acessa essa página, analisa o conteúdo e extrai os links, adicionando-os a uma fila para rastrear em seguida. Esse processo é repetido, permitindo ao rastreador explorar um site inteiro ou uma grande parte da web, dependendo da configuração.

1. `Homepage`:Você começa com a página inicial contendo `link1`, `link2`, e `link3`.
    ```txt
    Homepage
    ├── link1
    ├── link2
    └── link3
    ```

2. `Visiting link1`: Visitar `link1`mostra a página inicial, `link2`, e também `link4`e `link5`.
	```txt
    link1 Page
    ├── Homepage
    ├── link2
    ├── link4
    └── link5
	```

3. `Continuing the Crawl`: O rastreador continua seguindo esses links sistematicamente, reunindo todas as páginas acessíveis e seus links.

Existem dois tipos principais de estratégias de rastreamento.

### Rastejamento em largura
![[Pasted image 20240821075605.png]]
`Breadth-first crawling`prioriza explorar a largura de um site antes de ir fundo. Ele começa rastreando todos os links na página semente, depois passa para os links nessas páginas, e assim por diante. Isso é útil para obter uma visão geral ampla da estrutura e do conteúdo de um site.

### Rastejamento em profundidade
![[Pasted image 20240821075722.png]]
Em contraste, `depth-first crawling`prioriza a profundidade em vez da amplitude. Ele segue um único caminho de links o máximo possível antes de voltar atrás e explorar outros caminhos. Isso pode ser útil para encontrar conteúdo específico ou alcançar profundamente a estrutura de um site.

A escolha da estratégia depende dos objetivos específicos do processo de rastreamento.

## Extraindo informações valiosas
Os rastreadores podem extrair uma variedade diversificada de dados, cada um servindo a um propósito específico no processo de reconhecimento:

- `Links (Internal and External)`: Esses são os blocos de construção fundamentais da web, conectando páginas dentro de um site ( `internal links`) e para outros sites ( `external links`). Os rastreadores coletam meticulosamente esses links, permitindo que você mapeie a estrutura de um site, descubra páginas ocultas e identifique relacionamentos com recursos externos.
- `Comments`: Seções de comentários em blogs, fóruns ou outras páginas interativas podem ser uma mina de ouro de informações. Os usuários frequentemente revelam inadvertidamente detalhes sensíveis, processos internos ou dicas de vulnerabilidades em seus comentários.
- `Metadata`: Metadados referem-se a `data about data`. No contexto de páginas da web, incluem informações como títulos de página, descrições, palavras-chave, nomes de autores e datas. Esses metadados podem fornecer contexto valioso sobre o conteúdo, propósito e relevância de uma página para seus objetivos de reconhecimento.
- `Sensitive Files`: Os rastreadores da Web podem ser configurados para procurar ativamente por arquivos sensíveis que podem ser expostos inadvertidamente em um site. Isso inclui `backup files`(por exemplo, `.bak`, `.old`), `configuration files`(por exemplo, `web.config`, `settings.php`), `log files`(por exemplo, `error_log`, `access_log`) e outros arquivos contendo senhas, `API keys`, ou outras informações confidenciais. Examinar cuidadosamente os arquivos extraídos, especialmente os arquivos de backup e configuração, pode revelar um tesouro de informações sensíveis, como `database credentials`, `encryption keys`, ou até mesmo trechos de código-fonte.
### A Importância do Contexto

Entender o contexto dos dados extraídos é crucial. Informações isoladas, como um comentário sobre uma versão de software, podem parecer insignificantes, mas quando combinadas com outros dados, como metadados de uma versão desatualizada ou um arquivo de configuração vulnerável, podem revelar uma vulnerabilidade crítica.

O verdadeiro valor dos dados está em conectar os pontos e formar uma visão abrangente do cenário digital. Por exemplo, uma lista de links pode parecer comum, mas ao identificar um padrão, como várias URLs apontando para um diretório acessível publicamente, você pode descobrir dados sensíveis.

A análise contextual de dados, considerando suas inter-relações, é fundamental para uma compreensão completa e para identificar possíveis riscos.



































