As definições de política em objetos de política de grupo (GPOs) definem a configuração. No entanto, você precisa especificar os computadores ou usuários aos quais o GPO se aplica antes que as alterações de configuração em um GPO afetem computadores ou usuários em sua organização. Isso é a criação de escopo de um GPO. O escopo de um GPO é a coleção de usuários e computadores que receberão as configurações no GPO.

>[!important]
>Você cria o escopo de um GPO vinculando-o a uma unidade organizacional que contém os usuários e computadores de destino.

## Criar escopo de um GPO

Você pode usar vários métodos para gerenciar o escopo dos GPOs baseados em domínio. O primeiro é a vinculação do GPO. No Active Directory (AD), você pode vincular GPOs a:
- Sites
- Domínios
- UOs (unidades organizacionais)

O site, o domínio ou a UO se torna o escopo máximo do GPO. Você pode vincular um GPO a mais de um domínio, UO ou site.

>[!danger]
>Vincular GPOs a vários sites em uma floresta com vários domínios pode causar problemas de desempenho, pois os GPOs são armazenados nos controladores de domínio do domínio onde foram criados. Isso pode levar a lentidão na aplicação das políticas, especialmente se os computadores precisarem acessar GPOs através de links WAN (redes de longa distância). Portanto, é recomendado evitar a vinculação de GPOs a vários sites para evitar problemas de desempenho.

Você pode restringir ainda mais o escopo do GPO com um dos dois tipos de filtros mostrados na tabela a seguir.

|**Filter**|**Descrição**|
|---|---|
|Segurança|Especifica grupos de segurança ou objetos de computador ou usuário individuais que se relacionem com um escopo de GPO, mas aos quais o GPO deve ou não ser aplicado explicitamente.|
|WMI|Especifica um escopo usando as características de um sistema, como uma versão do sistema operacional ou espaço livre em disco.|

![[Pasted image 20240917172759.png]]

Use filtros de segurança e filtros WMI para restringir ou especificar o escopo dentro do escopo inicial que a vinculação ao GPO criou. Veja a seguir um exemplo de filtro WMI que resulta em uma lista de computadores que executam o Windows 10.

```powershell
select * from Win32_OperatingSystem where Version like "10.%"
```

## Ordem de processamento do GPO
Os GPOs que se aplicam a um usuário, computador ou ambos não são aplicados ao mesmo tempo. As configurações conflitantes que forem processadas mais tarde poderão substituir as configurações que forem processadas primeiro.

A Política de Grupo obedece à seguinte ordem de processamento hierárquico:
1. GPOs locais.
2. GPOs vinculados ao site.
3. GPOs vinculados ao domínio.
4. GPOs vinculados à unidade organizacional.
5. GPOs vinculados à unidade organizacional acessória.

>[!important]
>Em relação à aplicação da Política de Grupo, a regra padrão é que a última política (a política mais específica) aplicada prevalece.

1. **Ordem de Processamento de GPOs:** Se vários GPOs são aplicados a uma UO, o processamento segue a ordem definida pelo administrador na guia **Objetos de Política de Grupo Vinculados**. A ordem pode ser ajustada conforme necessário.

2. **Desabilitação de GPOs:** É possível desabilitar a vinculação de GPOs a um domínio ou UO para evitar sua aplicação. Isso é útil, por exemplo, para solucionar problemas causados por alterações recentes em um GPO.

3. **Desabilitação de Configurações Específicas:** É possível desabilitar configurações específicas de um GPO (usuário ou computador) se uma seção não for necessária, o que pode melhorar a eficiência do processamento da política. Por exemplo, se uma política apenas define configurações para a área de trabalho do usuário, a seção de configurações do computador pode ser desabilitada.

## Herança de GPO
- **Precedência dos GPOs:** Quando múltiplos GPOs definem a mesma configuração de política, a precedência determina qual configuração será aplicada. GPOs com precedência mais alta (números menores) prevalecem sobre GPOs com precedência mais baixa (números maiores). Por exemplo, um GPO com precedência 1 tem prioridade sobre outros GPOs.
    
- **Herdabilidade e Aplicação das Políticas:** Por padrão, os GPOs vinculados a contêineres de nível superior são herdados por contêineres de nível inferior. A aplicação das políticas ocorre na seguinte ordem:
    - **Site**
    - **Domínio**
    - **Unidades Organizacionais (UOs)**
    
    A sequência de aplicação é site → domínio → UO, criando um efeito cumulativo chamado **herança de política**. O **Conjunto de Políticas Resultante (RSoP)** é a combinação final das políticas aplicadas ao usuário ou computador.

### Bloquear herança
Você pode impedir que configurações de política sejam herdadas por um domínio ou unidade organizacional (UO) configurando o bloqueio de herança. Isso é feito no Console de Gerenciamento de Política de Grupo (GPMC) ao clicar com o botão direito do mouse no domínio ou UO e selecionar **Bloquear Herança**.

![[Pasted image 20240917173543.png]]

A opção Bloquear Herança é uma propriedade de um contêiner e, portanto, bloqueia todas as configurações de Política de Grupo de GPOs que se vinculam aos pais na hierarquia de Política de Grupo.

>[!danger]
>Use a opção Bloquear Herança com moderação, pois o bloqueio de herança dificulta a avaliação de precedência e herança das Políticas de Grupo.

>[!tip]
>Com a filtragem de grupo de segurança, você pode criar cuidadosamente o escopo de um GPO para que ele se aplique apenas aos usuários e computadores corretos, tornando desnecessário o uso da opção Bloquear Herança.

### Impor um link de GPO
Além disso, você pode definir a imposição de uma vinculação de GPO. Para impor uma vinculação de GPO, clique com o botão direito do mouse ou acesse o menu de contexto da vinculação do GPO na árvore de console e selecione **Imposto** no menu de atalho.

![[Pasted image 20240917173727.png]]

Quando você define uma vinculação de GPO como Imposto, o GPO usa o nível mais alto de precedência. As configurações de política nesse GPO prevalecem sobre as configurações de política conflitantes em outros GPOs.

>[!important]
>Uma vinculação imposta se aplica a contêineres filho mesmo quando esses contêineres estão definidos para Bloquear Herança. A opção Imposto faz com que a política se aplique a todos os objetos dentro de seu escopo.

Aqui está o resumo:

A imposição de GPOs é usada para garantir que uma configuração obrigatória definida por políticas corporativas de TI não seja substituída por outros GPOs vinculados a níveis iguais ou inferiores. Para isso, você deve usar a imposição da vinculação do GPO.

### Avaliação de precedência
Para avaliar a precedência dos GPOs, selecione uma UO ou domínio e vá para a guia **Herança de Política de Grupo**. Esta guia mostra a precedência dos GPOs considerando vinculação, ordem de vinculação, bloqueio de heranças e imposição de vinculações.

![[Pasted image 20240917174051.png]]

>[!important]
>Essa guia não leva em conta políticas vinculadas a um site, segurança de GPO ou filtragem WMI.

