Muitas variantes de malware escritas para Windows podem se espalhar pela rede por meio de compartilhamentos de rede com permissões brandas aplicadas. A infame vulnerabilidade `EternalBlue` ainda assombra sistemas Windows sem patches em execução `SMBv1` e frequentemente abre caminho para que o ransomware feche organizações.

O `Server Message Block protocol`( `SMB`) é usado no Windows para conectar recursos compartilhados como arquivos e impressoras.

Ele é usado em ambientes grandes, médios e pequenos.

![[smb_diagram.webp]]

Permissões NTFS e Permissões de Compartilhamento são frequentemente entendidas como as mesmas, mas não são.

Algumas permissões individuais que podem ser definidas para proteger/conceder aos objetos acesso a um compartilhamento de rede hospedado em um sistema operacional Windows executando o sistema de arquivos NTFS.

#### Permissões de compartilhamento

| Permissão      | Descrição                                                                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Full Control` | Os usuários têm permissão para executar todas as ações fornecidas pelas permissões Alterar e Ler, bem como alterar permissões para arquivos NTFS e subpastas |
| `Change`       | Os usuários têm permissão para ler, editar, excluir e adicionar arquivos e subpastas                                                                         |
| `Read`         | Os usuários têm permissão para visualizar o conteúdo dos arquivos e subpastas                                                                                |

#### Permissões básicas do NTFS

| Permissão              | Descrição                                                                                                                                                           |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Full Control`         | Os usuários têm permissão para adicionar, editar, mover, excluir arquivos e pastas, bem como alterar as permissões NTFS que se aplicam a todas as pastas permitidas |
| `Modify`               | Os usuários têm permissão ou não para visualizar e modificar arquivos e pastas. Isso inclui adicionar ou excluir arquivos                                           |
| `Read & Execute`       | Os usuários têm permissão ou não para ler o conteúdo dos arquivos e executar programas                                                                              |
| `List folder contents` | Os usuários têm permissão ou não para visualizar uma lista de arquivos e subpastas                                                                                  |
| `Read`                 | Os usuários têm permissão ou não para ler o conteúdo dos arquivos                                                                                                   |
| `Write`                | Os usuários têm permissão ou não para gravar alterações em um arquivo e adicionar novos arquivos a uma pasta                                                        |
| `Special Permissions`  | Uma variedade de opções de permissões avançadas                                                                                                                     |

#### Permissões especiais NTFS

| Permissão                        | Descrição                                                                                                                                                                                                                                         |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Full control`                   | Os usuários têm permissão ou não para adicionar, editar, mover, excluir arquivos e pastas, bem como alterar as permissões NTFS que se aplicam a todas as pastas permitidas.                                                                       |
| `Traverse folder / execute file` | Os usuários têm permissão ou não para acessar uma subpasta dentro de uma estrutura de diretório, mesmo que o usuário tenha acesso negado ao conteúdo no nível da pasta pai. Os usuários também podem ter permissão ou não para executar programas |
| `List folder/read data`          | Os usuários têm permissão ou não para visualizar arquivos e pastas contidos na pasta pai. Os usuários também podem ter permissão para abrir e visualizar arquivos                                                                                 |
| `Read attributes`                | Os usuários têm permissão ou não para visualizar atributos básicos de um arquivo ou pasta. Exemplos de atributos básicos: sistema, arquivo, somente leitura e oculto                                                                              |
| `Read extended attributes`       | Os usuários têm permissão ou não para visualizar atributos estendidos de um arquivo ou pasta. Os atributos diferem dependendo do programa                                                                                                         |
| `Create files/write data`        | Os usuários têm permissão ou não para criar arquivos dentro de uma pasta e fazer alterações em um arquivo                                                                                                                                         |
| `Create folders/append data`     | Os usuários têm permissão ou não para criar subpastas dentro de uma pasta. Os dados podem ser adicionados aos arquivos, mas o conteúdo pré-existente não pode ser substituído                                                                     |
| `Write attributes`               | Os usuários têm permissão ou não para alterar atributos de arquivo. Esta permissão não concede acesso à criação de arquivos ou pastas                                                                                                             |
| `Write extended attributes`      | Os usuários têm permissão ou não para alterar atributos estendidos em um arquivo ou pasta. Os atributos diferem dependendo do programa                                                                                                            |
| `Delete subfolders and files`    | Os usuários têm permissão ou não para excluir subpastas e arquivos. As pastas pai não serão excluídas                                                                                                                                             |
| `Delete`                         | Os usuários têm permissão ou não para excluir pastas pai, subpastas e arquivos.                                                                                                                                                                   |
| `Read permissions`               | Os usuários têm permissão ou não para ler permissões de uma pasta                                                                                                                                                                                 |
| `Change permissions`             | Os usuários têm permissão ou não para alterar as permissões de um arquivo ou pasta                                                                                                                                                                |
| `Take ownership`                 | Os usuários têm permissão ou não para assumir a propriedade de um arquivo ou pasta. O proprietário de um arquivo tem permissões totais para alterar quaisquer permissões                                                                          |

Permissões NTFS se aplicam ao sistema onde a pasta e os arquivos estão hospedados. Pastas criadas em NTFS herdam permissões de pastas pai por padrão. Pode-se desabilitar a herança para definir permissões personalizadas em pastas e subpastas.

As permissões de compartilhamento se aplicam quando a pasta está sendo acessada por meio de SMB, normalmente de um sistema diferente na rede.

Alguém logado localmente na máquina ou via RDP pode acessar a pasta e os arquivos compartilhados simplesmente navegando até o local no sistema de arquivos e precisa considerar apenas as permissões NTFS.

As permissões no nível NTFS fornecem aos administradores um controle muito mais granular sobre o que os usuários podem fazer em uma pasta ou arquivo.

## Considerações sobre o Firewall do Windows Defender

O Firewall do Windows Defender pode em potencial bloquear o acesso ao compartilhamento SMB. Quando um sistema Windows faz parte de um grupo de trabalho, todas as requisições `net logon` são autenticadas no banco de dados `SAM` desse sistema Windows específico. Quando um sistema Windows é unido a um ambiente de Domínio do Windows, todas as solicitações de netlogon são autenticadas no ``Active Directory``. 

A principal diferença entre um grupo de trabalho e um Domínio do Windows é que com um grupo de trabalho, o banco de dados SAM local é usado e, em um Domínio do Windows, um banco de dados centralizado baseado em rede (Active Directory) é usado.

Como a maioria dos firewalls, o Firewall do Windows Defender permite ou nega tráfego (solicitações de acesso e conexão neste caso) fluindo `inbound`e/ou`outbound`

As diferentes regras de entrada e saída estão associadas aos diferentes perfis de firewall no Defender.

Perfis do Firewall do Windows Defender:

- `Public`
- `Private`
- `Domain`

É uma prática recomendada habilitar regras predefinidas ou adicionar exceções personalizadas em vez de desativar o firewall completamente.

### Permissões NTFS ACL (guia Segurança)

![[ntfs.webp]]

Sempre que vemos uma marca de seleção cinza ao lado de uma permissão, ela foi herdada de um diretório pai. Por padrão, todas as permissões NTFS são herdadas do diretório pai. Em muitos casos, os administradores de uma organização seriam responsáveis por decidir quais permissões um usuário ou grupo de usuários obtém aos recursos de rede.

### Montagem no Share
```bash
sudo apt install cifs-utils

sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```












































































































