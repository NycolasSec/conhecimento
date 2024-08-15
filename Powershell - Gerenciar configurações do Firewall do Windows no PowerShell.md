O PowerShell dá suporte ao módulo **NetSecurity** que contém cmdlets para gerenciar configurações locais de Segurança de Rede, como regras de firewall do Windows e configurações de segurança de IP.

Para gerenciar as configurações de firewall, use cmdlets que têm o texto "NetFirewall" nos nomes. Para o gerenciamento de regras de firewall, use cmdlets que contêm o substantivo "NetFirewallRule".

A tabela a seguir lista cmdlets comuns para gerenciar as configurações e as regras de firewall.

| Cmdlet                      | Descrição                                      |
| --------------------------- | ---------------------------------------------- |
| **New-NetFirewallRule**     | Cria uma nova regra de firewall                |
| **Set-NetFirewallRule**     | Define propriedades para uma regra de firewall |
| **Get-NetFirewallRule**     | Obtém propriedades para uma regra de firewall  |
| **Remove-NetFirewallRule**  | Exclui uma regra de firewall                   |
| **Rename-NetFirewallRule**  | Renomeia uma regra de firewall                 |
| **Copy-NetFirewallRule**    | Faz uma cópia de uma regra de firewall         |
| **Enable-NetFirewallRule**  | Habilita uma regra de firewall                 |
| **Disable-NetFirewallRule** | Desabilita uma regra de firewall               |
| **Get-NetFirewallProfile**  | Obtém propriedades para um perfil de firewall  |
| **Set-NetFirewallProfile**  | Define propriedades para um perfil de firewall |

Você pode usar o cmdlet **Get-NetFirewallRule** para recuperar as configurações de regras de firewall. Você pode habilitar e desabilitar regras usando um dos seguintes cmdlets:

- O cmdlet **Set-NetFirewallRule** com o parâmetro _-Enabled_
- Os cmdlets **Enable-NetFirewallRule** ou **Disable-NetFirewallRule**.

Ambos os comandos a seguir habilitam regras de firewall no grupo **Acesso Remoto**:

```powershell
Enable-NetFirewallRule -DisplayGroup "Remote Access"
```
e
```powershell
Set-NetFirewallRule -DisplayGroup "Remote Access" -Enabled True
```

























































































