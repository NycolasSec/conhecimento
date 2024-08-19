Na massa crescente da internet, a confiança é uma mercadoria frágil. Um dos pilares dessa confiança é o protocolo `Secure Sockets Layer/Transport Layer Security`( `SSL/TLS`), que criptografa a comunicação entre seu navegador e um site. No coração do SSL/TLS está o `digital certificate`, um pequeno arquivo que verifica a identidade de um site e permite uma comunicação segura e criptografada.

No entanto, o processo de emissão e gerenciamento desses certificados não é infalível. Os invasores podem explorar certificados falsos ou emitidos incorretamente para personificar sites legítimos, interceptar dados confidenciais ou espalhar malware.

## O que são Logs de Transparência de Certificados?
`Certificate Transparency`( `CT`) logs são livros-razão públicos, somente anexos, que registram a emissão de certificados SSL/TLS. Sempre que uma Autoridade Certificadora (CA) emite um novo certificado, ela deve enviá-lo a vários logs de CT. Organizações independentes mantêm esses logs e estão abertos para qualquer um inspecionar.

Pense nos logs de CT como um `global registry of certificates`. Eles fornecem um registro transparente e verificável de cada certificado SSL/TLS emitido para um site. Essa transparência atende a vários propósitos cruciais:

- `Early Detection of Rogue Certificates`: Ao monitorar logs de CT, pesquisadores de segurança e proprietários de sites podem identificar rapidamente certificados suspeitos ou emitidos incorretamente. Um certificado desonesto é um certificado digital não autorizado ou fraudulento emitido por uma autoridade de certificação confiável. Detectá-los cedo permite uma ação rápida para revogar os certificados antes que eles possam ser usados ​​para fins maliciosos.
- `Accountability for Certificate Authorities`: Os logs de CT responsabilizam as CAs por suas práticas de emissão. Se uma CA emitir um certificado que viole as regras ou padrões, ele ficará publicamente visível nos logs, levando a potenciais sanções ou perda de confiança.
- `Strengthening the Web PKI (Public Key Infrastructure)`: O Web PKI é o sistema de confiança que sustenta a comunicação online segura. Os logs de CT ajudam a aprimorar a segurança e a integridade do Web PKI ao fornecer um mecanismo para supervisão pública e verificação de certificados.

## Registros de TC e reconhecimento da Web

Os **Logs de Transparência de Certificado (CT)** oferecem uma abordagem poderosa e eficiente para a enumeração de subdomínios, superando métodos tradicionais como força bruta ou listas de palavras. Ao invés de depender de suposições ou de prever nomes de subdomínios, os logs de CT fornecem registros definitivos de certificados emitidos para um domínio e seus subdomínios.

Essa abordagem permite acesso a uma visão histórica abrangente dos subdomínios, incluindo aqueles que podem não estar mais ativos ou que são difíceis de adivinhar. Além disso, os logs de CT podem revelar subdomínios associados a certificados antigos ou expirados, que podem estar rodando software ou configurações desatualizadas, tornando-os potenciais alvos para exploração.

Em resumo, os logs de CT oferecem uma maneira confiável de descobrir subdomínios sem a necessidade de força bruta exaustiva ou de confiar em listas de palavras. Eles proporcionam uma visão detalhada do histórico de um domínio, permitindo a descoberta de subdomínios ocultos que podem melhorar significativamente a eficiência e a precisão do reconhecimento em atividades de segurança cibernética.

## Pesquisando registros de CT
Existem duas opções populares para pesquisar logs de TC:

| Ferramenta                          | Principais características                                                                                                         | Casos de uso                                                                                                                | Prós                                                  | Contras                                      |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| [crt.sh](https://crt.sh/)           | Interface web amigável, pesquisa simples por domínio, exibe detalhes do certificado e entradas SAN.                                | Pesquisas rápidas e fáceis, identificação de subdomínios, verificação do histórico de emissão de certificados.              | Gratuito, fácil de usar, sem necessidade de registro. | Opções limitadas de filtragem e análise.     |
| [Censys](https://search.censys.io/) | Mecanismo de busca poderoso para dispositivos conectados à internet, filtragem avançada por domínio, IP, atributos de certificado. | Análise aprofundada de certificados, identificando configurações incorretas, encontrando certificados e hosts relacionados. | Amplas opções de dados e filtragem, acesso à API.     | Requer registro (nível gratuito disponível). |

### pesquisa crt.sh
Embora `crt.sh`ofereça uma interface web conveniente, você também pode aproveitar sua API para pesquisas automatizadas diretamente do seu terminal. Vamos ver como encontrar todos os subdomínios 'dev' `facebook.com`usando `curl` e `jq`:

```shell-session
NycolasES6@htb[/htb]$ curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
 
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com
```

- `curl -s "https://crt.sh/?q=facebook.com&output=json"`: Este comando busca a saída JSON de crt.sh para certificados correspondentes ao domínio `facebook.com`.
- `jq -r '.[] | select(.name_value | contains("dev")) | .name_value'`: Esta parte filtra os resultados JSON, selecionando apenas entradas onde o `name_value`campo (que contém o domínio ou subdomínio) inclui a string " `dev.`" O `-r`sinalizador informa `jq`para gerar strings brutas.
- `sort -u`: Isso classifica os resultados em ordem alfabética e remove duplicatas.






























