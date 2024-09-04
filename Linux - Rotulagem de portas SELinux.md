Além do contexto do arquivo e da rotulagem do tipo de processo, o SELinux rotula as portas de rede com um contexto SELinux. O SELinux controla o acesso à rede rotulando as portas de rede e incluindo regras na política de destino (targeted) de um serviço.

Por exemplo, a política de destino de SSH inclui a porta `22/TCP` com um rótulo de contexto de porta `ssh_port_t`. Na política de HTTP, as portas padrão `80/TCP` e `443/TCP` usam um rótulo de contexto de porta `http_port_t`.

Quando um processo de destino tenta abrir uma porta para escuta, o SELinux verifica se a política inclui entradas que permitem a vinculação do processo e do contexto. O SELinux pode, então, impedir que um serviço não autorizado assuma o controle de portas usadas por outros serviços de rede legítimos.

### Gerenciar rotulagem de portas SELinux
Se um serviço tentar escutar em uma porta não padrão, e a porta não estiver rotulada com o tipo correto do SELinux, o SELinux poderá bloquear a tentativa. Você pode corrigir esse problema alterando o contexto SELinux na porta.

Normalmente, a política `targeted` já terá rotulado todas as portas esperadas com o tipo correto. Por exemplo, como a porta `8008/TCP` é frequentemente usada para aplicativos web, ela já está rotulada com `http_port_t`, que é o tipo de porta padrão utilizado por um servidor web. Portas individuais podem ser rotuladas com apenas um contexto de porta.

#### Listar rótulos de porta
--- ---
Use o comando `grep` para filtrar o número da porta.
```shell-session
[root@host ~]# grep gopher /etc/services
```
![[Pasted image 20240902142639.png]]
--- ---
Use o comando `semanage` para listar as atribuições de rótulos de portas atuais.
```shell-session
[root@host ~]# semanage port -l
```
![[Pasted image 20240902142736.png]]
--- ---
Use o comando `grep` para filtrar o rótulo de porta do SELinux usando o nome do serviço.
```shell-session
[root@host ~]# semanage port -l | grep ftp
```
![[Pasted image 20240902142836.png]]
--- ---
Um rótulo de porta pode aparecer na lista muitas vezes para cada protocolo de rede compatível.

Use o comando `grep` para filtrar o rótulo de porta do SELinux usando o número da porta.
```shell-session
[root@host ~]# semanage port -l | grep -w 70
```
![[Pasted image 20240902143017.png]]
--- ---

#### Gerenciar vinculações de porta
Use o comando `semanage` para atribuir novos rótulos de porta, bem como para remover e modificar os existentes.

Você pode rotular uma nova porta com um rótulo de contexto de porta existente (tipo).
--- ---

No comando `semanage port`, a opção `-a` adiciona um novo rótulo de porta, a opção `-t` denota o tipo, e a opção `-p` denota o protocolo.
```shell-session
[root@host ~]# semanage port -a -t port_label -p tcp|udp PORTNUMBER
```
--- ---

No seguinte exemplo, habilite o serviço `gopher` para escutar na porta `71/TCP`:
```shell-session
[root@host~]# semanage port -a -t gopher_port_t -p tcp 71
```
--- ---

Para exibir alterações locais da política padrão, use a opção `-C` do comando `semanage port`.
```shell-session
[root@host~]# semanage port -l -C
```
![[Pasted image 20240902143414.png]]
--- ---

As políticas de destino incluem muitos tipos de porta.

As páginas do man do SELinux específicas do serviço são nomeadas usando o nome do serviço mais `_selinux`. Essas páginas do man incluem informações específicas do serviço sobre tipos, booleanos e tipos de porta do SELinux, e não são instaladas por padrão.
--- ---

Para exibir uma lista de todas as páginas do man do SELinux disponíveis, instale o pacote e execute uma pesquisa de palavra-chave `man -k` para a string `_selinux`.
```shell-session
[root@host ~]# dnf -y install selinux-policy-doc
[root@host ~]# man -k _selinux
```
--- ---

Use o comando `semanage` para excluir um rótulo de porta, com a opção `-d`. No seguinte exemplo, remova a vinculação da porta `71/TCP` ao tipo `gopher_port_t`:
```shell-session
[root@host ~]# semanage port -d -t gopher_port_t -p tcp 71
```
--- ---

Para alterar uma vinculação de porta quando ocorre uma mudança nos requisitos, use a opção `-m`. Essa opção é mais eficiente do que excluir a vinculação anterior e adicionar a mais nova.
--- ---

Por exemplo, para modificar a porta `71/TCP` de `gopher_port_t` para `http_port_t`, use o seguinte comando:
```shell-session
[root@server ~]# semanage port -m -t http_port_t -p tcp 71
```
--- ---

Visualize a modificação usando o comando ``semanage``.
```shell-session
[root@server ~]# semanage port -l -C
```
![[Pasted image 20240902144758.png]]

```shell-session
[root@server ~]# semanage port -l | grep http
```
![[Pasted image 20240902144819.png]]


























































































