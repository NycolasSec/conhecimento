### Contexto inicial do SELinux
Todos os recursos no SELinux, como processos, arquivos e portas, são rotulados com um contexto SELinux específico. Esses contextos são gerenciados com base em políticas de rotulagem localizadas no diretório `/etc/selinux/targeted/contexts/files/`. 

- **Rotulagem de Novos Arquivos**: Quando novos arquivos são criados, eles recebem um rótulo padrão baseado na política de rotulagem existente para o nome do arquivo. Se o nome do arquivo não corresponde a uma política, o arquivo herda o rótulo do diretório pai.

- **Herança de Rotulagem**: Arquivos sempre recebem um rótulo devido à herança, mesmo na ausência de uma política explícita para o arquivo.

- **Mudança de Contexto ao Copiar Arquivos**: Copiar um arquivo para um novo local pode alterar seu contexto SELinux, que será baseado na política de rotulagem do novo local ou herdado do diretório pai. Para preservar o contexto SELinux original, use o comando `cp -p` (para preservar todos os atributos) ou `cp --preserve=context` (para preservar somente o contexto SELinux).

O exemplo a seguir demonstra como esse processo funciona.

Crie dois arquivos no diretório `/tmp`. Ambos os arquivos recebem o tipo de contexto `user_tmp_t`.

Mova o primeiro arquivo e copie o segundo arquivo para o diretório `/var/www/html`.
- O arquivo movido retém o contexto do arquivo que foi rotulado do diretório original `/tmp`.
- O arquivo copiado tem um novo ``inode`` e herda o contexto SELinux do diretório de destino `/var/www/html`.

O comando `ls -Z` exibe o contexto SELinux de um arquivo. Observe o rótulo dos arquivos criados no diretório `/tmp`.

```shell-session
[root@host ~]# touch /tmp/file1 /tmp/file2
[root@host ~]# ls -Z /tmp/file*
```
![[Pasted image 20240909172348.png]]

O comando `ls -Zd` exibe o contexto SELinux de um diretório especificado. Observe o rótulo no diretório `/var/www/html` e os arquivos dentro dele.
```shell-session
[root@host ~]# ls -Zd /var/www/html/
system_u:object_r:httpd_sys_content_t:s0 /var/www/html/

[root@host ~]# ls -Z /var/www/html/index.html
unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/index.html
```

Mova um arquivo do diretório `/tmp` para o diretório `/var/www/html`. Copie o outro arquivo para o mesmo diretório. Observe o rótulo resultante em cada arquivo.
```shell-session
[root@host ~]# mv /tmp/file1 /var/www/html/

[root@host ~]# cp /tmp/file2 /var/www/html/

[root@host ~]# ls -Z /var/www/html/file*
unconfined_u:object_r:user_tmp_t:s0 /var/www/html/file1
unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/file2
```

O arquivo movido manteve seu rótulo original e o arquivo copiado herdou o rótulo do diretório de destino. `unconfined_u` é a função de usuário SELinux, `object_r` é a função do SELinux e `s0` é o nível de sensibilidade (mais baixo possível). As configurações e os recursos avançados do SELinux usam esses valores.

### Alteração do contexto SELinux
Você pode gerenciar o contexto SELinux nos arquivos com os comandos `semanage fcontext`, `restorecon` e `chcon`.

Para alterar o contexto de um arquivo no SELinux de maneira recomendada e gerenciável:

1. **Criar uma Política de Contexto**: Use o comando `semanage fcontext` para definir uma política de contexto de arquivo. Isso especifica o contexto SELinux desejado para um determinado caminho de arquivo.

2. **Aplicar o Contexto**: Aplique o contexto definido na política usando o comando `restorecon`. Isso garante que o arquivo seja rotulado corretamente conforme a política e permite que o contexto seja reaplicado quando necessário.

   - **Vantagem**: O uso desses comandos garante que você possa rotular corretamente os arquivos e aplicar o contexto conforme definido na política, mesmo após reinicializações e modificações do sistema.

3. **Alteração Temporária com `chcon`**: O comando `chcon` altera o contexto SELinux diretamente nos arquivos, mas sem referência à política SELinux. Embora seja útil para testes e depuração, as alterações feitas com `chcon` são temporárias e podem ser substituídas quando o comando `restorecon` é executado para aplicar a política de contexto.

O exemplo a seguir cria um diretório com um contexto SELinux `default_t`, que ele herdou do diretório pai `/`.
```shell-session
[root@host ~]# mkdir /virtual
[root@host ~]# ls -Zd /virtual
```
![[Pasted image 20240909173509.png]]

A execução do comando `restorecon` redefine o contexto para o valor padrão de `default_t`. Observe a mensagem `Relabeled`.
```shell-session
[root@host ~]# restorecon -v /virtual
```
![[Pasted image 20240909173601.png]]

```shelll-session
[root@host ~]# ls -Zd /virtual
```
![[Pasted image 20240909173631.png]]

### Definição das políticas de contexto do arquivo SELinux padrão

O comando `semanage fcontext` é usado para exibir e modificar as políticas que definem os contextos de arquivo padrão no SELinux.

