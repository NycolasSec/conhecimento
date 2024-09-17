O **Active Directory Domain Services (AD DS)** utiliza um processo de **vários mestres** para replicar dados entre controladores de domínio e aplica automaticamente um algoritmo para resolver conflitos de atualizações simultâneas. Esse modelo distribuído permite que vários usuários e aplicativos façam alterações em objetos do AD DS em diferentes controladores de domínio simultaneamente, o que é essencial para ambientes com dois ou mais controladores de domínio, especialmente em grandes e distribuídos como o da Contoso. No entanto, algumas operações específicas só podem ser realizadas por funções específicas em determinados controladores de domínio.

## O que são mestres de operações do AD DS?

As **funções de mestre de operações** do **AD DS** são responsáveis por tarefas que não são adequadas para um modelo de vários mestres e são conhecidas como **FSMO (Flexible Single Master Operation)**. Existem cinco funções de mestre de operações:

1. **Mestre de esquema**
2. **Mestre em nomenclatura de domínio**
3. **Mestre de infraestrutura**
4. **Mestre RID**
5. **Mestre emulador PDC**

Por padrão, o primeiro controlador de domínio instalado em uma floresta hospeda todas as cinco funções. No entanto, essas funções podem ser transferidas para outros controladores de domínio conforme necessário. Cada floresta possui um mestre de esquema e um mestre de nomenclatura de domínio, enquanto cada domínio tem um mestre RID, um mestre de infraestrutura e um emulador PDC. Essas funções podem ser concentradas em um único controlador de domínio ou distribuídas entre vários.

### Mestres de operações florestais
Uma floresta tem as seguintes funções de mestre de operações:

- Mestre de nomenclatura de domínio. Este é o controlador de domínio que você deve contatar quando adicionar ou remover um domínio ou fizer alterações no nome do domínio.

>[!info]
>Se o mestre de nomenclatura de domínio não estiver disponível, você não poderá adicionar domínios à floresta.

- Schema master. Este é o controlador de domínio no qual você faz todas as alterações de schema.

>[!info]
>Se o mestre do esquema não estiver disponível, você não poderá fazer alterações no esquema.

>[!important]
>O comando Get-ADForest do Windows PowerShell, do módulo Active Directory para Windows PowerShell, exibe as propriedades da floresta, incluindo o mestre de nomenclatura de domínio atual e o mestre de esquema.

### Mestres de operações de domínio
Um domínio tem as seguintes funções de mestre de operações:

- O **Mestre RID** no **AD DS** é responsável por garantir que cada entidade de segurança (como usuários, computadores ou grupos) receba um **ID de segurança (SID)** único. Para evitar a duplicação de SIDs entre controladores de domínio, o mestre RID aloca blocos de **RIDs** (Relative Identifiers) para cada controlador de domínio dentro do domínio, que são usados na criação de SIDs.

>[!important]
>Se o mestre RID não estiver disponível, você poderá ter dificuldades para adicionar entidades de segurança ao domínio. Além disso, como os controladores de domínio usam seus RIDs existentes, eles eventualmente ficam sem eles e não conseguem criar novos objetos.

- O **Mestre de Infraestrutura** no **AD DS** gerencia as referências de objetos entre domínios, como quando um grupo em um domínio possui membros de outro domínio. Essa função garante a integridade dessas referências. Por exemplo, ao revisar a guia Segurança de um objeto, o sistema traduz os SIDs listados em nomes. Em uma floresta com múltiplos domínios, o mestre de infraestrutura atualiza as referências de SIDs de outros domínios com os nomes principais de segurança correspondentes.

>[!importante]
>Se o mestre de infraestrutura não estiver disponível, os controladores de domínio que não forem catálogos globais não poderão executar a tradução dos nomes principais de segurança dos SIDs.

>[!importante]
>A função de mestre de infraestrutura não deve residir no controlador de domínio que está hospedando a função de catálogo global, a menos que cada controlador de domínio na floresta esteja configurado para servir como um catálogo global. Nesse caso, a função de mestre de infraestrutura não é necessária porque cada controlador de domínio conhece cada objeto na floresta.

