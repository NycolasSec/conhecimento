## Ferramentas DNS
Aqui estão algumas das ferramentas mais populares e versáteis no arsenal de profissionais de reconhecimento da web:

|Ferramenta|Principais características|Casos de uso|
|---|---|---|
|`dig`|Ferramenta de pesquisa de DNS versátil que suporta vários tipos de consulta (A, MX, NS, TXT, etc.) e saída detalhada.|Consultas manuais de DNS, transferências de zona (se permitidas), solução de problemas de DNS e análise aprofundada de registros de DNS.|
|`nslookup`|Ferramenta de pesquisa de DNS mais simples, principalmente para registros A, AAAA e MX.|Consultas básicas de DNS, verificações rápidas de resolução de domínio e registros de servidor de e-mail.|
|`host`|Ferramenta de pesquisa de DNS simplificada com saída concisa.|Verificações rápidas de registros A, AAAA e MX.|
|`dnsenum`|Ferramenta automatizada de enumeração de DNS, ataques de dicionário, força bruta, transferências de zona (se permitido).|Descobrindo subdomínios e coletando informações de DNS de forma eficiente.|
|`fierce`|Ferramenta de reconhecimento de DNS e enumeração de subdomínios com pesquisa recursiva e detecção de curinga.|Interface amigável para reconhecimento de DNS, identificando subdomínios e alvos potenciais.|
|`dnsrecon`|Combina diversas técnicas de reconhecimento de DNS e suporta vários formatos de saída.|Enumeração abrangente de DNS, identificação de subdomínios e coleta de registros de DNS para análise posterior.|
|`theHarvester`|Ferramenta OSINT que coleta informações de várias fontes, incluindo registros DNS (endereços de e-mail).|Coleta de endereços de e-mail, informações de funcionários e outros dados associados a um domínio de várias fontes.|
|Serviços de pesquisa de DNS on-line|Interfaces fáceis de usar para realizar pesquisas de DNS.|Pesquisas de DNS rápidas e fáceis, convenientes quando as ferramentas de linha de comando não estão disponíveis, verificando a disponibilidade do domínio ou informações básicas|

## Domain Information Gruper(dig)

O comando dig é um utilitário versátil e poderoso para consultar servidores DNS e recuperar vários tipos de registros DNS. Sua flexibilidade e saída detalhada e personalizável o tornam uma escolha obrigatória.

### Comandos comuns de dig

|Comando|Descrição|
|---|---|
|`dig domain.com`|Executa uma pesquisa de registro A padrão para o domínio.|
|`dig domain.com A`|Recupera o endereço IPv4 (registro A) associado ao domínio.|
|`dig domain.com AAAA`|Recupera o endereço IPv6 (registro AAAA) associado ao domínio.|
|`dig domain.com MX`|Encontra os servidores de e-mail (registros MX) responsáveis ​​pelo domínio.|
|`dig domain.com NS`|Identifica os servidores de nomes autorizados para o domínio.|
|`dig domain.com TXT`|Recupera todos os registros TXT associados ao domínio.|
|`dig domain.com CNAME`|Recupera o registro de nome canônico (CNAME) para o domínio.|
|`dig domain.com SOA`|Recupera o registro de início de autoridade (SOA) para o domínio.|
|`dig @1.1.1.1 domain.com`|Especifica um servidor de nomes específico para consultar; neste caso 1.1.1.1|
|`dig +trace domain.com`|Mostra o caminho completo da resolução de DNS.|
|`dig -x 192.168.1.1`|Executa uma pesquisa reversa no endereço IP 192.168.1.1 para encontrar o nome do host associado. Talvez seja necessário especificar um servidor de nomes.|
|`dig +short domain.com`|Fornece uma resposta curta e concisa à consulta.|
|`dig +noall +answer domain.com`|Exibe apenas a seção de resposta da saída da consulta.|
|`dig domain.com ANY`|Recupera todos os registros DNS disponíveis para o domínio (Observação: muitos servidores DNS ignoram `ANY`consultas para reduzir a carga e evitar abusos, conforme [RFC 8482](https://datatracker.ietf.org/doc/html/rfc8482) ).|

## Groping DNS
```sh
dig google.com
```
![[Pasted image 20240816093941.png]]

A saída pode ser dividida em quatro seções principais:

1. Cabeçalho
    
    - `;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449`: Esta linha indica o tipo de consulta ( `QUERY`), o status bem-sucedido ( `NOERROR`) e um identificador exclusivo ( `16449`) para esta consulta específica.
        
        - `;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0`: Isso descreve os sinalizadores no cabeçalho DNS:
            - `qr`: Sinalizador de resposta de consulta - indica que esta é uma resposta.
            - `rd`: Sinalizador de recursão desejada - significa que a recursão foi solicitada.
            - `ad`: Sinalizador de dados autênticos - significa que o resolvedor considera os dados autênticos.
            - Os números restantes indicam o número de entradas em cada seção da resposta do DNS: 1 pergunta, 1 resposta, 0 registros de autoridade e 0 registros adicionais.
    - `;; WARNING: recursion requested but not available`: Isso indica que a recursão foi solicitada, mas o servidor não a suporta.
        
2. Seção de perguntas
    
    - `;google.com. IN A`:Esta linha especifica a pergunta: "Para que serve o endereço IPv4 (registro A) `google.com`?"
3. Seção de Respostas
    
    - `google.com. 0 IN A 142.251.47.142`: Esta é a resposta para a consulta. Ela indica que o endereço IP associado a `google.com`é `142.251.47.142`. O ' `0`' representa o `TTL`(time-to-live), indicando por quanto tempo o resultado pode ser armazenado em cache antes de ser atualizado.
4. Rodapé
    
    - `;; Query time: 0 msec`: Isso mostra o tempo que levou para a consulta ser processada e a resposta ser recebida (0 milissegundos).
        
    - `;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)`: Isso identifica o servidor DNS que forneceu a resposta e o protocolo usado (UDP).
        
    - `;; WHEN: Thu Jun 13 10:45:58 SAST 2024`: Este é o registro de data e hora em que a consulta foi feita.
        
    - `;; MSG SIZE rcvd: 54`: Isso indica o tamanho da mensagem DNS recebida (54 bytes).

Às vezes, pode existir um `opt pseudosection` em uma consulta `dig`. Isso se deve aos Mecanismos de Extensão para DNS ( `EDNS`), que permitem recursos adicionais, como tamanhos de mensagem maiores e suporte a Extensões de Segurança de DNS ( `DNSSEC` ).

Se você quiser apenas a resposta para a pergunta, sem nenhuma outra informação, você pode consultar `dig`usando `+short`:

```sh
dig +short heckthebox.com

104.18.20.126
104.18.21.126
```





































