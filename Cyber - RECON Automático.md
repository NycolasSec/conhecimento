Embora o reconhecimento manual possa ser eficaz, ele também pode ser demorado e propenso a erros humanos. Automatizar tarefas de reconhecimento da web pode aumentar significativamente a eficiência e a precisão, permitindo que você reúna informações em escala e identifique vulnerabilidades potenciais mais rapidamente.

## Por que automatizar o reconhecimento?

A automação oferece diversas vantagens importantes para o reconhecimento da web:

- `Efficiency`: Ferramentas automatizadas podem executar tarefas repetitivas muito mais rápido que humanos, liberando tempo valioso para análise e tomada de decisões.
- `Scalability`:A automação permite que você amplie seus esforços de reconhecimento em um grande número de alvos ou domínios, revelando um escopo mais amplo de informações.
- `Consistency`: Ferramentas automatizadas seguem regras e procedimentos predefinidos, garantindo resultados consistentes e reproduzíveis e minimizando o risco de erro humano.
- `Comprehensive Coverage`: A automação pode ser programada para executar uma ampla gama de tarefas de reconhecimento, incluindo enumeração de DNS, descoberta de subdomínios, rastreamento da web, varredura de portas e muito mais, garantindo cobertura completa de possíveis vetores de ataque.
- `Integration`:Muitas estruturas de automação permitem fácil integração com outras ferramentas e plataformas, criando um fluxo de trabalho perfeito, do reconhecimento à avaliação e exploração de vulnerabilidades.

## Estruturas de reconhecimento

Essas estruturas visam fornecer um conjunto completo de ferramentas para reconhecimento da web:

