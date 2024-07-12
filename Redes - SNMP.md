[[Cyber - SNMP]]
[[Linux - SNMP]]

---

## SNMP

Simple Network Management Protocol (SNMP) foi criado para monitorar e gerenciar dispositivos de rede, como roteadores, switches, servidores e dispositivos IoT. Além do monitoramento, SNMP permite a configuração e alteração remota de dispositivos. A versão atual, SNMPv3, melhora a segurança, mas também aumenta a complexidade do protocolo.

SNMP utiliza a porta UDP 161 para trocar informações e comandos de controle entre agentes. O cliente pode definir valores e alterar configurações no dispositivo. Além disso, SNMP permite o uso de "traps" na porta UDP 162, onde o servidor envia dados não solicitados ao cliente quando eventos específicos ocorrem.

Para uma troca eficaz de informações, os objetos SNMP devem ter endereços únicos conhecidos por ambos os lados, garantindo uma transmissão de dados e monitoramento de rede bem-sucedidos.

### MIB

Para garantir a compatibilidade do acesso SNMP entre diferentes fabricantes e combinações cliente-servidor, foi criado o Management Information Base (MIB). MIB é um formato independente para armazenar informações de dispositivos em um arquivo de texto, onde todos os objetos SNMP consultáveis são listados em uma hierarquia de árvore padronizada.

Cada MIB contém pelo menos um Object Identifier (OID), que inclui um endereço exclusivo, nome, tipo, direitos de acesso e descrição do objeto. Escrito no formato de texto ASCII baseado em Abstract Syntax Notation One (ASN.1), os MIBs não contêm dados, mas descrevem onde encontrar informações, como elas se parecem, quais são os valores de retorno para o OID específico e o tipo de dados usado.

### OID

Um OID representa um nó em um namespace hierárquico. Uma sequência de números identifica exclusivamente cada nó, permitindo que a posição do nó na árvore seja determinada. Quanto maior a cadeia, mais específica a informação. Muitos nós na árvore OID não contêm nada, exceto referências àqueles abaixo deles. Os OIDs consistem em inteiros e geralmente são concatenados por notação de ponto. Podemos procurar muitos MIBs para os OIDs associados no [Object Identifier Registry](https://www.alvestrand.no/objectid/) .

### SNMPv1

O SNMPv1 (Simple Network Management Protocol version 1) é uma versão inicial do protocolo utilizado para gerenciamento e monitoramento de redes. Aqui está um resumo das principais características e limitações do SNMPv1:

1. **Finalidade**: SNMPv1 é usado para recuperação de informações de dispositivos de rede, configuração de dispositivos e envio de traps (notificações de eventos).

2. **Ausência de Autenticação**: SNMPv1 não possui mecanismos embutidos de autenticação. Isso significa que os dados transmitidos podem ser lidos e até mesmo modificados por qualquer pessoa que acesse a rede, sem restrições significativas.

3. **Falta de Criptografia**: Outra limitação crítica do SNMPv1 é a falta de suporte a criptografia. Todos os dados são transmitidos em texto simples, o que os torna vulneráveis a interceptações. Isso é especialmente preocupante em redes onde a segurança dos dados é essencial.

4. **Uso Atual**: Apesar de suas limitações de segurança, o SNMPv1 ainda é utilizado em muitas redes pequenas e em alguns casos específicos onde a segurança não é uma preocupação primária.

Em resumo, enquanto o SNMPv1 oferece funcionalidades básicas para gerenciamento de rede, como recuperação de informações e notificações de eventos, suas falhas significativas em autenticação e criptografia o tornam inadequado para ambientes onde a segurança dos dados é uma prioridade.

### SNMPv2

O SNMPv2 existia em diferentes versões. A versão que ainda existe hoje é `v2c`, e a extensão `c`significa SNMP baseado em comunidade. Em relação à segurança, o SNMPv2 está no mesmo nível do SNMPv1 e foi estendido com funções adicionais do SNMP baseado em parte que não está mais em uso. No entanto, um problema significativo com a execução inicial do protocolo SNMP é que o `community string`que fornece segurança é transmitido apenas em texto simples, o que significa que não tem criptografia interna.

### SNMPv3

A segurança foi aumentada enormemente por `SNMPv3`recursos de segurança como `authentication`uso de nome de usuário e senha e transmissão `encryption`(via `pre-shared key`) dos dados. No entanto, a complexidade também aumenta na mesma extensão, com significativamente mais opções de configuração do que `v2c`.

### Community Strings

As strings da comunidade no SNMP são como senhas usadas para determinar se informações solicitadas podem ser acessadas. Muitas organizações ainda usam SNMPv2 devido à complexidade da transição para SNMPv3, mantendo os serviços ativos. Isso preocupa administradores, pois falta conhecimento sobre como informações podem ser obtidas e usadas por invasores. Além disso, a falta de criptografia nas strings da comunidade permite que sejam interceptadas e lidas durante o envio pela rede, ampliando as preocupações com segurança.

---
## Referências
**`HTB`** - https://academy.hackthebox.com/module/112/section/1075
**`Manpage`** - http://www.net-snmp.org/docs/man/snmpd.conf.html









































































































































































































































