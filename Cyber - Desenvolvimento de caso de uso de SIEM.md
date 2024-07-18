## O que é um caso de uso de SIEM?

Resumindo, os casos de uso de SIEM são essenciais para uma estratégia de segurança cibernética robusta, pois permitem a identificação e detecção eficaz de incidentes de segurança. Eles são projetados para ilustrar situações específicas onde um sistema SIEM pode ser aplicado, desde cenários simples como tentativas de login falhadas até situações complexas como a detecção de surtos de ransomware.

![[usecase1.webp]]

Considere que um usuário Bob passa por 10 tentativas de falha de autenticação consecutivas. Pode se originar de um usuário real que esqueceu as credenciais ou de um agente malicioso usando força bruta. Nos dois casos esses eventos são enviados ao SIEM, que os relaciona em um único evento e dispara um alerta para a equipe de SOC na categoria de caso de uso "força bruta".

Com base nos dados de log gerados dentro do SIEM, a equipe do SOC é então responsável por tomar as medidas apropriadas.

## Ciclo de vida de desenvolvimento de casos de uso de SIEM

Os seguintes estágios críticos devem ser considerados ao desenvolver qualquer caso de uso:

![[usecase2.webp]]
Resumindo o processo de desenvolvimento de um caso de uso de SIEM:

1. **Requisitos**: Compreenda o propósito do caso de uso, como detectar um ataque de força bruta após 10 falhas consecutivas de login em 4 minutos.

2. **Pontos de Dados**: Identifique fontes de dados como máquinas Windows, Linux, endpoints, servidores, capturando detalhes como usuário, data e hora.

3. **Validação de Logs**: Verifique se os logs contêm informações essenciais como usuário, data e hora, garantindo que todos os eventos de autenticação sejam registrados.

4. **Design e Implementação**: Defina condições para acionar alertas, como o número de falhas de login e o período de tempo, utilizando agregação e priorização adequadas.

5. **Documentação**: Documente os procedimentos operacionais padrão (SOP) para analistas, incluindo condições, agregações, prioridades e escalonamentos.

6. **Implementação**: Inicie com o desenvolvimento do caso de uso, tratando de falsos positivos antes de mover para o ambiente de produção.

7. **Atualização Periódica/Ajustes**: Obtenha feedback regular para ajustar e otimizar o caso de uso, mantendo as regras de correlação atualizadas.

Este processo garante que o caso de uso de SIEM seja eficaz na detecção e resposta a incidentes de segurança cibernética.

## Como construir casos de uso de SIEM

- Entenda suas necessidades, riscos e estabeleça alertas para monitorar todos os sistemas necessários adequadamente.
    
- Determine a prioridade e o impacto e, em seguida, mapeie o alerta para a cadeia de eliminação ou estrutura MITRE.
    
- Estabeleça o Tempo de Detecção (TTD) e o Tempo de Resposta (TTR) para o alerta para avaliar a eficácia do SIEM e o desempenho dos analistas.
    
- Crie um Procedimento Operacional Padrão (POP) para gerenciar alertas.
    
- Descreva o processo para refinar alertas com base no monitoramento SIEM.
    
- Desenvolva um Plano de Resposta a Incidentes (IRP) para abordar incidentes verdadeiramente positivos.
    
- Defina Acordos de Nível de Serviço (SLAs) e Acordos de Nível Operacional (OLAs) entre equipes para lidar com alertas e seguir o IRP.
    
- Implementar e manter um processo de auditoria para gerenciar alertas e relatórios de incidentes por analistas.
    
- Crie documentação para revisar o status de registro de máquinas ou sistemas, a base para a criação de alertas e sua frequência de acionamento.
    
- Estabeleça um documento de base de conhecimento para informações essenciais e atualizações de ferramentas de gerenciamento de casos.


















