- [FinalRecon](https://github.com/thewhiteh4t/FinalRecon) : Uma ferramenta de reconhecimento baseada em Python que oferece uma gama de módulos para diferentes tarefas, como verificação de certificado SSL, coleta de informações Whois, análise de cabeçalho e rastreamento. Sua estrutura modular permite fácil personalização para necessidades específicas.
- [Recon-ng](https://github.com/lanmaster53/recon-ng) : Um framework poderoso escrito em Python que oferece uma estrutura modular com vários módulos para diferentes tarefas de reconhecimento. Ele pode executar enumeração de DNS, descoberta de subdomínio, varredura de porta, rastreamento da web e até mesmo explorar vulnerabilidades conhecidas.
- [theHarvester](https://github.com/laramies/theHarvester) : Projetado especificamente para reunir endereços de e-mail, subdomínios, hosts, nomes de funcionários, portas abertas e banners de diferentes fontes públicas, como mecanismos de busca, servidores de chaves PGP e o banco de dados SHODAN. É uma ferramenta de linha de comando escrita em Python.
- [SpiderFoot](https://github.com/smicallef/spiderfoot) : Uma ferramenta de automação de inteligência de código aberto que se integra a várias fontes de dados para coletar informações sobre um alvo, incluindo endereços IP, nomes de domínio, endereços de e-mail e perfis de mídia social. Ela pode executar pesquisas de DNS, rastreamento da web, varredura de portas e muito mais.
- [OSINT Framework](https://osintframework.com/) : Uma coleção de várias ferramentas e recursos para coleta de inteligência de código aberto. Ela abrange uma ampla gama de fontes de informação, incluindo mídias sociais, mecanismos de busca, registros públicos e muito mais.

### FinalRecon

`FinalRecon`oferece uma riqueza de informações de reconhecimento:

- `Header Information`: Revela detalhes do servidor, tecnologias usadas e possíveis configurações incorretas de segurança.
- `Whois Lookup`: Descobre detalhes de registro de domínio, incluindo informações do registrante e detalhes de contato.
- `SSL Certificate Information`: Examina o certificado SSL/TLS quanto à validade, emissor e outros detalhes relevantes.
- `Crawler`:
    - HTML, CSS, JavaScript: Extrai links, recursos e potenciais vulnerabilidades desses arquivos.
    - Links internos/externos: mapeiam a estrutura do site e identificam conexões com outros domínios.
    - Imagens, robots.txt, sitemap.xml: reúne informações sobre caminhos de rastreamento permitidos/não permitidos e estrutura do site.
    - Links em JavaScript, Wayback Machine: descobre links ocultos e dados históricos de sites.
- `DNS Enumeration`: Consulta mais de 40 tipos de registros DNS, incluindo registros DMARC para avaliação de segurança de e-mail.
- `Subdomain Enumeration`: Aproveita várias fontes de dados (crt.sh, AnubisDB, ThreatMiner, CertSpotter, API do Facebook, API do VirusTotal, API do Shodan, API do BeVigil) para descobrir subdomínios.
- `Directory Enumeration`: Suporta listas de palavras personalizadas e extensões de arquivo para descobrir diretórios e arquivos ocultos.
- `Wayback Machine`: Recupera URLs dos últimos cinco anos para analisar alterações no site e possíveis vulnerabilidades.

A instalação é rápida e fácil:
```shell-session
NycolasES6@htb[/htb]$ git clone https://github.com/thewhiteh4t/FinalRecon.git
NycolasES6@htb[/htb]$ cd FinalRecon
NycolasES6@htb[/htb]$ pip3 install -r requirements.txt
NycolasES6@htb[/htb]$ chmod +x ./finalrecon.py
NycolasES6@htb[/htb]$ ./finalrecon.py --help
```

Para começar, você primeiro clonará o `FinalRecon`repositório do GitHub usando `git clone https://github.com/thewhiteh4t/FinalRecon.git` . Isso criará um novo diretório chamado "FinalRecon" contendo todos os arquivos necessários.

Em seguida, navegue até o diretório recém-criado com `cd FinalRecon`. Uma vez lá dentro, você instalará as dependências necessárias do Python usando `pip3 install -r requirements.txt`. Isso garante que `FinalRecon`ele tenha todas as bibliotecas e módulos necessários para funcionar corretamente.

Para garantir que o script principal seja executável, você precisará alterar as permissões do arquivo usando `chmod +x ./finalrecon.py`. Isso permite que você execute o script diretamente do seu terminal.

Por fim, você pode verificar se `FinalRecon`está instalado corretamente e obter uma visão geral de suas opções disponíveis executando `./finalrecon.py --help`. Isso exibirá uma mensagem de ajuda com detalhes sobre como usar a ferramenta, incluindo os vários módulos e suas respectivas opções:

| Opção         | Argumento | Descrição                                                     |
| ------------- | --------- | ------------------------------------------------------------- |
| `-h`,`--help` |           | Mostrar a mensagem de ajuda e sair.                           |
| `--url`       | URL       | Especifique o URL de destino.                                 |
| `--headers`   |           | Recuperar informações de cabeçalho para o URL de destino.     |
| `--sslinfo`   |           | Obtenha informações do certificado SSL para o URL de destino. |
| `--whois`     |           | Execute uma pesquisa Whois para o domínio de destino.         |
| `--crawl`     |           | Rastreie o site de destino.                                   |
| `--dns`       |           | Execute a enumeração de DNS no domínio de destino.            |
| `--sub`       |           | Enumere subdomínios para o domínio de destino.                |
| `--dir`       |           | Pesquise diretórios no site de destino.                       |
| `--wayback`   |           | Recupere URLs do Wayback para o alvo.                         |
| `--ps`        |           | Execute uma varredura rápida de porta no alvo.                |
| `--full`      |           | Execute uma varredura de reconhecimento completa no alvo.     |

Por exemplo, se quisermos `FinalRecon`coletar informações de cabeçalho e executar uma pesquisa Whois para `inlanefreight.com`, usaríamos os sinalizadores correspondentes ( `--headers`e `--whois`), então o comando seria:

```shell-session
NycolasES6@htb[/htb]$ ./finalrecon.py --headers --whois --url http://inlanefreight.com
```













































