Você gerencia os GPOs usando duas ferramentas principais:

- Console de Gerenciamento da Política de Grupo
- O Editor de Gerenciamento de Política de Grupo

Você também pode usar cmdlets do Windows PowerShell para gerenciar GPOs e suas configurações, incluindo os descritos na tabela a seguir.

|**Cmdlet**|**Descrição**|
|---|---|
|`New-GPO`|Cria um novo GPO.|
|`New-GPLink`|Vincula um GPO a um site, domínio ou UO.|
|`Get-GPInheritance`|Obtém informações de herança da Política de Grupo para um domínio ou uma UO específicos.|
|`Set-GPInheritance`|Bloqueia ou desbloqueia herança para um domínio ou unidade organizacional específicos.|
|`Get-GPO`|Obtém um GPO ou todos os GPOs em um domínio.|

Há duas categorias principais de configurações de política: configurações do computador, que estão contidas no nó **Configuração do Computador** e configurações do usuário, que estão contidas no nó **Configuração do Usuário**:

- O nó **Configuração do Computador** contém as configurações que se aplicam a computadores, independentemente de quem faz logon neles. As configurações do computador são aplicadas quando o sistema operacional é iniciado, durante as atualizações em segundo plano e, depois, a cada 90 a 120 minutos.
- O nó **Configuração do Usuário** contém configurações que se aplicam quando um usuário faz logon em um computador, durante as atualizações em segundo plano e, depois, a cada 90 a 120 minutos.

Nos nós **Configuração do Computador** e **Configuração do Usuário** estão os nós **Políticas** e **Preferências**.

Os nós **Políticas** em Configuração do Computador e Configuração do Usuário têm uma hierarquia de pastas que contêm configurações de política. Como há milhares de configurações, o escopo deste curso não inclui configurações individuais. No entanto, vale a pena examinar os tipos de configurações que você pode configurar.

## Aplicar configurações de segurança
No sistema operacional Windows Server, os GPOs incluem muitas configurações relacionadas à segurança que você pode aplicar a usuários e computadores.

Por exemplo, você pode impor configurações à política de senha do domínio e ao Windows Defender Firewall e pode configurar a auditoria e outras configurações de segurança. Você também pode configurar conjuntos completos de atribuições de direitos de usuário.

## Gerenciar configurações de desktop e aplicativo
Você pode usar Política de Grupo para oferecer um ambiente de aplicativo e de área de trabalho consistente a todos os usuários da sua organização. Com GPOs, você pode definir cada configuração que afete a representação do ambiente do usuário. Você também pode definir configurações para alguns aplicativos que dão suporte a GPOs.

## Implantar software
Com a Política de Grupo, você pode implantar o software para usuários e computadores. Você pode usar a Política de Grupo para implantar todos os softwares disponíveis no formato ``.msi``.

Além disso, você pode impor a instalação automática de software ou permitir que os usuários decidam se desejam que o software seja implantado em seus computadores.

>[!important]
>A implantação de grandes pacotes de software com GPOs pode não ser a maneira mais eficiente de distribuir um aplicativo nos computadores da sua organização. Em algumas circunstâncias, pode ser mais eficiente distribuir aplicativos como parte da imagem do computador desktop.

## Gerenciar o Redirecionamento de Pasta
**Objetivo**: O Redirecionamento de Pasta permite que você centralize os arquivos de dados dos usuários em um servidor de rede em vez de armazená-los localmente em seus computadores.

- **Benefícios**:
    
    - **Backup Simplificado**: Facilita a realização de backup dos arquivos de dados dos usuários.
    - **Acesso Universal**: Permite que os usuários acessem seus dados independentemente do computador em que estão conectados.
    - **Centralização**: Armazena os dados dos usuários em um local centralizado, melhorando a organização e a segurança.
- **Exemplo**: Redirecionar a pasta **Documentos** dos usuários para uma pasta compartilhada em um servidor de rede.
    

## Definindo as Configurações de Rede
**Objetivo**: Usar a Política de Grupo para configurar várias opções de rede em computadores cliente.

- **Configurações de Rede Sem Fio**: Imponha a conexão com SSIDs específicos e configure autenticação e criptografia predefinidas.
- **Configurações de Rede Com Fio**: Configure as opções de rede com fio, incluindo serviços como DirectAccess, que utilizam a Política de Grupo para configurar o lado do cliente.

## Solucionando Problemas com a Aplicação de GPOs
A aplicação das configurações de Política de Grupo pode ser complexa devido a heranças, filtros e exceções. O **RSoP** (Resultado da Política de Grupo) é uma ferramenta que ajuda a entender e solucionar esses problemas.

- **O que é RSoP**: O RSoP mostra o efeito combinado dos GPOs aplicados a um usuário ou computador, considerando:
    
    - **Vinculações de GPO**: GPOs aplicados em diferentes níveis (site, domínio, UO).
    - **Exceções**: Imposto e Bloquear Herança.
    - **Filtros**: Filtros WMI e de segurança.
- **Ferramentas RSoP**:
    
    - **Assistente de Resultados de Política de Grupo**: Analisa as configurações exatas aplicadas a um computador e usuário.
    - **Assistente de Modelagem de Política de Grupo**: Modela as configurações de política para diversos cenários, como mudanças de UO ou associações de grupo.
    - **GPResult.exe**: Ferramenta de linha de comando que fornece um relatório detalhado das configurações de política aplicadas.















