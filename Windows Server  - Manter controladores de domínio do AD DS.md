Há aspectos operacionais aplicáveis a todos os ambientes do AD DS ( Active Directory Domain Services) que se concentram em manter a continuidade de negócios dos serviços de autenticação. Isso inclui o backup e a recuperação dos controladores de domínio e dos objetos do AD DS hospedados por eles.

## Manter a disponibilidade do controlador de domínio do AD DS
Os controladores de domínio usam um processo de replicação de vários mestres para copiar dados de um controlador de domínio para outro. Como prática recomendada, um domínio do AD DS deve ter pelo menos dois controladores de domínio por site do AD DS. Isso torna o banco de dados do AD DS mais disponível e espalha a carga de autenticação durante horários de entrada de pico.

>[!info]
>Para a maioria das empresas, considere dois controladores de domínio por região geográfica como o mínimo absoluto para ajudar a garantir a alta disponibilidade e o desempenho.

## Planejar o backup e a restauração do AD DS
Manter a confiabilidade dos dados do Active Directory é importante. Os backups regulares podem desempenhar uma função nesse processo, mas saber como restaurar ou recuperar dados após uma falha é essencial.

### Restaurando objetos do AD DS excluídos usando a lixeira
O **Active Directory Recycle Bin** no Windows Server permite a restauração de objetos excluídos sem causar tempo de inatividade do sistema. Após habilitar esse recurso, o contêiner "Objetos Excluídos" no Centro Administrativo do Active Directory exibirá os objetos excluídos, que permanecem lá até o término do tempo de vida (padrão de 180 dias, mas ajustável).

Para restaurar itens excluídos:

1. Abra o Centro Administrativo do Active Directory.
2. Navegue até o contêiner "Objetos Excluídos" no nó de domínio.
3. Selecione os objetos que deseja recuperar.
4. Clique com o botão direito na seleção e escolha **Restaurar** para restaurar no local original, ou **Restaurar para** para especificar um novo local.

>[!info]
>Você não pode usar o Active Directory Recycle Bin para reverter as alterações em objetos existentes. Nesses casos, você deve usar os métodos tradicionais de backup e restauração do AD DS.

### Backup e restauração do AD DS
Para restaurar o AD DS, um backup deve incluir explicitamente os dados de estado do sistema. O <i>estado do sistema</i> é uma coleção de arquivos críticos de sistema operacional e de função de servidor que inclui o banco de dados do AD DS e o registro.

>[!important]
>Um backup de servidor completo que é usado para a recuperação completa do servidor não oferece suporte a esse cenário.

#### Restauração do AD DS usando DSRM (Modo de Restauração de Diretório Seguro):

1. **Acesso Completo:** Para restaurar o AD DS, reinicie o controlador de domínio no DSRM, acessando as opções avançadas de inicialização e selecionando DSRM.
2. **Autenticação:** No DSRM, entre com a senha do DSRM e use o Backup do Windows Server para restaurar o banco de dados do diretório.
3. **Reinício e Replicação:** Após a restauração, reinicie o servidor. O controlador de domínio atualizará seu banco de dados com alterações feitas no diretório desde o backup, usando replicação padrão.

#### Tipos de Restauração:

- **Restauração Não Autoritativa:**
    - Restaura o AD DS a partir de um backup, revertendo o controlador de domínio para um estado anterior.
    - Após reiniciar, o controlador solicita atualizações dos parceiros de replicação, que podem incluir mudanças (como exclusões) feitas após o backup.
    - Útil quando o problema é local ao controlador de domínio e não se espalhou para outros controladores.
- **Restauração Autoritativa:**
    - Restaura uma cópia válida de objetos e marca esses objetos como autoritativos, para que sejam replicados para outros controladores de domínio.
    - Útil para garantir que a versão restaurada dos objetos seja a versão final replicada, substituindo versões mais recentes nos outros controladores de domínio.
















































