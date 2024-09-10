Quando os aplicativos não funcionam inesperadamente devido a negações de acesso do ``SELinux``, há métodos e ferramentas disponíveis para resolver esses problemas. É útil começar entendendo alguns conceitos e comportamentos fundamentais quando o ``SELinux`` está habilitado.

- O SELinux consiste em políticas direcionadas que definem explicitamente as ações permitidas.
- Uma entrada de política define um processo rotulado e um recurso rotulado que interage.
- A política declara o tipo de processo e o contexto de arquivo ou porta usando rótulos.
- A entrada da política define um tipo de processo, um rótulo de recurso e a ação explícita a ser permitida.
- Uma ação pode ser uma chamada do sistema, uma função do kernel ou outra rotina de programação específica.
- Se nenhuma entrada for criada para um relacionamento específico de processo-recurso-ação, a ação será negada.
- Quando uma ação é negada, a tentativa é registrada com informações de contexto úteis.

O Red Hat Enterprise Linux fornece uma política SELinux direcionada estável para quase todos os serviços na distribuição. Portanto, é incomum ter problemas de acesso do SELinux com serviços comuns do RHEL quando eles são configurados corretamente. Os problemas de acesso do SELinux ocorrem quando os serviços são implementados incorretamente ou quando novos aplicativos têm políticas incompletas. Considere esses conceitos de solução de problemas antes de fazer grandes alterações na configuração do SELinux.

- A maioria das negações de acesso indica que o SELinux está funcionando corretamente por meio do bloqueio de ações impróprias.
- A avaliação de ações negadas requer alguma familiaridade com ações de serviço normais e esperadas.
- O problema mais comum do SELinux é um contexto incorreto em arquivos novos, copiados ou transferidos.
- Os contextos de arquivo podem ser corrigidos quando uma política existente faz referência ao seu local.
- Os recursos opcionais da política booleana estão documentados nas páginas do man `_selinux`.
- A implementação de recursos booleanos geralmente exige a definição de configurações adicionais que não sejam do SELinux.
- As políticas SELinux não substituem nem contornam as permissões de arquivo ou as restrições da lista de controle de acesso.

### Monitoramento de violações do SELinux

O serviço de solução de problemas do SELinux, do pacote `setroubleshoot-server`, fornece ferramentas para diagnosticar problemas do SELinux. Quando o SELinux nega uma ação, uma mensagem de cache de vetor de acesso (AVC, Access Vector Cache) é registrada no arquivo de log de segurança `/var/log/audit/audit.log`. O serviço de solução de problemas do SELinux monitora eventos AVC e envia um sumário do evento para o arquivo `/var/log/messages`.

O sumário do AVC inclui um identificador exclusivo do evento (UUID). Use o comando ``sealert -l _`UUID`_`` para visualizar detalhes abrangentes do relatório para o evento específico. Use o comando `sealert -a /﻿var/log/audit/audit.log` para visualizar todos os eventos existentes.

Considere o exemplo a seguir de sequência de comandos em um servidor web Apache padrão. Você cria `/root/mypage` e o move para o diretório de conteúdo padrão do Apache (`/var/www/html`). Em seguida, depois de iniciar o serviço Apache, você tenta recuperar o conteúdo do arquivo.

```shell-session
[root@host ~]# touch /root/mypage
[root@host ~]# mv /root/mypage /var/www/html
[root@host ~]# systemctl start httpd
[root@host ~]# curl http://localhost/mypage
```
![[Pasted image 20240909183638.png]]

O servidor web não exibe o conteúdo e retorna um erro `permission denied`. Um evento AVC é registrado nos arquivos `/var/log/audit/audit.log` e `/var/log/messages`. Observe o comando sugerido `sealert` e a UUID na mensagem de evento `/var/log/messages`.

```shell-session
[root@host ~]# tail /var/log/audit/audit.log
```
![[Pasted image 20240909183728.png]]

```shell-session
[root@host ~]# tail /var/log/messages
```
![[Pasted image 20240909183748.png]]

A saída `sealert` descreve o evento, incluindo o processo afetado, o arquivo acessado e a ação tentada e negada. A saída inclui recomendações para corrigir o rótulo do arquivo se apropriado. Conselhos adicionais descrevem como gerar uma nova política para permitir a ação negada.

```shell-session
[root@host ~]# sealert -l 95f41f98-6b56-45bc-95da-ce67ec9a9ab7
```
![[Pasted image 20240909183923.png]]

Neste exemplo, o arquivo acessado está no local correto, mas não tem o contexto de arquivo SELinux correto. A seção `Raw Audit Messages` exibe informações da entrada do evento `/var/log/audit/audit.log`. Use o comando `restorecon /var/www/html/mypage` para definir o rótulo de contexto correto. Para corrigir vários arquivos recursivamente, use o comando `restorecon -R` no diretório pai.

Use o comando `ausearch` para pesquisar eventos AVC no arquivo de log `/var/log/audit/audit.log`. Use a opção `-m` para especificar o tipo de mensagem `AVC` e a opção `-ts` para fornecer uma dica de hora, como `recent`.

```shell-session
[root@host ~]# ausearch -m AVC -ts recent
```
![[Pasted image 20240909183959.png]]






