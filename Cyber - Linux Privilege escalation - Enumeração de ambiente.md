Enumeração é a chave para a escalada de privilégios. Vários scripts auxiliares (como [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) e [LinEnum](https://github.com/rebootuser/LinEnum) existem para auxiliar na enumeração. Ainda assim, também é importante entender quais informações procurar e ser capaz de executar sua enumeração manualmente.

**Versão do SO (OS Version):**
    - Identifique a distribuição (Ubuntu, Debian, FreeBSD, etc.) e a versão do sistema operacional.
    - Isso ajuda a descobrir ferramentas disponíveis e exploits públicos.

**Versão do Kernel (Kernel Version):**
    - Exploits podem existir para versões específicas do kernel.
    - Exploits do kernel podem causar instabilidade ou falhas completas, então entenda bem o exploit e suas consequências antes de executá-lo.

**Serviços em Execução (Running Services):**
    - Identifique quais serviços estão sendo executados, especialmente os que são executados como root.
    - Serviços mal configurados ou vulneráveis como root podem facilitar a escalada de privilégios.
    - Exemplos incluem serviços como Nagios, Exim, Samba e ProFTPd, com PoCs públicas como CVE-2016-9566 no Nagios Core < 4.2.4.

## Obtendo Consciência Situacional
 Após explorar uma vulnerabilidade de upload de arquivo irrestrito e obter acesso a um host Linux durante um Teste de Penetração Externa, é crucial coletar informações básicas sobre o sistema.

**Identificação do Sistema Operacional:**
    - Determine com qual sistema operacional você está lidando (CentOS, Red Hat, Debian, Ubuntu, FreeBSD, Solaris, HP-UX, AIX, etc.).
    - Compreender o sistema operacional é essencial, pois os comandos e técnicas podem variar.

**Metodologia Geral:**
    - Comece com um alvo específico (por exemplo, Ubuntu) para aprender as táticas e técnicas gerais.
    - A abordagem básica será a mesma, independentemente do sistema Linux, uma vez que você entenda os fundamentos e o processo de Teste de Penetração.

**Prática e Flexibilidade:**
    - Há muitas dicas e métodos para enumerar sistemas Linux, e algumas informações podem ser obtidas de várias maneiras.
    - Pratique ajustar os comandos e técnicas conforme necessário e desafie-se a entender o que cada comando faz e como adaptá-lo.
    - Um conhecimento profundo permitirá sucesso em diferentes ambientes, além de apenas redigitar comandos de uma folha de dicas.

Normalmente, precisamos executar alguns comandos básicos para nos orientar:

- `whoami` : qual usuário estamos executando
- `id` : A quais grupos nosso usuário pertence?
- `hostname` : Qual é o nome do servidor? Podemos deduzir alguma coisa da convenção de nomenclatura?
- `ifconfig` ou `ip -a` : em qual sub-rede chegamos, o host tem NICs adicionais em outras sub-redes?
- `sudo -l` : nosso usuário pode executar qualquer coisa com sudo (como outro usuário como root) sem precisar de uma senha? Às vezes, isso pode ser a vitória mais fácil e podemos fazer algo como `sudo su`e cair direto em um shell root.

Incluir capturas de tela das informações obtidas pode ser útil em relatórios de clientes. Elas fornecem evidências de uma Execução Remota de Código (RCE) bem-sucedida e ajudam a identificar claramente o sistema afetado.

Após capturar as evidências, passe para uma enumeração mais detalhada e sistemática.

Começaremos verificando com qual sistema operacional e versão estamos lidando.

```bash
cat /etc/os-release
```
![[Pasted image 20240719102522.png]]

O alvo está executando [o Ubuntu 20.04.4 LTS ("Focal Fossa")](https://releases.ubuntu.com/20.04/). Para qualquer versão que encontrarmos devemos verificar se é algo desatualizado ou mantido.

**Variável PATH**
	- O PATH é a variável de ambiente onde o sistema Linux procura executáveis para comandos digitados.
	- Exemplo: O comando `id` está localizado em `/usr/bin/id` no sistema.
	- Se a variável PATH estiver mal configurada, pode ser possível explorá-la para escalar privilégios.
	- Anote o PATH atual e adicione essa informação à sua ferramenta de anotações.

```bash
echo $PATH
```
![[Pasted image 20240719103105.png]]

Podemos verificar todas as variáveis ​​de ambiente que estão definidas para nosso usuário atual, podemos ter sorte e encontrar algo sensível lá, como uma senha. Vamos anotar isso e seguir em frente.

```bash
env
```
![[Pasted image 20240719103205.png]]

Vamos anotar a versão do Kernel. Para verificar se o Kernel está vulnerável ou tem algum PoC de exploração pública conhecido.

Podemos fazer isso de algumas maneiras, outra maneira seria, `cat /proc/version` mas usaremos o comando `uname -a`

```bash
uname -a
```
![[Pasted image 20240719103450.png]]

Em seguida, podemos reunir algumas informações adicionais sobre o próprio host, como o tipo/versão da CPU:

```bash
lscpu
```
![[Pasted image 20240719103532.png]]

Quais shells de login existem no servidor? Anote-os e destaque que tanto `Tmux` quanto `Screen` estão disponíveis para nós.

```bash
cat /etc/shells
```
![[Pasted image 20240719103610.png]]

Também devemos procurar se há alguma defesa, elas incluem mas não se restringem à :
	- [Exec Shield](https://en.wikipedia.org/wiki/Exec_Shield)
	- [iptables](https://linux.die.net/man/8/iptables)
	- [AppArmor](https://apparmor.net/)
	- [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)
	- [Fail2ban](https://github.com/fail2ban/fail2ban)
	- [Snort](https://www.snort.org/faq/what-is-snort)
	- [Uncomplicated Firewall (ufw)](https://wiki.ubuntu.com/UncomplicatedFirewall)

Não é sempre possível enumerar configurações de proteções, mas saber quais estão em vigor pode evitar perda de tempo com tarefas desnecessárias.

Utilize o comando `lsblk` para listar dispositivos de bloco no sistema (discos rígidos, unidades USB, unidades ópticas, etc.). Se encontrar e montar uma unidade adicional ou sistema de arquivos desmontado, pode haver arquivos, senhas ou backups confidenciais que podem ser usados para escalar privilégios.

```bash
lsblk
```
![[Pasted image 20240719103946.png]]

**Impressoras**
	- Use o comando `lpstat` para encontrar informações sobre impressoras conectadas ao sistema.
	- Verifique se há trabalhos de impressão ativos ou em fila, pois eles podem conter informações sensíveis.

**Drives montados e desmontados**
	- Verifique se há drives montados e desmontados.
	- Tente montar um drive desmontado para acessar dados confidenciais.
	- Pesquise em `/etc/fstab` por palavras comuns como senha, nome de usuário, e credencial para encontrar informações relevantes.

```bash
cat /etc/fstab
```
|
```output
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-BdLsBLE4CvzJUgtkugkof4S0dZG7gWR8HCNOlRdLWoXVOba2tYUMzHfFQAP9ajul / ext4 defaults 0 0
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/20b1770d-a233-4780-900e-7c99bc974346 /boot ext4 defaults 0 0
```

Verifique a tabela de roteamento digitando `route`or `netstat -rn`. Aqui podemos ver quais outras redes estão disponíveis por meio de qual interface.

```bash
route
```
![[Pasted image 20240719104347.png]]

Se houver um domínio devemos verificar o `/etc/resolv.conf` para saber se o host está usando DNS interno. Podemos usar isso como ponto de partida para consultar o ambiente do Active Directory.

Devemos também verificar a tabela arp para ver com quais outros hosts o alvo está se comunicando.

```bash
arp -a
```

**Importância da Enumeração de Usuários:**
	- Conhecer os usuários no sistema de destino é crucial.
	- Usuários são frequentemente configurados para limitar os privilégios de serviços e manter a segurança do sistema.

Todos os usuários no sistema são listados no arquivo `/etc/passwd`. O formato do arquivo fornece informações sobre os usuários e suas configurações, como :

1. Username
2. Password
3. User ID (UID)
4. Group ID (GID)
5. User ID info
6. Home directory
7. Shell

#### Usuários existentes
```bash
cat /etc/passwd
```
![[Pasted image 20240719104925.png]]

Ocasionalmente, veremos hashes de senha diretamente no `/etc/passwd`. Este arquivo é legível por todos os usuários e, assim como os hashes no `/etc/shadow`arquivo, estes podem ser submetidos a um ataque de quebra de senha offline.

```bash
cat /etc/passwd | cut -f1 -d:
```
![[Pasted image 20240719105114.png]]

Identificar um algoritmo de hash a partir dos primeiros blocos de hash pode nos ajudar a usá-los e trabalhar com eles. Aqui está uma lista dos mais usados:

| **Algoritmo**  | **Cerquilha**  |
| -------------- | -------------- |
| MD5 Salgado    | `$1$`...       |
| SHA-256        | `$5$`...       |
| SHA-512        | `$6$`...       |
| Criptografia B | `$2a$`...      |
| Escrita        | `$7$`...       |
| Argônio2       | `$argon2i$`... |

Devemos saber quais usuários tem shell de login, quais shells estão no sistema, e versões do shell. Como a versão 4.1 do Bash, são vulneráveis ​​a um exploit `shellshock`.

```bash
grep "*sh$" /etc/passwd
```
![[Pasted image 20240719110134.png]]

**Grupos de Usuários:**
	- Cada usuário em sistemas Linux é atribuído a um ou mais grupos, que determinam seus privilégios especiais.
	- Por exemplo, para acessar uma pasta específica para desenvolvedores, o usuário deve estar no grupo apropriado.

**Arquivo /etc/group:**
	- Informações sobre os grupos disponíveis estão no arquivo `/etc/group`.
	- O arquivo mostra o nome dos grupos e os usuários atribuídos a cada um.

#### Grupos existentes
```bash
cat /etc/group
```
![[Pasted image 20240719110327.png]]

O arquivo `/etc/group` lista todos os grupos no sistema. Usando o `getent` listamos membros de grupos interessantes.

```bash
getent group sudo
```
![[Pasted image 20240719110546.png]]

**Verificação de Diretórios Home:**
	- Enumere os diretórios em `/home` para verificar se algum usuário está armazenando dados confidenciais ou arquivos com senhas.

**Arquivos e Configurações:**
	- Verifique se arquivos como `.bash_history` são legíveis e contenham comandos interessantes.
	- Procure por arquivos de configuração que possam conter credenciais úteis.

**Chaves SSH:**
	- Verifique as chaves SSH para todos os usuários.
	- Elas podem ser usadas para persistência no sistema, escalonamento de privilégios, pivotamento e encaminhamento de portas para a rede interna.

**Cache ARP:**
	- Verifique o cache ARP para identificar outros hosts acessados e faça referência cruzada com chaves privadas SSH utilizáveis.

```bash
ls /home
```
![[Pasted image 20240719110822.png]]

**Arquivos de Configuração:**
	- Procure por "fruta fácil de colher", como arquivos de configuração com extensões `.conf` e `.config`.
	- Esses arquivos podem conter nomes de usuários, senhas e outros segredos.

**Testar Senhas:**
	- Teste qualquer senha coletada em todos os usuários do sistema, pois a reutilização de senhas é comum.

**Sistemas de Arquivos Montados:**
	- Arquivos podem estar armazenados em sistemas de arquivos montados.
	- Tipos de sistemas de arquivos como ext4, NTFS e FAT32 podem ser montados, com diferentes benefícios e desvantagens.
	- Sistemas de arquivos de leitura/gravação permitem acesso para ler e gravar arquivos.
	- Montar e desmontar sistemas de arquivos requer privilégios de root.
	- Acesse sistemas de arquivos montados para encontrar informações, documentação ou aplicativos confidenciais.

#### Sistemas de arquivos montados
```bash
df -h
```
![[Pasted image 20240719111045.png]]

Ao ser desmontado, um sistema de arquivos não é mais acessível ao sistema. Portanto, se pudermos estender nossos privilégios ao `root`, poderemos montar e ler esses sistemas de arquivos nós mesmos.

Os sistemas de arquivos desmontados podem ser visualizados da seguinte forma:

#### Sistemas de arquivos não montados
```bash
cat /etc/fstab | frep -v "#" | column -t
```
![[Pasted image 20240719111313.png]]

Muitas pastas e arquivos são mantidos ocultos em um sistema Linux para que não sejam óbvios, e a edição acidental é evitada.

No entanto, precisamos ser capazes de localizar todos os arquivos e pastas ocultos porque eles podem frequentemente conter informações sensíveis

#### Todos os arquivos ocultos
```bash
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
```
![[Pasted image 20240719111446.png]]

#### Todos os diretórios ocultos
```bash
find / -type d -name ".*" -ls 2>/dev/null
```
![[Pasted image 20240719111537.png]]

**Pastas Temporárias:**
	- **/tmp:** Armazena arquivos temporários e exclui dados automaticamente após dez dias. Todos os arquivos são deletados quando o sistema é reiniciado.
	- **/var/tmp:** Também armazena arquivos temporários, mas os dados são retidos por até 30 dias. É usado para armazenar dados que precisam ser preservados entre reinicializações.

**Visibilidade e Uso:**
	- Essas pastas são visíveis e legíveis para todos os usuários.
	- Logs temporários e saídas de scripts podem ser encontrados nessas pastas.

#### Arquivos temporários
```bash
ls -l /tmp /var/dump /dev/shm
```
![[Pasted image 20240719111848.png]]

### Se movendo

**Próximos Passos:**
	- Após obter uma visão inicial e coletar dados sensíveis, o próximo passo é verificar permissões.
	- Cheque quais diretórios, scripts e binários podem ser lidos e escritos com os privilégios de usuário atuais.

**Uso de Ferramentas:**
	- Em uma avaliação do mundo real, considere executar o script **linPEAS** para obter uma análise abrangente de permissões e outras informações.
	- O script pode revelar problemas sutis que a enumeração manual pode não detectar.

**Prática de Enumeração Manual:**
	- Pratique a enumeração manual para desenvolver suas habilidades e crie sua própria folha de dicas de comandos e alternativas para diferentes sistemas Linux.
	- Ferramentas são úteis, mas saber realizar tarefas manualmente é crucial, especialmente quando ferramentas falham ou não estão disponíveis.

---
## Referências

**`htb`** - https://academy.hackthebox.com/module/51/section/1592





































