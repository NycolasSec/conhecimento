## Manter a disponibilidade do controlador de domínio do AD DS
 Como prática recomendada, um domínio do AD DS deve ter pelo menos dois controladores de domínio por site do AD DS. Isso torna o banco de dados do AD DS mais disponível e espalha a carga de autenticação durante horários de entrada de pico.

## Planejar o backup e a restauração do AD DS
Manter a confiabilidade dos dados do Active Directory é importante. Os backups regulares podem desempenhar uma função nesse processo, mas saber como restaurar ou recuperar dados após uma falha é essencial.

### Restaurando objetos do AD DS excluídos usando a lixeira
Windows Server oferece o recurso **Active Directory Recycle Bin**, com um método simples de restauração de objetos excluídos sem tempo de inatividade do AD DS. Depois de habilitar o **Active Directory Recycle Bin**, o contêiner de objetos excluídos é exibido no Centro Administrativo do Active Directory.

Objetos excluídos persistirão nesse contêiner até que o tempo de vida deles expire. Para novas implantações do AD DS, esse tempo de vida é definido como 180 dias, mas você tem a opção de alterá-lo. Você pode optar por restaurar os objetos para o local original ou para um alternativo dentro do AD DS.

### Backup e restauração do AD DS
Para restaurar o AD DS, um backup deve incluir explicitamente os dados de estado do sistema. O _estado do sistema_ é uma coleção de arquivos críticos de sistema operacional e de função de servidor que inclui o banco de dados do AD DS e o registro.

Para uma restauração do AD DS, você deve ter acesso completo aos arquivos no controlador de domínio. Isso requer reiniciar o controlador de domínio no DSRM. Para reiniciar um controlador de domínio localmente, abra as opções avançadas de inicialização e escolha o DSRM no menu.

Ao iniciar um controlador de domínio no DSRM, você entrará como administrador com a senha do DSRM. Você pode usar o Backup do Windows Server para restaurar o banco de dados do diretório.

#### Restauração não autoritativa
Por padrão, você restaura um backup do AD DS a partir de uma data válida conhecida, revertendo ele a um estado anterior. O controlador de domínio é atualizado com o restante do domínio usando mecanismos de replicação padrão.

Esse tipo de restauração será útil quando o diretório em um controlador de domínio tiver sido danificado ou corrompido, mas o problema não se espalhar a outros controladores. Em alguns cenários, essa abordagem não é adequada. Por exemplo, isso não permitirá que você recupere um objeto excluído após o backup se essa exclusão tiver sido replicada para outros controladores de domínio.

Se você restaurar uma versão válida do AD DS e reiniciar o controlador de domínio, a exclusão que aconteceu após o backup será simplesmente replicada de volta no controlador.

#### Restauração autoritativa
Uma restauração autoritativa permite restaurar uma cópia válida conhecida de objetos do AD DS, que substitui a versão atual deles no banco de dados do AD DS. Em uma restauração autoritativa, você começa com a mesma sequência de etapas da restauração não autoritativa.

No entanto, antes de reiniciar o controlador de domínio, você marca os objetos restaurados que deseja manter como autoritativos para que eles sejam replicados da saída do controlador restaurado em seus parceiros de replicação.