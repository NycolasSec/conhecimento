## Descrição
O _console da web_ é uma interface de gerenciamento baseada na web para o Red Hat Enterprise Linux. A interface foi desenvolvida para gerenciar e monitorar seus servidores e é baseada no serviço de open source Cockpit.

## Instalação e ativação
```bash
dnf install cockpit
systemctl enable --now cockpit.socket
```

Se estiver usando um perfil de firewall personalizado, você deve adicionar o serviço `cockpit` a `firewalld` para abrir a porta `9090` no firewall:
```bash
firewall-cmd --add-service=cockpit --permanent

firewall-cmd --reload
```