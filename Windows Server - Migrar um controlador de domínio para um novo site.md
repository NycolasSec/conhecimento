Gerenciar **sites e sub-redes** é essencial para a administração de um domínio corporativo, especialmente quando a empresa expande ou muda sua estrutura. Ao lançar um novo local, as etapas principais incluem:

1. **Criar um novo site** para agrupar objetos com base na localização física.
2. **Adicionar uma sub-rede** para garantir que todos os recursos do novo site estejam na mesma rede.
3. **Migrar um controlador de domínio** para o novo site, o que ajuda a reduzir o tráfego de internet e o atraso em consultas de autenticação e autorização com sites distantes.

## Criar um novo site
O primeiro passo para configurar o novo local é criar um novo site, para que todos os computadores, usuários e outros objetos possam ser associados ao novo local.

Para criar um novo site no console Sites e Serviços do Active Directory:
1. Abra o console **Sites e Serviços do Active Directory** .
2. Na árvore do console, clique com o botão direito do mouse em Sites e selecione **Novo Site** .
3. Dê um nome ao novo site, associe-o ao link do site apropriado e clique em **OK** .

Como alternativa, você pode criar um novo site usando o comando do PowerShell da seguinte maneira.

```powershell
New-ADReplicationSite -Name "Ludington" -Description "Site for new facility in Ludington MI."
```

## Mapear o novo site para uma sub-rede
Para atribuir o site recém-criado a uma sub-rede:

1. No console **Sites e Serviços do Active Directory** , expanda a pasta **Sites** para ver a lista de sites.
2. Clique com o botão direito em **Sub-redes** e selecione **Nova sub-rede** .
3. Insira o endereço de sub-rede e a máscara e selecione o site que você criou na lista suspensa.
4. Selecione **OK** para associar a sub-rede ao seu novo site.

Para usar o PowerShell para mapear um novo site para uma sub-rede, você pode usar o seguinte comando.

```
New-ADReplicationSubnet -Name "192.168.1.0/24" -Site "Ludington"
```

## Mover um controlador de domínio para um novo site

Para mover um controlador de domínio para o novo site:

1. No console **Sites e Serviços do Active Directory** , expanda o site que atualmente contém o controlador de domínio que você deseja mover.
2. Expanda a pasta **Servidores** e, em seguida, expanda o servidor que representa o controlador de domínio.
3. **Clique com o botão direito do mouse no objeto Configurações NTDS** do controlador de domínio e selecione **Mover** .
4. Na caixa de diálogo **Mover servidor** , selecione o novo site para o qual deseja mover o controlador de domínio e selecione **OK** .

Assim como você pode usar o PowerShell para criar um novo site e mapeá-lo para uma sub-rede, você pode usar o PowerShell para mover um controlador de domínio.

```powershell
Move-ADDirectoryServer -Identity "DC2.WestMI" -Site "Ludington"
```














