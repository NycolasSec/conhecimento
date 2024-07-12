## O que é o Elastic Stack

É uma coleção de código aberto de principalmente três aplicativos (Elasticsearch, Logstash e Kibana) que funcionam em harmonia para oferecer aos usuários recursos a de pesquisa e visualização para análise e exploração em tempo real de fontes de arquivos de log.

![[elastic.webp]]

A arquitetura de alto nível da pilha Elastic pode ser aprimorada em ambientes com uso intensivo de recursos com a adição de Kafka, RabbitMQ e Redis para buffering e resiliência, e nginx para segurança.

![[elastic1.webp]]

`Elasticsearch` é um mecanismo de busca distribuído e baseado em JSON, projetado com APIs Restful. Ele lida com indexação, armazenamento e consulta.O Elasticsearch capacita os usuários a conduzir consultas sofisticadas e executar operações analíticas nos registros de arquivo de log processados ​​pelo Logstash.

`Logstash`é responsável por coletar, transformar e transportar registros de arquivos de log.O Logstash opera em três áreas principais:

1. `Process input`: O Logstash ingere registros de arquivos de log de locais remotos, convertendo-os em um formato que as máquinas podem entender, esses registros podem vir de soquetes TCP, syslog e etc. Após processar a entrada, o Logstash prossegue para a próxima função.
2. `Transform and enrich log records`: O Logstash oferece inúmeras maneiras de modificar o formato e até mesmo o conteúdo de um registro de log . Depois que um registro de log é transformado, o Logstash o processa ainda mais.
3. `Send log records to Elasticsearch`: O Logstash utiliza [plugins de saída](https://www.elastic.co/guide/en/logstash/current/output-plugins.html) para transmitir registros de log para o Elasticsearch.

`kibana` serve como ferramenta de visualização para documentos do Elasticsearch. O Kibana simplifica a compreensão dos resultados da consulta usando tabelas, gráficos e painéis personalizados.




























































