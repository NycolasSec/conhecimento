A maioria dos aplicativos e serviços críticos usa arquivos e diretórios temporários. Alguns utilizam o diretório `/tmp` para dados transitórios, enquanto outros usam locais específicos como `/run`, que são voláteis e existem apenas na memória. Esses sistemas de arquivos baseados em memória são limpos automaticamente após reinicializações ou perdas de energia.

Daemons e scripts dependem da existência desses arquivos temporários para operar corretamente, e a limpeza de arquivos temporários em armazenamento persistente é crucial para evitar problemas de espaço em disco e dados obsoletos.


O Red Hat Enterprise Linux inclui a ferramenta `systemd-tmpfiles`, que fornece um método estruturado e configurável de gerenciar diretórios e arquivos temporários.

No boot do sistema, uma das primeiras unidades de serviço `systemd` iniciadas é o serviço `systemd-tmpfiles-setup`. Esse serviço executa a opção `--create --remove` do comando `systemd-tmpfiles`, que lê as instruções dos arquivos de configuração `/usr/lib/tmpfiles.d/*.conf`, `/run/tmpfiles.d/*.conf` e `/etc/tmpfiles.d/*.conf`. Esses arquivos de configuração listam arquivos e diretórios que o serviço `systemd-tmpfiles-setup` é instruído a criar, excluir ou proteger com permissões.

#### Limpeza de arquivos temporários com um temporizador do systemd

Para impedir que os sistemas de longa execução encham seus discos com dados antigos, uma unidade de temporizador do `systemd` chamada `systemd-tmpfiles-clean.timer` aciona a unidade `systemd-tmpfiles-clean.service` em um intervalo regular, que executa o comando `systemd-tmpfiles --clean`.

Uma configuração de unidade de temporizador do `systemd` tem uma seção `[Timer]` para indicar como iniciar o serviço com o mesmo nome do temporizador.

Use o comando `systemctl` a seguir para visualizar o conteúdo do arquivo de configuração da unidade do `systemd-tmpfiles-clean.timer`.

```shell-session
[user@host ~]$ systemctl cat systemd-tmpfiles-clean.timer
```
![[Pasted image 20240909145005.png]]

Na configuração anterior, o parâmetro `OnBootSec=15min` indica que a unidade `systemd-tmpfiles-clean.service` é acionada 15 minutos após o sistema ter sido inicializado. O parâmetro `OnUnitActiveSec=1d` indica que qualquer outro trigger para a unidade `systemd-tmpfiles-clean.service` acontece 24 horas após a unidade de serviço ter sido ativada por último.

Altere os parâmetros no arquivo de configuração da unidade `systemd-tmpfiles-clean.timer` para atender aos requisitos. Por exemplo, um valor `30min` para o parâmetro `OnUnitActiveSec` aciona a unidade `systemd-tmpfiles-clean.service` 30 minutos depois de a unidade de serviço ter sido ativada pela última vez. Como resultado, a unidade `systemd-tmpfiles-clean.service` é acionada a cada 30 minutos depois de as alterações serem reconhecidas.

Depois de alterar o arquivo de configuração da unidade de temporizador, use o comando `systemctl daemon-reload` para garantir que o daemon `systemd` carregue a nova configuração.

```shell-session
[root@host ~]# systemctl daemon-reload
```

#### Limpeza manual dos arquivos temporários
O comando `systemd-tmpfiles --clean` analisa os arquivos de configuração usados pelo `systemd-tmpfiles` para eliminar arquivos que não foram acessados, alterados ou modificados além da idade máxima definida.

Para detalhes sobre o formato desses arquivos de configuração, consulte a página do man `tmpfiles.d(5)`. A sintaxe inclui colunas como Type, Path, Mode, UID, GID, Age e Argument, onde "Type" especifica a ação a ser executada, como criar um diretório ou restaurar contextos de SELinux.

O seguinte comando elimina uma configuração com explicações:
```shell-session
d /run/systemd/seats 0755 root root -
```

Ao criar arquivos e diretórios, crie o diretório `/run/systemd/seats` se ele não existir, com o usuário `root`, e o grupo `root` como proprietários e com as permissões de `rwxr-xr-x`. Se esse diretório existir, não execute nenhuma ação. O serviço `systemd-tmpfiles` não limpa esse diretório automaticamente.
```shell-session
D /home/student 0700 student student 1d
```

Crie o diretório `/home/student` se ele ainda não existir. Se ele existir, remova todo o conteúdo dele. Quando o sistema executa o comando `systemd-tmpfiles --clean`, ele remove todos os arquivos do diretório que você não acessou, alterou ou modificou há mais de um dia.

```shell-session
L /run/fstablink - root root - /etc/fstab
```

Crie o link simbólico `/run/fstablink` para apontar para o diretório `/etc/fstab`. Nunca limpe automaticamente essa linha.

#### Precedência do arquivo de configuração
Os arquivos de configuração do serviço `systemd-tmpfiles-clean` podem existir em três locais:

- `/etc/tmpfiles.d/*.conf`
- `/run/tmpfiles.d/*.conf`
- `/usr/lib/tmpfiles.d/*.conf`

Os arquivos em `/etc/tmpfiles.d/` são usados para configurar locais temporários personalizados e substituir padrões fornecidos por fornecedores. Arquivos em `/run/tmpfiles.d/` são voláteis e usados por daemons para gerenciar arquivos temporários de tempo de execução. Arquivos fornecidos por pacotes RPM estão em `/usr/lib/tmpfiles.d/` e não devem ser editados.

Se um arquivo em `/run/tmpfiles.d/` tiver o mesmo nome que um arquivo em `/usr/lib/tmpfiles.d/`, o arquivo de `/run/tmpfiles.d/` será usado. Da mesma forma, arquivos em `/etc/tmpfiles.d/` substituirão os de `/run/tmpfiles.d/` e `/usr/lib/tmpfiles.d/` se tiverem o mesmo nome.

Para substituir configurações de fabricantes, copie o arquivo relevante para `/etc/tmpfiles.d/` e edite-o conforme necessário, garantindo que suas configurações não sejam substituídas por atualizações de pacotes.
































































































































































