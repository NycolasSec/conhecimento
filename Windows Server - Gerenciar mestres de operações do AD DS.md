O AD DS usa um processo de vários mestres para copiar dados entre controladores de domínio e implementa automaticamente um algoritmo de resolução de conflitos que corrige atualizações simultâneas e conflitantes.

Essas provisões permitem um modelo de gerenciamento distribuído, em que vários usuários e aplicativos podem aplicar alterações simultaneamente a objetos do AD DS em diferentes controladores de domínio.

Esse modelo é necessário para dar suporte a qualquer ambiente do AD DS com dois ou mais controladores de domínio. Apesar disso, determinadas operações podem ser executadas apenas por uma função específica, em um controlador de domínio específico.

## O que são os mestres de operações do AD DS?

As funções de mestre de operações do AD DS são responsáveis por executar operações não adequadas para um modelo de vários mestres. Um controlador de domínio com uma dessas funções é um mestre de operações. Uma função de mestre de operações também é conhecida como uma função de _operação de mestre único (FSMO) flexível_. Há cinco funções de mestre de operações:

- Mestre de esquema
- Mestre de nomeação de domínio
- Mestre de infraestrutura
- Mestre de RID do
- Mestre emulador PDC

Por padrão, o primeiro controlador de domínio instalado em uma floresta hospeda todas as cinco funções. No entanto, elas podem ser transferidas depois da implantação de controladores de domínio adicionais. Ao executar alterações específicas do mestre de operações, você deve se conectar ao controlador de domínio com a função. As cinco funções de mestre de operações têm a seguinte distribuição:

- Cada floresta conta com um mestre de esquema e um de nomeação de domínio.
- Cada domínio do AD DS tem um mestre de ID relativo (RID), um mestre de infraestrutura e um emulador de controlador de domínio primário (PDC).

Você pode posicionar todas as cinco em um único controlador de domínio ou distribuí-las entre vários.

### Mestres de operações de floresta
Uma floresta tem as seguintes funções de mestre de operações:

- **Mestre de nomeação de domínio**: É responsável por gerenciar a adição e remoção de domínios e por fazer alterações de nome de domínio.
- **Mestre de esquema**: É responsável por controlar todas as atualizações e modificações no esquema do AD em uma floresta. Ele é o único controlador de domínio que pode realizar alterações no esquema do AD, como adicionar novos atributos ou classes de objetos.

### Mestres de operações de domínio
Um domínio tem as seguintes funções de mestre de operações:

1. **Mestre RID**: Garante que cada controlador de domínio atribua SIDs exclusivos aos objetos, alocando blocos de RIDs (Relative Identifiers) para evitar duplicação de SIDs entre controladores.
   
2. **Mestre de infraestrutura**: Gerencia referências entre domínios, como a relação entre grupos e membros de domínios diferentes, atualizando SIDs com seus respectivos nomes.

3. **Mestre emulador PDC**: Atua como a fonte de tempo do domínio, sincronizando-se com o PDC do domínio raiz da floresta e, em última instância, com uma fonte externa. Ele também recebe alterações urgentes de senha e registra mudanças nos GPOs.

Essas funções ajudam a manter a integridade e a sincronização do AD DS em ambientes de vários domínios.

### Transferir funções de mestre de operações
Você pode transferir funções de mestre de operações usando os snap-ins do AD DS listados na tabela a seguir.

**Mestre de esquema**
Esquema do Active Directory

**Mestre de nomeação de domínio**
Domínios e Relações de Confiança do Active Directory

**Mestre de infraestrutura**
Usuários e computadores do Active Directory

**Mestre de RID do**
Usuários e computadores do Active Directory

**Mestre emulador PDC**
Usuários e computadores do Active Directory

### Capturar funções de mestre de operações
Não é possível usar os snap-ins do AD DS para capturar funções de mestre de operações. Em vez disso, você deve usar a ferramenta de linha de comando **ntdsutil.exe** ou o Windows PowerShell para capturar funções.

A sintaxe é semelhante para transferir e para capturar uma função no Windows PowerShell, como demonstra a seguinte linha de sintaxe:

```powershell
Move-ADDirectoryServerOperationsMasterRole -Identity "<servername>" -OperationsMasterRole "<rolenamelist>" -Force
```

































