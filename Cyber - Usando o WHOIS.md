Vamos considerar três cenários para ajudar a ilustrar o valor dos dados WHOIS.

## Cenário 1: Investigação de Phishing

Um gateway de segurança de e-mail sinaliza um e-mail suspeito enviado a vários funcionários de uma empresa. O e-mail alega ser do banco da empresa e pede que os destinatários cliquem em um link para atualizar suas informações de conta. Um analista de segurança investiga o e-mail e começa realizando uma pesquisa WHOIS no domínio vinculado no e-mail.

O registro WHOIS revela o seguinte:

- `Registration Date`:O domínio foi registrado há apenas alguns dias.
- `Registrant`:As informações do registrante ficam ocultas atrás de um serviço de privacidade.
- `Name Servers`: Os servidores de nomes estão associados a um provedor de hospedagem conhecido e à prova de falhas, frequentemente usado para atividades maliciosas.

Essa combinação de fatores levanta bandeiras vermelhas significativas para o analista. A data de registro recente, informações ocultas do registrante e hospedagem suspeita sugerem fortemente uma campanha de phishing. O analista alerta prontamente o departamento de TI da empresa para bloquear o domínio e avisa os funcionários sobre o golpe.

Investigações mais aprofundadas sobre o provedor de hospedagem e os endereços IP associados podem revelar domínios de phishing adicionais ou infraestrutura usada pelo agente da ameaça.

## Cenário 2: Análise de malware

Um pesquisador de segurança está analisando uma nova cepa de malware que infectou vários sistemas dentro de uma rede. O malware se comunica com um servidor remoto para receber comandos e exfiltrar dados roubados. Para obter insights sobre a infraestrutura do agente da ameaça, o pesquisador realiza uma pesquisa WHOIS no domínio associado ao servidor de comando e controle (C2).

O registro WHOIS revela:

- `Registrant`: O domínio é registrado para um indivíduo usando um serviço de e-mail gratuito conhecido pelo anonimato.
- `Location`:O endereço do registrante está em um país com alta prevalência de crimes cibernéticos.
- `Registrar`: O domínio foi registrado por meio de um registrador com um histórico de políticas de abuso negligentes.

Com base nessas informações, o pesquisador conclui que o servidor C2 provavelmente está hospedado em um servidor comprometido ou "à prova de balas". O pesquisador então usa os dados WHOIS para identificar o provedor de hospedagem e notificá-lo sobre a atividade maliciosa.

## Cenário 3: Relatório de inteligência de ameaças

Uma empresa de segurança cibernética rastreia as atividades de um sofisticado grupo de atores de ameaças conhecido por ter como alvo instituições financeiras. Analistas reúnem dados WHOIS em vários domínios associados às campanhas passadas do grupo para compilar um relatório abrangente de inteligência de ameaças.

Ao analisar os registros WHOIS, os analistas descobrem os seguintes padrões:

- `Registration Dates`:Os domínios foram registrados em grupos, geralmente pouco antes de grandes ataques.
- `Registrants`:Os registrantes usam vários pseudônimos e identidades falsas.
- `Name Servers`: Os domínios geralmente compartilham os mesmos servidores de nomes, sugerindo uma infraestrutura comum.
- `Takedown History`:Muitos domínios foram retirados do ar após ataques, indicando intervenções anteriores de autoridades policiais ou de segurança.

Esses insights permitem que analistas criem um perfil detalhado das táticas, técnicas e procedimentos (TTPs) do agente de ameaça. O relatório inclui indicadores de comprometimento (IOCs) com base nos dados WHOIS, que outras organizações podem usar para detectar e bloquear ataques futuros.

## sando WHOIS
Antes de usar o comando `whois`, você precisará garantir que ele esteja instalado no seu sistema Linux. É um utilitário disponível por meio dos gerenciadores de pacotes Linux e, se não estiver instalado, pode ser instalado simplesmente com

```sh
sudo apt install whois -y
```

Vamos executar uma pesquisa WHOIS em `facebook.com`:
```sh
whois facebook.com
```
![[Pasted image 20240816085425.png]]

A saída do WHOIS `facebook.com`revela vários detalhes importantes:

1. `Domain Registration`:
    
    - `Registrar`: RegistrarSafe, LLC
    - `Creation Date`: 1997-03-29
    - `Expiry Date`: 2033-03-30
    
    Esses detalhes indicam que o domínio está registrado na RegistrarSafe, LLC, e está ativo há um período considerável, sugerindo sua legitimidade e presença online estabelecida. A data de expiração distante reforça ainda mais sua longevidade.
    
2. `Domain Owner`:
    
    - `Registrant/Admin/Tech Organization`: Meta Platforms, Inc.
    - `Registrant/Admin/Tech Contact`: Administrador de domínio
    
    Essas informações identificam a Meta Platforms, Inc. como a organização por trás de `facebook.com`, e "Domain Admin" como o ponto de contato para assuntos relacionados ao domínio. Isso é consistente com a expectativa de que o Facebook, uma plataforma de mídia social proeminente, seja de propriedade da Meta Platforms, Inc.
    
3. `Domain Status`:
    
    - `clientDeleteProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`, `serverDeleteProhibited`, `serverTransferProhibited`, e`serverUpdateProhibited`
    
    Esses status indicam que o domínio está protegido contra alterações, transferências ou exclusões não autorizadas tanto no lado do cliente quanto do servidor. Isso destaca uma forte ênfase na segurança e no controle sobre o domínio.
    
4. `Name Servers`:
    
    - `A.NS.FACEBOOK.COM`, `B.NS.FACEBOOK.COM`, `C.NS.FACEBOOK.COM`,`D.NS.FACEBOOK.COM`
    
    Esses servidores de nomes estão todos dentro do `facebook.com`domínio, sugerindo que a Meta Platforms, Inc. gerencia sua infraestrutura de DNS. É uma prática comum para grandes organizações manter o controle e a confiabilidade sobre sua resolução de DNS.

No geral, a saída do WHOIS para `facebook.com` está alinhada com as expectativas de um domínio bem estabelecido e seguro, de propriedade de uma grande organização como a Meta Platforms, Inc.

