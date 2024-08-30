## Montar exportações NFS com o montador automático

O **montador automático** (autofs) é um serviço que monta e desmonta sistemas de arquivos e exportações NFS automaticamente, sob demanda. Isso permite que usuários sem privilégios acessem mídias removíveis e sistemas de arquivos locais ou remotos que não estão montados na inicialização. Enquanto o `/etc/fstab` monta sistemas de arquivos durante o boot e os mantém montados, o autofs monta sistemas de arquivos apenas quando necessário, quando um usuário ou aplicativo acessa o ponto de montagem.

### Benefícios do montador automático
O **montador automático** (autofs) utiliza recursos de maneira semelhante aos sistemas de arquivos montados no boot, já que um sistema de arquivos só consome recursos quando está em uso. No entanto, oferece vantagens adicionais: desmonta sistemas de arquivos quando não estão em uso, protegendo-os contra corrupção inesperada. Ao montar novamente, usa a configuração mais atual, ao contrário de `/etc/fstab`, e pode escolher a conexão mais rápida se houver servidores e caminhos redundantes.

### O método de serviço autofs do montador automático
O serviço **autofs** suporta os mesmos sistemas de arquivos e opções de montagem que o arquivo `/etc/fstab`, incluindo NFS e SMB, e permite restrições de acesso. Ele usa os comandos `mount` e `umount` para gerenciar sistemas de arquivos, que permanecem desmontados até que o ponto de montagem seja acessado. Quando o sistema de arquivos é acessado, é montado automaticamente e permanece montado enquanto estiver em uso. Após um tempo-limite, o montador automático desmonta o sistema de arquivos quando não há arquivos abertos e todos os usuários e processos saíram do diretório.

### Casos de uso de mapas diretos e indiretos
O **montador automático** (autofs) suporta dois tipos de montagens:
1. **Montagem Direta**: O sistema de arquivos é montado em um local de ponto de montagem fixo e conhecido, como diretórios permanentes. Este é o tipo de montagem que a maioria das pessoas configura e utiliza regularmente.
2. **Montagem Indireta**: O local do ponto de montagem não é conhecido até que a demanda de montagem ocorra. Um exemplo é o mapeamento de diretórios pessoais montados remotamente, onde o diretório é criado e montado apenas quando um usuário acessa seu diretório pessoal. O ponto de montagem é criado sob demanda e excluído quando não é mais necessário.

### Configurar o serviço de montador automático
Para usar o serviço `autofs`, você deve instalar os pacotes `autofs` e `nfs-utils`.
```shell-session
[root@host ~]# dnf install autofs nfs-utils
```

### O arquivo de mapa mestre
O arquivo de mapa mestre (`/etc/auto.master`) é o arquivo de configuração padrão para o serviço `autofs`. Você pode usar o arquivo `/etc/autofs.conf` para alterar o arquivo de mapa mestre para o serviço `autofs`. Use o diretório `/etc/auto.master.d` para configurar o arquivo de mapa mestre. Esse arquivo identifica o diretório-base dos pontos de montagem e identifica o arquivo de mapeamento para criar as montagens automáticas.

O nome do arquivo do mapa mestre é arbitrário (embora normalmente significativo), mas deve ter uma extensão de `.autofs` para que o subsistema o reconheça. Você pode colocar várias entradas em um único arquivo de mapa mestre; como alternativa, você pode criar vários arquivos de mapa mestre, cada um com suas próprias entradas agrupadas logicamente.

O mapa mestre usa o seguinte formato para montagem automática:
```shell-session
[root@host ~]# cat /etc/auto.master.d/demo.autofs
```
![[Pasted image 20240830172646.png]]

Substitua a variável _``mount-point``_ pelo diretório a ser usado como ponto de montagem base para montagens automáticas.

Substitua a variável ``_`map-file`_`` pelo arquivo a ser usado como arquivo de mapa. O arquivo de mapa deve ser criado antes de o serviço `autofs` ser iniciado.

### O arquivo de mapa
Os arquivos de mapa configuram as propriedades de pontos de montagem individuais conforme a necessidade.

O montador automático criará os diretórios se eles não existirem. Se os diretórios já existirem antes de o montador automático ser iniciado, ele não os removerá ao sair. Se um tempo limite for especificado, o diretório será automaticamente desmontado se o diretório não for acessado durante o período de tempo limite.

Se você usar um único nome de diretório para o ponto de montagem, o diretório será montado como uma montagem indireta. Se você usar o caminho completo para o ponto de montagem, o diretório será montado como uma montagem indireta.

O arquivo de mapa usa o seguinte formato para montagem automática:
```shell-session
[root@host ~]# cat /etc/auto.demo
```
![[Pasted image 20240830172856.png]]

Substitua a variável _`mount-point`_ pelo ponto de montagem `autofs`.
Substitua a variável _`mount-options`_ com as opções a serem usadas para montar o ponto de montagem `autofs`.
Substitua a variável _`source-location`_ pelo local de origem da montagem.

#### Criar um arquivo de mapa indireto
Cada arquivo de mapeamento identifica o ponto de montagem, as opções de montagem e o local de origem para a montagem para um conjunto de montagens automáticas.

Para montagem automática indireta, inclua o seguinte conteúdo no arquivo de mapa mestre:
```shell-session
[root@host ~]# cat /etc/auto.master.d/indirect.autofs
```
![[Pasted image 20240830173303.png]]

Essa entrada usa o diretório `/shares` como o ponto de montagem de base para a montagem automática indireta. O arquivo de mapa `/etc/auto.indirect` contém os detalhes da montagem.

O seguinte exemplo mostra uma entrada de amostra no arquivo de mapa:
```shell-session
[root@host ~]# cat /etc/auto.indirect
```
![[Pasted image 20240830173343.png]]

