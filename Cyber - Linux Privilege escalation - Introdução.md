Durante uma avaliação, você pode obter um shell de baixo privilégio em um host Linux e precisar executar escalonamento de privilégios para a conta root.

Além disso, se a máquina Linux for unida ao domínio, podemos obter o hash NTLM e começar a enumerar e atacar o Active Directory.

## Enumeração
É a chave para o escalonamento de privilégios. Vários scripts auxiliares (como [LinEnum](https://github.com/rebootuser/LinEnum) ) existem para auxiliar na enumeração. Ainda assim, também é importante entender quais informações procurar e ser capaz de executar sua enumeração manualmente.

É importante verificar alguns detalhes importantes:

**Versão do Sistema Operacional (OS Version):**
	- Identificar a distribuição (Ubuntu, Debian, FreeBSD, Fedora, SUSE, Red Hat, CentOS, etc.) fornece informações sobre as ferramentas disponíveis e ajuda a localizar exploits públicos.
	- Conhecer a versão específica do sistema operacional pode revelar vulnerabilidades conhecidas.

**Versão do Kernel (Kernel Version):**
	- Assim como a versão do SO, a versão do kernel pode ter exploits públicos conhecidos.
	- Exploits do kernel podem causar instabilidade ou falha total do sistema.
	- É crucial entender os exploits e suas possíveis consequências antes de executá-los em sistemas de produção.

**Serviços em Execução (Running Services):**
	- Identificar serviços em execução, especialmente aqueles executados como root, é essencial.
	- Serviços mal configurados ou vulneráveis executados como root são alvos fáceis para escalonamento de privilégios.
	- Exemplos de serviços com vulnerabilidades incluem Nagios, Exim, Samba, ProFTPd.
	- Existem provas de conceito públicas para muitas dessas vulnerabilidades, como a CVE-2016-9566 no Nagios Core < 4.2.4.

#### Listar processos atuais
```bash
ps aux | grep root
```
![[Pasted image 20240719091527.png]]
Aqui listamos os processos com permissões de root.

**Pacotes Instalados e suas Versões:**
	- Verificar se há pacotes desatualizados ou vulneráveis é crucial para identificar possíveis escalonamentos de privilégios.
	- Exemplo: A versão 4.05.00 do Screen, um multiplexador de terminal, possui uma vulnerabilidade de escalonamento de privilégios.

**Usuários Conectados:**
	- Identificar quais usuários estão conectados ao sistema e suas atividades pode revelar informações úteis para movimentações laterais locais e caminhos de escalonamento de privilégios.

#### Listar processos atuais
```bash
ps au
```
![[Pasted image 20240719091449.png]]

**Diretórios Home dos Usuários:**
	- Verifique se os diretórios home de outros usuários são acessíveis.
	- Diretórios home podem conter chaves SSH, scripts e arquivos de configuração com credenciais.
	- Esses arquivos podem ser usados para acessar outros sistemas ou obter entrada no ambiente do Active Directory.

### Conteúdo do diretório inicial
```bash
ls /home
```
![[Pasted image 20240719091734.png]]

Podemos verificar diretórios de usuários individuais e verificar se arquivos como o `.bash_history`, costumam ser legíveis, procurar arquivos de configuração e verificar se podemos obter cópias das chaves SSH de um usuário.

#### Conteúdo do diretório inicial do usuário
```bash
ls -la /home/stacey.jenkins/
```
![[Pasted image 20240719092146.png]]

Caso encontremos uma chave SSH podemos usar para abrir uma sessão estável no host, elas podem ser usadas para acessar outros sistemas na rede.

Sempre verifique o cache ``ARP`` para ver quais outros hosts estão sendo acessados e faça uma referência cruzada com chaves SSH utilizáveis.

#### conteúdo do diretório SSH
```bash
ls -l ~/.ssh
```
![[Pasted image 20240719092613.png]]

Deve-se verificar o histórico bash de um usuário, pois ele pode ter passado senhas como argumento a algum comando, configurado tarefas e muito mais.

#### Histórico do Bash
```bash
history
```
![[Pasted image 20240719092854.png]]

**Privilégios Sudo:**
	- Verifique se o usuário pode executar comandos como outro usuário ou como root.
	- Entradas sudoer com NOPASSWD permitem que o usuário execute comandos especificados sem fornecer uma senha.
	- Nem todos os comandos executados como root levarão à escalada de privilégios.
	- Obter acesso a um usuário com privilégios sudo completos permite executar qualquer comando como root.
	- Um comando simples como `sudo su` pode proporcionar uma sessão root imediata.

#### Sudo - Listar privilégios do usuário
```bash
sudo l
```
![[Pasted image 20240719093252.png]]

**Arquivos de Configuração:**
	- Arquivos de configuração podem conter informações valiosas, como nomes de usuários, senhas e segredos.
	- É útil pesquisar em arquivos com extensões como .conf e .config.

**Arquivo Shadow Legível:**
	- Se o arquivo `/etc/shadow` for legível, você pode obter hashes de senha para usuários com senhas definidas.
	- Esses hashes podem ser usados em ataques de força bruta offline para tentar recuperar senhas em texto simples.

**Hashes de Senha em /etc/passwd:**
	- Às vezes, hashes de senha podem aparecer diretamente no arquivo `/etc/passwd`, que é legível por todos os usuários.
	- Esses hashes também podem ser alvo de ataques de quebra de senha offline.
	- Essa configuração é rara, mas pode ocorrer em dispositivos e roteadores embarcados.

#### Passwd
```bash
cat etc /etc/passwd
```
![[Pasted image 20240719093519.png]]

**Cron Jobs:**
	- Trabalhos cron em sistemas Linux são semelhantes às tarefas agendadas do Windows.
	- São usados para tarefas de manutenção e backup.
	- Configurações incorretas, como caminhos relativos ou permissões fracas, podem permitir a escalada de privilégios quando o trabalho cron é executado.

#### Cron jobs
```bash
ls -la /etc/cron.daily/
```
![[Pasted image 20240719093705.png]]

**Sistemas de Arquivos Desmontados e Unidades Adicionais:**
	- Montar uma unidade adicional ou um sistema de arquivos desmontado pode revelar arquivos confidenciais, senhas ou backups.
	- Esses arquivos podem ser usados para aumentar privilégios.

#### Sistemas de arquivos e unidades adicionais
```bash
lsblk
```
![[Pasted image 20240719093826.png]]

**Permissões SETUID e SETGID:**
	- Binários com permissões SETUID e SETGID permitem que um usuário execute comandos como root sem acesso root completo.
	- Muitos binários com essas permissões podem ser explorados para obter um shell root.

**Diretórios Graváveis:**
	- Identificar diretórios graváveis é crucial para baixar ferramentas ou scripts.
	- Descobrir um diretório gravável onde um cron job coloca arquivos pode indicar a frequência de execução do cron job e fornecer oportunidades para elevar privilégios, especialmente se o script do cron job também for gravável.

#### Encontre diretórios graváveis
```bash
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```
![[Pasted image 20240719101055.png]]

**Arquivos Graváveis:**
	- Verifique se algum script ou arquivo de configuração é gravável por qualquer usuário.
	- Modificar arquivos de configuração pode ser destrutivo, mas pequenas alterações podem ampliar o acesso.
	- Scripts executados como root através de tarefas cron podem ser levemente modificados para anexar comandos adicionais.

#### Encontre arquivos graváveis
```bash
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```
![[Pasted image 20240719101203.png]]

### Se movendo
Como vimos, há várias técnicas de enumeração manual que podemos executar para obter informações para informar vários ataques de escalonamento de privilégios.



