- **Listar Políticas**: Use `semanage fcontext -l` para listar todas as regras de política de contexto de arquivo. Essas regras usam sintaxe de expressões regulares estendidas para especificar caminhos e nomes de arquivos.
    
- **Sintaxe de Expressão Regular**: A expressão regular estendida mais comum é `(/.*)?`, que geralmente é usada em nomes de diretórios. Essa sintaxe, que se assemelha a um "pirata" (um rosto com um tapa-olho e uma mão de gancho), corresponde ao próprio diretório e a quase qualquer nome de arquivo criado dentro dele. Em termos simples, essa notação cobre o diretório em si e todos os arquivos dentro dele.

Por exemplo, a regra a seguir especifica que o diretório `/var/www/cgi-bin` e todos os arquivos nele ou em seus subdiretórios (e seus subdiretórios e assim por diante) têm o contexto SELinux `system_u:object_r:httpd_sys_script_exec_t:s0`, a menos que uma regra mais específica substitua essa.
```txt
/var/www/cgi-bin(/.*)?  all files  system_u:object_r:httpd_sys_script_exec_t:s0
```

#### Operações de contexto básicas com arquivos
A tabela a seguir é uma referência para as opções do comando `semanage fcontext` adicionarem, removerem ou listarem políticas de contextos de arquivos SELinux.

|Opção|Descrição|
|:--|:--|
|`-a, --add`|Adiciona um registro do tipo de objeto especificado.|
|`-d, --delete`|Exclui um registro do tipo de objeto especificado.|
|`-l, --list`|Lista registros do tipo de objeto especificado.|

Para gerenciar contextos SELinux, instale os pacotes `policycoreutils` e `policycoreutils-python-utils`, que contêm os comandos `restorecon` e `semanage`.

Para redefinir os arquivos em um diretório para o contexto de política padrão, siga estas etapas:

1. **Verifique a Política de Contexto:** Use o comando `semanage fcontext -l` para localizar e verificar se a política de contexto correta para o diretório e seus arquivos está definida.
    
2. **Redefina os Contextos de Arquivo:** Use o comando `restorecon` no nome do diretório com caracteres curinga para aplicar o contexto padrão a todos os arquivos e subdiretórios de forma recursiva.

**Exemplo:**

- **Antes de aplicar a política:** Verifique os contextos de arquivo usando comandos como `ls -Z` para observar os contextos atuais.
    
- **Depois de aplicar a política:** Após executar `restorecon`, os contextos dos arquivos serão atualizados para corresponder à política padrão.

Essa abordagem garante que todos os arquivos e subdiretórios dentro de um diretório sejam rotulados de acordo com a política de SELinux apropriada.

Primeiro, verifique o contexto SELinux para os arquivos:
```shell-session
[root@host ~]# ls -Z /var/www/html/file*
```
![[Pasted image 20240909175332.png]]

Em seguida, use o comando `semanage fcontext -l` para listar os contextos padrão do arquivo SELinux:
```shell-session
[root@host ~]# semanage fcontext -l
```
![[Pasted image 20240909175356.png]]

A saída do comando `semanage` indica que todos os arquivos e subdiretórios no diretório `/var/www/` têm o contexto `httpd_sys_content_t` por padrão.

A execução do comando `restorecon` no diretório com caracteres curinga restaura o contexto padrão em todos os arquivos e subdiretórios.

```shell-session
[root@host ~]# restorecon -Rv /var/www/
```
![[Pasted image 20240909175552.png]]

```shell-session
[root@host ~]# ls -Z /var/www/html/file*
```
![[Pasted image 20240909175615.png]]


O exemplo a seguir usa o comando `semanage` para adicionar uma política de contexto para um novo diretório. Primeiro, crie o diretório `/virtual` com um arquivo `index.html` dentro dele. Veja o contexto SELinux para o arquivo e o diretório.
```shell-session
[root@host ~]# mkdir /virtual
[root@host ~]# touch /virtual/index.html
[root@host ~]# ls -Zd /virtual/
```
![[Pasted image 20240909175704.png]]

```shell-session
[root@host ~]# ls -Z /virtual/
```
![[Pasted image 20240909175732.png]]

Em seguida, use o comando `semanage fcontext` para adicionar uma política de contexto de arquivo SELinux para o diretório.
```shell-session
[root@host ~]# semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'
```

Use o comando `restorecon` no diretório com caracteres curinga para definir o contexto padrão no diretório e todos os arquivos dentro dele.

```shell-session
[root@host ~]# restorecon -RFvv /virtual
```
![[Pasted image 20240909175838.png]]

```shell-session
[root@host ~]# ls -Zd /virtual/
```
![[Pasted image 20240909175903.png]]

```shell-session
[root@host ~]# ls -Z /virtual/
```
![[Pasted image 20240909175926.png]]

Use o comando `semanage fcontext -l -C` para visualizar qualquer personalização local para a política padrão.

```shell-session
[root@host ~]# semanage fcontext -l -C
```
![[Pasted image 20240909175956.png]]












































































