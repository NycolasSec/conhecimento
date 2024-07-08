Aqui verificamos se o serviço está instalado
```bash
sudo dnf list firewalld
```

Aqui verificamos se o serviço está ativo
```bash
sudo systemctl status firealld
```

primeiro vemos a página de ajuda e depois verificamos quais serviços estão liberados
```bash
sudo firewall-cmd --help
sudo firewall-cmd --list-all
```

---

```bash
sudo systemctl disable firewalld
sudo systemctl enable firewalld
```

1. Desabilitamos o ``firewaald`` para que não inicie com o servidor
2. Habilitamos o `firewlld` para que inicie junto com o servior.

---

Adicionamos uma regra ao firewall
```bash
firewall-cmd --add-service=ssh --permanent
```

reiniciamos o serviço
```bash
firewall-cmd --reload
```

