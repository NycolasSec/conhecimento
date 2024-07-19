Após obter informações básicas sobre permissões, o próximo passo é uma enumeração mais detalhada dos internos do sistema operacional do host. Esta fase envolve a coleta de dados cruciais que ajudarão em ataques futuros:

**Serviços e Aplicativos:**
	- Quais serviços e aplicativos estão instalados?
	- Quais serviços estão em execução?
	- Quais soquetes estão em uso?

**Usuários e Grupos:**
	- Quais usuários, administradores e grupos existem no sistema?
	- Quem está logado no momento? Quais usuários logaram recentemente?
	- Quais políticas de senha estão em vigor?

**Domínio e Rede:**
	- O host está associado a um domínio do Active Directory?
	- Informações atuais de endereçamento IP
	- Conexões de rede interessantes dentro e fora da rede interna
	- Presença de outras interfaces de rede para possível pivotamento

**Arquivos e Histórico:**
	- Informações em arquivos de histórico, log e backup
	- Arquivos modificados recentemente e padrões de modificação que indicam uso de cron jobs
	- Conteúdo interessante no arquivo `/etc/hosts`
	- Acessar o arquivo `bash_history` de usuários para encontrar senhas ou comandos interessantes
	- Tarefas cron em execução que podem ser sequestradas

**Ferramentas Utilizáveis:**
	- Ferramentas instaladas como Netcat, Perl, Python, Ruby, Nmap, tcpdump, gcc, etc.

Use comandos como `ip a` ou `ifconfig` (se o pacote net-tools estiver presente) para coletar informações de rede.

## Internals

**Configuração Interna:**
	- Refere-se à configuração interna e à maneira de trabalhar do sistema, incluindo processos integrados projetados para realizar tarefas específicas.

**Interfaces de Comunicação:**
	- Inicie a análise pelas interfaces pelas quais o sistema de destino pode se comunicar.

#### Interfaces de rede
```bash
ip a
```
![[Pasted image 20240719143912.png]]

#### Hosts
```bash
cat /etc/hosts
```
![[Pasted image 20240719144020.png]]

Também devemos ver o último login dos usuários do sistema, assim saberemos o quão utilizado esse sistema é.

#### Último login de usuário
```bash
lastlog
```
![[Pasted image 20240719144128.png]]

Devemos também verificar se há mais algum usuário logado conosco.

#### Usuários logados
```bash
w
```
![[Pasted image 20240719144353.png]]

O histórico do bash pode conter senhas de comandos utilizados, e também dá uma visão geral do sistema.

#### Histórico de comandos
```bash
history
```
![[Pasted image 20240719144546.png]]

Às vezes também podemos encontrar arquivos de histórico especiais criados por scripts ou programas. Isso pode ser encontrado, entre outros, em scripts que monitoram certas atividades de usuários e verificam atividades suspeitas.

#### Encontrando arquivos de histórico
```bash
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
```
![[Pasted image 20240719144700.png]]

**Cron Jobs:**
	- Semelhantes às tarefas agendadas do Windows.
	- Configurados para executar tarefas de manutenção e backup.
	- Podem ser explorados para escalonamento de privilégios, especialmente se houver configurações incorretas, como caminhos relativos ou permissões fracas.

#### cron
```bash
ls -la /etc/cron.daily/
```
![[Pasted image 20240719144808.png]]

**Sistema de Arquivos `proc` (procfs):**
	- Um sistema de arquivos específico no Linux que contém informações sobre processos do sistema, hardware e outras informações do sistema.
	- Principal meio de acessar informações de processos e modificar configurações do kernel.
	- Virtual e gerado dinamicamente pelo kernel.
	- Usado para visualizar o estado dos processos em execução, parâmetros do kernel, memória do sistema e dispositivos.
	- Define parâmetros do sistema, como prioridade do processo, agendamento e alocação de memória.

#### proc
```bash
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```
![[Pasted image 20240719144917.png]]

## Services

**Pacotes Instalados:**
	- Sistemas Linux mais antigos têm maior probabilidade de ter pacotes com vulnerabilidades conhecidas.
	- Versões atuais também podem ter pacotes ou softwares antigos que podem ser vulneráveis.

**Próximos Passos:**
	- Criação de uma lista de pacotes instalados para ajudar na detecção de pacotes potencialmente perigosos.

#### Pacotes instalados
```bash
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
```
![[Pasted image 20240719145212.png]]

Também deve-se verificar se a versão do `sudo` instalada no sistema é vulnerável a qualquer exploração antiga ou recente.

#### Versão Sudo
```bash
sudo -V
```
![[Pasted image 20240719145318.png]]

Ocasionalmente pode haver programas compilados na forma de binários. Esses não exigem instalação.

#### Binários
```nash
ls -l /bin /usr/bin/ /usr/sbin
```
![[Pasted image 20240719145541.png]]

[O GTFObins](https://gtfobins.github.io/) fornece uma plataforma excelente que inclui uma lista de binários que podem ser potencialmente explorados para escalar nossos privilégios no sistema alvo.

#### GTFObins
```bash
for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done
```
![[Pasted image 20240719145701.png]]

#### Ferramenta `strace` em Sistemas Linux

**Função:**
	- Rastreia e analisa chamadas de sistema e processamento de sinais.
	- Permite seguir o fluxo de um programa, entender como ele acessa recursos do sistema e processa sinais.
	- Monitoramento de atividades relacionadas à segurança e identificação de potenciais vetores de ataque, como solicitações específicas para hosts remotos.

**Recursos:**
	- A saída pode ser gravada em um arquivo para análise posterior.
	- Oferece uma variedade de opções para monitoramento detalhado do comportamento do programa.

#### Trace System Calls
```bash
strace ping -c1 10.129.112.20
```
![[Pasted image 20240719150031.png]]

**Arquivos de Configuração:**
	- Geralmente legíveis por qualquer usuário se mantidos com permissões padrão.
	- Revelam como o serviço é configurado e podem conter informações confidenciais, como chaves e caminhos para arquivos ocultos.
	- Mesmo que não tenhamos permissão para acessar a pasta, arquivos com permissões de leitura para todos podem ser acessados.

#### Arquivos de configuração
```bash
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
```
![[Pasted image 20240719150153.png]]

**Scripts:**
	- Semelhantes aos arquivos de configuração, mas frequentemente negligenciados em termos de segurança interna.
	- Podem ter permissões incorretas, oferecendo acesso potencial a processos internos e informações valiosas.
	- Importantes para descobrir detalhes sobre a configuração e processos do sistema, que podem ser úteis para exploração.

#### scripts
```bash
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```
![[Pasted image 20240719150302.png]]

**Lista de Processos:**
	- Oferece informações sobre scripts e binários em uso e o usuário que os está executando.
	- Scripts ou binários com permissões inadequadas, criados pelo administrador, podem ser executados mesmo sem acesso root.

#### Serviços em execução por usuário
```bash
ps aux | grep root
```
![[Pasted image 20240719150335.png]]















































































































