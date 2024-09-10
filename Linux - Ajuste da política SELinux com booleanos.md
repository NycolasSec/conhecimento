### Valores booleanos do SELinux

Os booleanos do SELinux permitem ajustar o comportamento opcional de uma política SELinux para aplicativos e serviços. Cada política pode incluir comportamentos que podem ser ativados ou desativados conforme necessário. Esses comportamentos opcionais são específicos do aplicativo e devem ser descobertos e selecionados para cada aplicativo de destino.

Os booleanos específicos do serviço estão documentados na página do man do SELinux desse serviço. Os booleanos são documentados nas páginas do manual dos serviços, como `httpd_selinux(8)` para o servidor web httpd, e podem ser gerenciados com os comandos `getsebool` e `setsebool`. O `getsebool` lista os booleanos disponíveis e seu status atual, enquanto `setsebool` altera o estado dos booleanos, com a opção `-P` para tornar as mudanças persistentes. Somente usuários com privilégios podem modificar esses valores.

```shell-session
[root@host ~]# getsebool -a
```
![[Pasted image 20240909181555.png]]

#### Gerenciamento do booleano da política

A configuração de valores booleanos do SELinux com o comando `setsebool` sem a opção `-P` é temporária, e as configurações retornam aos valores persistentes após a reinicialização. Visualize informações adicionais com o comando `semanage boolean -l`, que lista os booleanos dos arquivos de política, incluindo se um booleano é persistente, os valores padrão e atuais e uma breve descrição.

```shell-session
[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs          (off   ,  off)  Allow httpd to enable homedirs

[root@host ~]# setsebool httpd_enable_homedirs on

[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs          (on   ,  off)  Allow httpd to enable homedirs

[root@host ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> on
```

Para listar apenas booleanos com uma configuração atual que seja diferente da configuração padrão no boot, use o comando `semanage boolean -l -C`. Este exemplo tem o mesmo resultado que o exemplo anterior, sem exigir a filtragem `grep`.
```shell-session
[root@host ~]# semanage boolean -l -C
```
![[Pasted image 20240909181910.png]]

O exemplo anterior definiu temporariamente o valor atual do booleano `httpd_enable_homedirs` como `on`, até que o sistema seja reinicializado. Para alterar a configuração padrão, use o comando `setsebool -P` para tornar a configuração persistente.

O exemplo a seguir define um valor persistente e, em seguida, exibe as informações do booleano do arquivo de política.
```shell-session
[root@host ~]# setsebool -P httpd_enable_homedirs on

[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
```
![[Pasted image 20240909182012.png]]

Use o comando `semanage boolean -l -C` novamente. O valor booleano é exibido apesar da aparência de que as configurações atuais e padrão são as mesmas. No entanto, a opção `-C` corresponde quando a configuração atual é diferente da configuração padrão do último boot. Para este exemplo `httpd_enable_homedirs`, a configuração de boot padrão original era `off`.

```shell-session
[root@host ~]# semanage boolean -l -C
```
![[Pasted image 20240909182115.png]]







