O **Mestre Emulador PDC** no **AD DS** desempenha várias funções importantes:

- **Fonte de Tempo**: Serve como a principal fonte de sincronização de tempo para todos os controladores de domínio no domínio.
- **Sincronização de Tempo**: Sincroniza seu tempo com o mestre emulador PDC no domínio raiz da floresta, que por sua vez sincroniza com uma fonte de tempo externa confiável.
- **Alterações de GPOs**: As alterações em Objetos de Política de Grupo (GPOs) são gravadas pelo mestre emulador PDC por padrão.
- **Alterações de Senha**: Recebe imediatamente alterações de senha e é o controlador de domínio que verifica as senhas dos usuários, mesmo se um controlador de domínio em outro local ainda não tiver recebido as novas informações de senha.

>[!important]
>Se o mestre do emulador PDC não estiver disponível, os usuários poderão ter problemas para fazer login até que suas alterações de senha sejam replicadas para todos os controladores de domínio.

>[!info]
>O comando Get-ADDomain do Windows PowerShell, do módulo do Active Directory para Windows PowerShell, exibe as propriedades do domínio, incluindo o mestre RID atual, o mestre de infraestrutura e o mestre do emulador PDC.

## Gerenciar mestres de operações do AD DS
Em um ambiente **AD DS** onde as funções de mestre de operações estão distribuídas entre controladores de domínio, pode ser necessário mover uma função de um controlador de domínio para outro. Isso pode ser feito de duas maneiras:

- **Transferência da Função**: Quando a movimentação é planejada e realizada entre dois controladores de domínio on-line. Durante a transferência, os dados mais recentes do controlador de domínio que detém a função são replicados para o novo servidor.
- **Apreensão da Função**: Em situações de emergência, quando o controlador de domínio atual da função não está disponível.

>[!important]
>Você deve assumir uma função apenas como último recurso, quando não há chance de recuperar o atual titular da função.

### Funções de mestre de operações de transferência
Você pode transferir funções de mestre de operações usando os snap-ins do AD DS listados na tabela a seguir.

|**Papel**|**Encaixe**|
|---|---|
|Mestre de esquema|Esquema do Active Directory|
|Mestre em nomenclatura de domínio|Domínios e relações de confiança do Active Directory|
|Mestre de infraestrutura|Usuários e computadores do Active Directory|
|Mestre RID|Usuários e computadores do Active Directory|
|Mestre emulador PDC|Usuários e computadores do Active Directory|

Você não pode usar snap-ins do AD DS para capturar funções de mestre de operações. Em vez disso, você deve usar a ferramenta de linha de comando **ntdsutil.exe ou o Windows PowerShell para capturar funções.**

>[!info]
>Você também pode usar essas ferramentas para transferir funções.

A sintaxe para transferir uma função e assumir uma função é semelhante no Windows PowerShell, como demonstra a seguinte linha de sintaxe:

```powershell
Move-ADDirectoryServerOperationsMasterRole -Identity "<servername>" -OperationsMasterRole "<rolenamelist>" -Force
```

Para a sintaxe anterior, as definições dignas de nota são as seguintes:
- **servername** : O nome do controlador de domínio de destino para o qual você está transferindo uma ou mais funções.
- **rolenamelist** : uma lista separada por vírgulas de nomes de funções do AD DS a serem movidas para o servidor de destino.
- **-Force** : Um parâmetro opcional que você inclui para assumir uma função em vez de transferi-la.

### Exercício
1. Crie um ambiente AD DS. Crie uma floresta AD DS de domínio único contendo dois controladores de domínio.
2. Verifique o posicionamento das funções de mestre de operações. Identifique qual dos dois controladores de domínio hospeda as funções de mestre de operações.
3. Transfira funções de mestre de operações entre controladores de domínio usando as ferramentas de GUI. Use as ferramentas de GUI para transferir as funções de mestre de operações do controlador de domínio que você identificou na etapa anterior para o outro.
4. Transfira funções de mestre de operações entre controladores de domínio usando ferramentas de linha de comando. Use as ferramentas de linha de comando para transferir as funções de mestre de operações de volta para o primeiro controlador de domínio.








