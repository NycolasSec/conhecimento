## Ajuste de sistemas
O daemon `tuned` aplica adaptações de ajustes estática e dinamicamente, usando perfis de ajuste que refletem os requisitos específicos de carga de trabalho.

### Configuração do ajuste estático
O daemon `tuned` aplica as configurações do sistema quando um serviço é iniciado ou após a seleção de um novo perfil de ajuste. O ajuste estático configura parâmetros `kernel` predefinidos em perfis que o daemon `tuned` aplica no boot.

### Configuração do ajuste dinâmico
Com o ajuste dinâmico, o daemon `tuned` monitora a atividade do sistema e ajusta as configurações, de acordo com as mudanças de comportamento do tempo de execução. O ajuste dinâmico é modificado continuamente para adaptar a carga de trabalho atual, começando com as configurações iniciais declaradas no perfil de ajuste escolhido.

Os perfis de ajuste predefinidos fornecem parâmetros de desempenho que o daemon `tuned` usa.

Para monitorar e ajustar as configurações de parâmetros, o daemon `tuned` usa módulos chamados de plugins de monitoramento e ajuste, respectivamente.

Os plugins de monitoramento analisam o sistema e obtêm informações dele, para que os plug-ins de ajuste usem essas informações para ajuste dinâmico. Neste momento, o daemon `tuned` é fornecido com três plugins de monitor:

- `disk`: monitora a carga do disco com base no número de operações de E/S para cada dispositivo de disco.
    
- `net`: monitora a carga da rede com base no número de pacotes transferidos por placa de rede.
    
- `load`: monitora a carga da CPU para cada CPU.

Plugins de ajuste ajustam os subsistemas individuais. Eles usam os dados dos plugins do monitor e os parâmetros de desempenho dos perfis de ajuste predefinidos. Entre outros, o daemon `tuned` é fornecido com os seguintes plugins de ajuste:

- `disk`: define diferentes parâmetros de disco, por exemplo, o scheduler de disco, o tempo-limite de rotação ou o gerenciamento avançado de energia.
    
- `net`: configura a velocidade da interface e a funcionalidade Wake-on-LAN (WoL).
    
- `cpu`: define diferentes parâmetros de CPU, por exemplo, o governador de CPU ou a latência.


Por padrão, o ajuste dinâmico está desabilitado. Você pode habilitá-lo definindo a variável `dynamic_tuning` como 1 no arquivo de configuração `/etc/tuned/tuned-main.conf`. Você pode definir o tempo em segundos entre as atualizações usando a variável `update_interval` no arquivo de configuração `/etc/tuned/tuned-main.conf`.

```shell-session
[root@host ~]$ cat /etc/tuned/tuned-main.conf

```
![[Pasted image 20240830141923.png]]

## Utilitário tuned
Você pode instalar e habilitar o pacote `tuned` usando os seguintes comandos:

```shell-session
[root@host ~]$ dnf install tuned

[root@host ~]$ systemctl enable --now tuned
```
![[Pasted image 20240830142012.png]]

O aplicativo `tuned` fornece perfis nas seguintes categorias:

- Perfis de economia de energia
    
- Perfis de aumento de desempenho
    

Os perfis de aumento de desempenho incluem perfis que se concentram nos seguintes aspectos:

- Baixa latência para armazenamento e rede
    
- Alto rendimento para armazenamento e rede
    
- Desempenho da máquina virtual
    
- Desempenho do host de virtualização

 A tabela mostra uma lista dos perfis de ajuste distribuídos com o Red Hat Enterprise Linux 9:

|Perfil ajustado|Finalidade|
|:--|:--|
|`balanced`|Ideal para sistemas que exigem um meio-termo entre economia de energia e desempenho.|
|`powersave`|Ajusta o sistema para máxima economia de energia.|
|`throughput-performance`|Ajusta o sistema para um rendimento máximo.|
|`accelerator-performance`|Sintoniza o mesmo que `throughput-performance` e reduz a latência para menos de 100 μs.|
|`latency-performance`|Ideal para sistemas de servidor que exigem baixa latência às custas do consumo de energia.|
|`network-throughput`|Derivado do perfil `throughput-performance`. Parâmetros de ajuste de rede adicionais são aplicados para o rendimento máximo da rede.|
|`network-latency`|Derivado do perfil `latency-performance`. Permite que parâmetros adicionais de ajuste de rede forneçam baixa latência de rede.|
|`desktop`|Derivado do perfil `balanced`. Fornece resposta mais rápida de aplicativos interativos.|
|`hpc-compute`|Derivado do perfil `latency-performance`. Ideal para computação de alto desempenho.|
|`virtual-guest`|Ajusta o sistema para desempenho máximo se for executado em uma máquina virtual.|
|`virtual-host`|Ajusta o sistema para desempenho máximo se agir como um host para máquinas virtuais.|
|`intel-sst`|Otimizado para sistemas com configurações da tecnologia Intel Speed Select. Use-o como uma sobreposição em outros perfis.|
|`optimize-serial-console`|Aumenta a capacidade de resposta do console serial. Use-o como uma sobreposição em outros perfis.|

