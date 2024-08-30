## Registro em log do sistema
O kernel e outros processos do sistema operacional registram logs de eventos para auditoria e solução de problemas. No Red Hat Enterprise Linux, os logs são registrados usando o protocolo Syslog e controlados pelos serviços systemd-journald e rsyslog. Utilitários de texto como ``less`` e ``tail`` podem ser usados para inspecionar esses logs.

O serviço `systemd-journald` está no centro da arquitetura de log de eventos do sistema operacional. O serviço `systemd-journald` coleta mensagens de eventos de muitas fontes:

- Kernel do sistema
- Saída dos estágios iniciais do processo de boot
- Saída padrão e erro padrão dos daemons
- Eventos do syslog

O serviço `systemd-journald` reestrutura os logs em um formato padrão e os grava em um diário estruturado e indexado do sistema. Por padrão, esse diário é armazenado em um sistema de arquivos que não persiste nas reinicializações.

O serviço `rsyslog` lê mensagens do syslog recebidas pelo serviço `systemd-journald` do diário conforme elas chegam. O serviço `rsyslog` então processa os eventos syslog, registrando-os em seus arquivos de log ou encaminhando-os para outros serviços de acordo com sua própria configuração.

O serviço `rsyslog` classifica e grava as mensagens do syslog nos arquivos de log que persistem em reinicializações no diretório `/var/log`. O serviço também classifica as mensagens de log para arquivos de log específicos com base no tipo de programa que enviou cada mensagem e a prioridade de cada mensagem do syslog.

Além dos arquivos de mensagens do syslog, o diretório `/var/log` contém arquivos de log de outros serviços no sistema. A tabela a seguir lista alguns arquivos úteis no diretório `/var/log`.

|Arquivo de log|Tipo de mensagens armazenadas|
|:--|:--|
|`/var/log/messages`|A maioria das mensagens do syslog é registrada aqui. As exceções são as mensagens sobre a autenticação e ao processamento de e-mails, a execução de tarefas programadas e as mensagens que são apenas relacionadas à depuração.|
|`/var/log/secure`|As mensagens do syslog sobre eventos de segurança e autenticação.|
|`/var/log/maillog`|As mensagens do syslog sobre o servidor de e-mail.|
|`/var/log/cron`|Mensagens do syslog sobre a execução de tarefas programadas.|
|`/var/log/boot.log`|As mensagens do console que não são de syslog sobre a inicialização do sistema.|

Alguns aplicativos não usam o serviço `syslog` para gerenciar suas mensagens de log. Por exemplo, o servidor web Apache salva as mensagens de log em arquivos de um subdiretório do diretório `/var/log`.
