Contas de usuário inativas e senhas sem data de validade no Active Directory Domain Services (AD DS) representam um risco significativo de segurança para o ambiente. Verificar e gerenciar essas contas é uma prática recomendada que ajuda a evitar acessos não autorizados e a manter o controle sobre o uso de credenciais.

### Contas de Usuário Inativas:

- **Identificação de Contas Inativas**: Contas de usuário que não foram utilizadas para logon durante um período específico podem pertencer a funcionários que deixaram a organização ou a contas que foram criadas para um propósito temporário e não foram removidas. Essas contas são uma vulnerabilidade, pois podem ser exploradas por invasores sem que os administradores percebam, especialmente se ainda estiverem ativas.
- **Ação Recomendável**: Quando contas inativas são identificadas, a melhor prática é desabilitá-las. Isso não exclui a conta, mas impede seu uso até que seja reativada. Caso a pessoa retorne à organização, a conta pode ser reativada rapidamente, sem precisar ser recriada do zero.

### Contas com Senhas que Não Expiram:

- **Risco de Senhas Estáticas**: Contas com senhas sem data de expiração são vulneráveis a longos períodos de exposição, uma vez que, se a senha for comprometida, o invasor pode mantê-la indefinidamente. Isso é especialmente perigoso para contas privilegiadas, como administradores de domínio ou serviços críticos, que têm acesso amplo aos recursos do AD e à rede.
- **Ação Recomendável**: É importante identificar essas contas e revisar suas configurações de senha. Idealmente, todas as contas devem estar sujeitas a uma política de expiração de senha, onde os usuários são forçados a alterá-las periodicamente. A imposição de atualizações de senha é uma medida de segurança fundamental para reduzir o tempo em que uma senha comprometida pode ser usada.

### Boas Práticas de Gerenciamento:

1. **Desativar Contas Inativas**: Contas que não são usadas por um período, como 90 dias, devem ser desativadas. Isso pode ser feito automaticamente com ferramentas de auditoria ou políticas no AD.
2. **Imposição de Políticas de Senha**: Revise as políticas de senha e certifique-se de que todas as contas, especialmente as de administradores e outros usuários com altos privilégios, estejam sujeitas a políticas rigorosas de expiração de senha. As contas com senhas fixas devem ser atualizadas regularmente.
3. **Monitoramento Contínuo**: Implementar monitoramento contínuo para identificar contas que se tornem inativas ou que tenham políticas de senha inadequadas, mantendo o ambiente seguro e controlado.

Você pode usar o Windows PowerShell ou o Centro Administrativo do AD DS para encontrar usuários problemáticos. Para usar o Windows PowerShell a fim de localizar usuários ativos com senhas definidas para nunca expirar, use o seguinte comando:
```powershell
Get-ADUser -Filter {Enabled -eq $true -and PasswordNeverExpires -eq $true}
```

Use o seguinte comando do Windows PowerShell para localizar usuários que não se conectaram nos últimos 90 dias:
```powershell
$age = (Get-Date).AddDays(-90);Get-ADUser -Filter {LastLogonTimeStamp -lt $age -and enabled -eq $true} -Properties LastLogonTimeStamp
```