A convenção de nomes de um arquivo de mapa é `/etc/auto.`_`name`_, em que _name_ reflete o conteúdo do mapa.

Claro! Aqui está um resumo mais detalhado do texto:

---

O **montador automático (autofs)** permite que você configure pontos de montagem para sistemas de arquivos com nomes arbitrários, sem impor uma estrutura específica de diretórios no cliente. O ponto de montagem local pode espelhar a estrutura de diretórios do servidor ou ter um nome completamente diferente, dependendo das necessidades.

### Opções de Montagem

- **Formato das Opções:** As opções de montagem começam com um hífen (-) e são separadas por vírgulas sem espaços em branco. Por exemplo:
  - `-rw` (acesso de leitura/gravação)
  - `-sync` (sincronização imediata durante operações de gravação)
- **Opções Específicas para Montador Automático:**
  - `-fstype=`: Especifica o tipo de sistema de arquivos, como `nfs4` ou `xfs`.
  - `-strict`: Trata erros como fatais ao tentar montar sistemas de arquivos, garantindo que qualquer problema durante a montagem resulte em uma falha.

### Montagem Direta e Indireta

- **Montagem Direta:** O ponto de montagem é conhecido e permanente, semelhante às montagens configuradas manualmente. O diretório do ponto de montagem é fixo e conhecido antecipadamente.
- **Montagem Indireta:** O ponto de montagem é criado dinamicamente conforme a demanda. Por exemplo, diretórios pessoais montados remotamente, onde o ponto de montagem não é conhecido até que um usuário específico solicite o acesso.

### Configuração de Exportações NFS

- **Formato do Local de Origem:** O local de origem para exportações NFS é especificado como `host:/pathname`. Por exemplo:
  - `hosta:/shares/work`
- **Permissões e Acesso:**
  - O servidor NFS deve exportar o diretório com as permissões apropriadas (leitura/gravação ou somente leitura).
  - Mesmo que o cliente solicite acesso de leitura/gravação, se o servidor exportar o diretório somente com leitura, o cliente receberá acesso somente leitura.

---

#### Criar um arquivo de mapa direto

Um arquivo de mapa direto mapeia uma exportação NFS para um ponto de montagem de caminho absoluto. Apenas um arquivo de mapa direto é necessário e pode conter qualquer número de mapas diretos.

Para usar pontos de montagem mapeados diretamente, inclua o seguinte conteúdo no arquivo de mapa mestre:

```shell-session
[root@host ~]# cat /etc/direct.autofs
```
![[Pasted image 20240830174313.png]]
A convenção de nomes de um arquivo de mapa é ``/etc/auto._`name`_``, em que _name_ reflete o conteúdo do mapa.

Todas as entradas de mapa diretas usam o caminho `/-` como o diretório-base. Nesse caso, o arquivo de mapeamento `/etc/auto.direct` contém os detalhes da montagem.

O seguinte exemplo mostra uma entrada de amostra no arquivo de mapa:

```shell-session
[root@host ~]# cat /etc/auto.direct
```
![[Pasted image 20240830174401.png]]

O ponto de montagem (ou chave) é sempre um caminho absoluto. O restante do arquivo de mapeamento usa a mesma estrutura.

Neste exemplo, o diretório `/mnt` existe e não é gerenciado pelo serviço `autofs`. O serviço `autofs` cria e remove automaticamente o diretório `/mnt/docs` completo.

#### Uso de curingas em arquivos de mapa
Quando um servidor NFS exporta vários subdiretórios dentro de um diretório, o montador automático poderá ser configurado para acessar qualquer um desses subdiretórios com uma única entrada de mapeamento.

Ainda sobre o exemplo anterior, se a exportação `hosta:/shares` tiver dois ou mais subdiretórios e se eles puderem ser acessados com as mesmas opções de montagem, então a entrada do arquivo `/etc/auto.indirect` poderá ter a seguinte aparência:

```shell-session
[root@host ~]# cat /etc/auto.indirect
```
![[Pasted image 20240830174957.png]]

O ponto de montagem (ou chave) é um asterisco (\*) e o subdiretório no local de origem é um e comercial (&). Tudo mais na entrada é igual.

Quando um usuário tentar acessar `/shares/work`, a chave `*` (que é `work`, neste exemplo) substitui o "e" comercial no local de origem e `hosta:/shares/work` é montado. Como no exemplo de arquivo de mapa indireto, o serviço `autofs` cria e remove automaticamente o diretório `work`.

## Iniciar o serviço do montador automático
Depois de configurar o mapa mestre e os arquivos de mapa, use o comando `systemctl` para iniciar e habilitar o serviço `autofs`.

```shell-session
[root@host ~]# systemctl enable --now autofs
```
![[Pasted image 20240830175151.png]]

## O método de montagem automática alternativo
O daemon `systemd` pode criar automaticamente arquivos de unidade para entradas no arquivo `/etc/fstab` que incluem a opção `x-systemd.automount`. Use o comando `systemctl daemon-reload` depois de modificar as opções de montagem de uma entrada para gerar um novo arquivo de unidade. Em seguida, use o comando ``systemctl start _`unit`_.automount`` para habilitar a configuração de montagem automática.

A nomenclatura da unidade é baseada no local de montagem dela. Por exemplo, se o ponto de montagem for `/remote/finance`, o arquivo de unidade será chamado de `remote-finance.automount`. O daemon `systemd` monta o sistema de arquivos quando o diretório `/remote/finance` é acessado inicialmente.

Esse método pode ser mais simples do que instalar e configurar o serviço `autofs`. No entanto, uma unidade `systemd.automount` só pode dar suporte a pontos de montagem de caminho absoluto, semelhante aos mapas diretos `autofs`.


