`Subdomain Brute-Force Enumeration`é uma técnica de descoberta de subdomínio ativo que alavanca listas predefinidas de nomes de subdomínio em potencial. Essa abordagem testa sistematicamente esses nomes em relação ao domínio de destino para identificar subdomínios válidos.

O processo se divide em quatro etapas:

1. `Wordlist Selection`: O processo começa com a seleção de uma lista de palavras contendo nomes de subdomínios em potencial. Essas listas de palavras podem ser:
    - `General-Purpose`: Contendo uma ampla gama de nomes de subdomínios comuns (por exemplo, `dev`, `staging`, `blog`, `mail`, `admin`, `test`). Essa abordagem é útil quando você não conhece as convenções de nomenclatura do alvo.
    - `Targeted`: Focado em indústrias, tecnologias ou padrões de nomenclatura específicos relevantes para o alvo. Essa abordagem é mais eficiente e reduz as chances de falsos positivos.
    - `Custom`: Você pode criar sua própria lista de palavras com base em palavras-chave específicas, padrões ou informações coletadas de outras fontes.
2. `Iteration and Querying`: Um script ou ferramenta itera pela lista de palavras, anexando cada palavra ou frase ao domínio principal (por exemplo, `example.com`) para criar possíveis nomes de subdomínio (por exemplo, `dev.example.com`, `staging.example.com`).
3. `DNS Lookup`: Uma consulta DNS é realizada para cada subdomínio potencial para verificar se ele resolve para um endereço IP. Isso é feito tipicamente usando o tipo de registro A ou AAAA.
4. `Filtering and Validation`: Se um subdomínio for resolvido com sucesso, ele será adicionado a uma lista de subdomínios válidos. Outras etapas de validação podem ser tomadas para confirmar a existência e a funcionalidade do subdomínio (por exemplo, tentando acessá-lo por meio de um navegador da web).

Existem várias ferramentas disponíveis que se destacam na enumeração de força bruta:

| Ferramenta                                              | Descrição                                                                                                                                            |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [dnsenum](https://github.com/fwaeytens/dnsenum)         | Ferramenta abrangente de enumeração de DNS que oferece suporte a ataques de dicionário e força bruta para descobrir subdomínios.                     |
| [fierce](https://github.com/mschwager/fierce)           | Ferramenta fácil de usar para descoberta recursiva de subdomínios, com detecção de curingas e uma interface fácil de usar.                           |
| [dns recon](https://github.com/darkoperator/dnsrecon)   | Ferramenta versátil que combina diversas técnicas de reconhecimento de DNS e oferece formatos de saída personalizáveis.                              |
| [amass](https://github.com/owasp-amass/amass)           | Ferramenta mantida ativamente e focada na descoberta de subdomínios, conhecida por sua integração com outras ferramentas e extensas fontes de dados. |
| [assetfinder](https://github.com/tomnomnom/assetfinder) | Ferramenta simples, porém eficaz, para encontrar subdomínios usando várias técnicas, ideal para verificações rápidas e leves.                        |
| [puredns](https://github.com/d3mondev/puredns)          | Ferramenta de força bruta de DNS poderosa e flexível, capaz de resolver e filtrar resultados de forma eficaz.                                        |

### Enumeração DNS
`dnsenum`é uma ferramenta de linha de comando versátil e amplamente usada escrita em Perl. É um kit de ferramentas abrangente para reconhecimento de DNS, fornecendo várias funcionalidades para reunir informações sobre a infraestrutura de DNS de um domínio alvo e subdomínios potenciais. A ferramenta oferece várias funções principais:

- `DNS Record Enumeration`: `dnsenum`pode recuperar vários registros DNS, incluindo registros A, AAAA, NS, MX e TXT, fornecendo uma visão geral abrangente da configuração DNS do alvo.
- `Zone Transfer Attempts`: A ferramenta tenta automaticamente transferências de zona de servidores de nomes descobertos. Enquanto a maioria dos servidores é configurada para impedir transferências de zona não autorizadas, uma tentativa bem-sucedida pode revelar um tesouro de informações de DNS.
- `Subdomain Brute-Forcing`: `dnsenum`suporta enumeração de força bruta de subdomínios usando uma lista de palavras. Isso envolve testar sistematicamente nomes de subdomínios potenciais em relação ao domínio de destino para identificar os válidos.
- `Google Scraping`: A ferramenta pode analisar os resultados de pesquisa do Google para encontrar subdomínios adicionais que podem não estar listados diretamente nos registros DNS.
- `Reverse Lookup`: `dnsenum`pode executar pesquisas reversas de DNS para identificar domínios associados a um determinado endereço IP, potencialmente revelando outros sites hospedados no mesmo servidor.
- `WHOIS Lookups`: A ferramenta também pode executar consultas WHOIS para coletar informações sobre propriedade de domínio e detalhes de registro.

Vamos ver `dnsenum`em ação demonstrando como enumerar subdomínios para nosso alvo, `inlanefreight.com`. Nesta demonstração, usaremos a `subdomains-top1million-5000.txt`lista de palavras de [SecLists](https://github.com/danielmiessler/SecLists) , que contém os 5000 subdomínios mais comuns.

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

Neste comando:

- `dnsenum --enum inlanefreight.com`: Especificamos o domínio de destino que queremos enumerar, juntamente com um atalho para algumas opções de ajuste ``--enum`.
- `-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`: Indicamos o caminho para a lista de palavras SecLists que usaremos para força bruta.
- `-r`: Esta opção habilita a força bruta recursiva de subdomínio, o que significa que se `dnsenum`encontrar um subdomínio, ele tentará enumerar subdomínios daquele subdomínio.

```shell-session
NycolasES6@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```
















































