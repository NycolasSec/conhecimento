``sudo dnf upgrade refresh``
## RHEL

No **RedHat** o nome do serviço que gerencia o ssh é `sshd`, então podemos estartar ele dessa forma.

Iniciando o `sshd`
```bash
systemctl start sshd
```

Ma ao rebootarmos a máquina ele irar parar o serviço, pois ele é desabilitado por padrão. Podemos automatizar sua inicialização.

Automatizando a inicialização
```bash
systemctl enable sshd
```

### Configuração

A configuração do ``ssh`` é feita nos arquivos

- `/etc/ssh/ssh_config` - Para o cliente ssh
- `/etc/ssh/sshd_config` - Para o servidor ssh

#### /etc/ssh/sshd_config
```txt
Port 22
ListenAddress 0.0.0.0
ListenAddress ::
PermitRootLogin yes
MaxSession 2
PasswordAuthentication yes
```

Essas são algumas configurações possíveis


































































































