## Armazenamento de diário do sistema
O Red Hat Enterprise Linux 9 armazena o diário do sistema no diretório `/run/log` e é limpo após uma reinicialização. Pode-se alterar as configurações do serviço `systemd-journald` no arquivo `/etc/systemd/journald.conf` para manter os diários após uma reinicialização.

O parâmetro `Storage` no arquivo `/etc/systemd/journald.conf` define se os diários do sistema devem ser armazenados de maneira volátil ou persistente em uma reinicialização. Defina esse parâmetro como `persistent`, `volatile`, `auto` ou `none` desta forma:

- `persistent`: armazena os diários no diretório `/var/log/journal`, que é mantido nas reinicializações. Se `/var/log/journal` não existir, o serviço `systemd-journald` criará o diretório.
    
- `volatile`: armazena os diários no diretório `/run/log/journal` volátil. Como o sistema de arquivos `/run` é temporário e existe somente na memória de tempo de execução, os dados armazenados nele, inclusive os diários do sistema, não são mantidos na reinicialização.
    
- `auto`: se o diretório `/var/log/journal` existir, o serviço `systemd-journald` usará o armazenamento persistente, caso contrário, usará o armazenamento volátil. Essa ação será o padrão se o parâmetro `Storage` não estiver definido.
    
- `none`: não usa nenhum armazenamento. O sistema descarta todos os logs, mas você ainda pode encaminhar os logs.

Diários do sistema persistente permitem acesso imediato a dados históricos no boot, mas não mantêm todos os dados indefinidamente. O rodízio de logs ocorre mensalmente, e os diários não ocupam mais de 10% do sistema de arquivos nem deixam menos de 15% do espaço livre. Esses limites podem ser ajustados no arquivo de configuração **/etc/systemd/journald.conf**.

O processo `systemd-journald` registra os limites atuais de tamanho do diário quando é iniciado. A seguinte saída do comando mostra as entradas de diário que refletem os limites atuais de tamanho:

```bash-session
[user@host ~]$ journalctl | grep -E 'Runtime Journal|System Journal'
Mar 15 04:21:14 localhost systemd-journald[226]: Runtime Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 113.3M, 105.3M free.
Mar 15 04:21:19 host.lab.example.com systemd-journald[719]: Runtime Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 113.3M, 105.3M free.
Mar 15 04:21:19 host.lab.example.com systemd-journald[719]: System Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 4.0G, 4.0G free.
```

### Configuração de diários do sistema persistentes
Configure o serviço `systemd-journald` deste modo para manter os diários do sistema de modo persistente em uma reinicialização:

- Crie o diretório `/var/log/journal`.
```bash-session
[root@host ~]# mkdir /var/log/journal
```

- Defina o parâmetro `Storage` como o valor `persistent` no arquivo `/etc/systemd/journald.conf`. Execute o editor de texto de sua escolha como superusuário para editar arquivo `/etc/systemd/journald.conf`.
```bash-session
[Journal]
Storage=persistent
...output omitted...
```

- Reinicie o serviço `systemd-journald` para aplicar as alterações de configuração.
```bash-session
[root@host ~]# systemctl restart systemd-journald
```

Se o serviço `systemd-journald` for reiniciado com êxito, o serviço criará subdiretórios no diretório `/var/log/journal`. O subdiretório no diretório `/var/log/journal` tem caracteres hexadecimais em seu nome longo e contém arquivos com a extensão `.journal`.

Os arquivos binários `.journal` armazenam as entradas de diário estruturadas e indexadas.
```bash-session
[root@host ~]# ls /var/log/journal
4ec03abd2f7b40118b1b357f479b3112

[root@host ~]# ls /var/log/journal/4ec03abd2f7b40118b1b357f479b3112
system.journal  user-1000.journal
```

A saída do comando `journalctl` inclui entradas do boot atual do sistema e dos boots anteriores do sistema. Para limitar a saída a um boot específico do sistema, use a opção `journalctl` do comando `-b`.

O seguinte comando `journalctl` recupera somente as entradas do primeiro boot do sistema:
```bash-session
[root@host ~]# journalctl -b 1
...output omitted...
```

O seguinte comando `journalctl` recupera somente as entradas do segundo boot do sistema. O argumento é relevante apenas se o sistema foi reinicializado pelo menos duas vezes:
```bash-session
[root@host ~]# journalctl -b 2
...output omitted...
```

Você pode listar os eventos de boot do sistema que o comando `journalctl` reconhece usando a opção `--list-boots`.
```bash-session
[root@host ~]# journalctl --list-boots
  -6 27de... Wed 2022-04-13 20:04:32 EDT—Wed 2022-04-13 21:09:36 EDT
  -5 6a18... Tue 2022-04-26 08:32:22 EDT—Thu 2022-04-28 16:02:33 EDT
  -4 e2d7... Thu 2022-04-28 16:02:46 EDT—Fri 2022-05-06 20:59:29 EDT
  -3 45c3... Sat 2022-05-07 11:19:47 EDT—Sat 2022-05-07 11:53:32 EDT
  -2 dfae... Sat 2022-05-07 13:11:13 EDT—Sat 2022-05-07 13:27:26 EDT
  -1 e754... Sat 2022-05-07 13:58:08 EDT—Sat 2022-05-07 14:10:53 EDT
   0 ee2c... Mon 2022-05-09 09:56:45 EDT—Mon 2022-05-09 12:57:21 EDT
```

O seguinte comando `journalctl` recupera somente as entradas do boot atual do sistema:
```bash-session
[root@host ~]# journalctl -b
...output omitted...
```









