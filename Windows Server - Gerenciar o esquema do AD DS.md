## O que é um esquema?
O Active Directory Domain Services (AD DS) armazena e recupera dados de diferentes aplicativos e serviços padronizando a forma como esses dados são armazenados. O **esquema do AD DS** define todas as classes de objetos e seus atributos, determinando como as informações são organizadas no diretório.

Cada domínio em uma floresta possui uma cópia do esquema, e qualquer alteração feita no esquema é replicada para todos os controladores de domínio da floresta. No entanto, essas alterações só podem ser iniciadas pelo **mestre de esquema**.

### Objetos
O Active Directory Domain Services (AD DS) utiliza objetos como unidades de armazenamento, com o esquema definindo os tipos de objetos. Sempre que o AD DS gerencia dados, ele consulta o esquema para determinar a definição apropriada do objeto, que especifica os tipos e a sintaxe dos dados que podem ser armazenados. Apenas objetos definidos pelo esquema podem ser criados.

Como o AD DS utiliza um formato rígido para armazenar dados, ele é capaz de armazenar, recuperar e validar essas informações de maneira consistente, independentemente do aplicativo que as fornece.

### Relações entre objetos, regras, atributos e classes

Os objetos no esquema do Active Directory Domain Services (AD DS) são compostos por atributos organizados em classes. Cada classe tem regras que determinam quais atributos são obrigatórios e quais são opcionais. Por exemplo, a classe de usuário possui mais de 400 atributos, como *givenName* e *displayName*, sendo que *cn* e *objectSID* são obrigatórios.

A classe de usuário é uma **classe estrutural**, o único tipo de classe que pode ter objetos no AD DS. Para modificar o esquema, é possível criar uma **classe auxiliar** com seus próprios atributos e referenciá-la em uma classe estrutural.

![[Pasted image 20241001182836.png]]

## Gerenciar o esquema do AD DS
Ao gerenciar o esquema do AD DS, você poderá modificá-lo somente se for membro do grupo de administradores de esquema no domínio raiz da floresta do AD DS. Para isso, use o snap-in de esquema do Active Directory.

Você deve alterar o esquema somente quando necessário, pois ele controla o armazenamento de informações. Além disso, todas as alterações feitas no esquema afetam cada controlador de domínio. 

Antes de alterar o esquema, revise as alterações e as implemente apenas depois de realizar testes. Isso ajudará a garantir que elas não afetem negativamente o restante da floresta ou quaisquer aplicativos que usam o AD DS.

![[Pasted image 20241001183043.png]]