O aplicativo `tuned` armazena os perfis de ajuste nos diretórios `/usr/lib/tuned` e `/etc/tuned`. Cada perfil tem um diretório separado e, dentro do diretório, o arquivo de configuração principal `tuned.conf` e, opcionalmente, outros arquivos.

```shel-session
[root@host ~]# cd /usr/lib/tuned

[root@host tuned]# ls
```
![[Pasted image 20240830142338.png]]

```shell-session
[root@host tuned]$ ls virtual-guest
```
![[Pasted image 20240830142359.png]]

Um arquivo de configuração `tuned.conf` típico tem a seguinte aparência:
```shell-session
[root@host tuned]# cat virtual-guest/tuned.conf
```
```txt
#
# tuned configuration
#

[main]
summary=Optimize for running inside a virtual guest
include=throughput-performance

[sysctl]
# If a workload mostly uses anonymous memory and it hits this limit, the entire
# working set is buffered for I/O, and any more write buffering would require
# swapping, so it's time to throttle writes until I/O can catch up.  Workloads
# that mostly use file mappings may be able to use even higher values.
#
# The generator of dirty data starts writeback at this percentage (system default
# is 20%)
vm.dirty_ratio = 30

# Filesystem I/O is usually much more efficient than swapping, so try to keep
# swapping low.  It's usually safe to go even lower than this on systems with
# server-grade storage.
vm.swappiness = 30
```

A seção `[main]` no arquivo pode incluir um sumário do perfil de ajuste. Essa seção também aceita o parâmetro `include` para o perfil herdar todas as configurações do perfil referenciado.

Para criar ou modificar perfis de ajuste, copie os arquivos de perfil de ajuste do diretório `/usr/lib/tuned` para o diretório `/etc/tuned` e modifique-os. Caso haja diretórios de perfil com o mesmo nome nos diretórios `/usr/lib/tuned` e `/etc/tuned`, os últimos sempre têm precedência. Por isso, nunca modifique diretamente os arquivos no diretório do sistema `/usr/lib/tuned`.

O restante das seções no arquivo `tuned.conf` usa os plugins de ajuste para modificar parâmetros no sistema. No exemplo anterior, a seção `[sysctl]` modifica os parâmetros de kernel `vm.dirty_ratio` e `vm.swappiness` com o plugin `sysctl`.

## Gerenciamento de perfis a partir da linha de comando.

Use o comando `tuned-adm` para alterar configurações do daemon `tuned`. O comando `tuned-adm` consulta configurações atuais, lista perfis disponíveis, recomenda um perfil de ajuste para o sistema, altera perfis diretamente ou desativa o ajuste.

Você pode identificar o perfil de ajuste atualmente ativo com o comando `tuned-adm active`.
```shell-sessino
[root@host ~]# tuned-adm active
```
![[Pasted image 20240830142913.png]]

O comando `tuned-adm list` lista todos os perfis de ajuste disponíveis, incluindo perfis integrados e perfis de ajuste criados de maneira personalizada.
```shell-session
[root@host ~]# tuned-adm list
```
![[Pasted image 20240830142957.png]]

Use o comando `tuned-adm profile_info` para obter informações sobre um determinado perfil.
```shell-session
[root@host ~]$ tuned-adm profile_info network-latency
```
![[Pasted image 20240830143118.png]]

Se nenhum perfil for especificado, o comando `tuned-adm profile_info` mostrará as informações para o perfil de ajuste ativo:
```shell-session
[root@host ~]$ tuned-adm active
```
![[Pasted image 20240830143206.png]]

```shell-session
[root@host ~]$ tuned-adm profile_info
```
![[Pasted image 20240830143237.png]]

Use o comando ``tuned-adm profile _`profilename`_`` para alternar para um perfil ativo diferente que atenda melhor os requisitos de ajuste atuais do sistema.

```shell-session
[root@host ~]$ tuned-adm profile throughput-performance

[root@host ~]$ tuned-adm active
```
![[Pasted image 20240830143327.png]]

O comando `tuned-adm recommend` pode recomendar um perfil de ajuste para o sistema. O sistema usa esse mecanismo para determinar o perfil padrão após a instalação.
```shell-session
[root@host ~]$ tuned-adm recommend
```
![[Pasted image 20240830143503.png]]

Para reverter as alterações de configuração que o perfil atual aplicou, alterne para outro perfil ou desative o daemon ajustado. Desative a atividade de ajuste do aplicativo `tuned` usando o comando `tuned-adm off`.
```shell-session
[root@host ~]$ tuned-adm off
[root@host ~]$ tuned-adm active
```
![[Pasted image 20240830143556.png]]










































