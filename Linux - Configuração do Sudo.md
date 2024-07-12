O arquivo `/etc/sudoers` é o arquivo de configuração principal para o comando `sudo`, você só poderá editá-lo com o comando especial `visudo`.

Por exemplo, a linha a seguir do arquivo `/etc/sudoers` permite acesso `sudo` aos membros do grupo `wheel`.

```txt
%wheel        ALL=(ALL:ALL)       ALL
```

- A string `%wheel` é o usuário ou grupo ao qual a regra se aplica. O símbolo `%` antes da palavra `wheel` especifica um grupo.
- O comando `ALL=(ALL:ALL)` especifica que em qualquer host com esse arquivo (o primeiro `ALL`), os usuários no grupo `wheel` podem executar comandos como qualquer outro usuário (o segundo `ALL`) e como qualquer outro grupo (o terceiro `ALL`) no sistema.
- O comando final `ALL` especifica que os usuários no grupo `wheel` podem executar qualquer comando.

Por padrão, o arquivo `/etc/sudoers` também inclui o conteúdo de qualquer arquivo no diretório `/etc/sudoers.d` como parte do arquivo de configuração. Assim podemos criar um arquivo apropiado para cada acesso que queremos liberar.


Para permitir acesso `sudo` total para o usuário `user01`, você pode criar o arquivo `/etc/sudoers.d/user01` com o seguinte conteúdo:
```txt
user01        ALL=(ALL)       ALL
```


Para permitir acesso `sudo` total para o grupo `group01`, você pode criar o arquivo `/etc/sudoers.d/group01` com o seguinte conteúdo:
```txt
%group01        ALL=(ALL)       ALL
```


Para permitir que os usuários no grupo `games` executem o comando `id` como o usuário `operator`, você pode criar o arquivo `/etc/sudoers.d/games` com o seguinte conteúdo:
```txt
%games ALL=(operator) /bin/id
```


Você também pode configurar `sudo` para permitir que um usuário execute comandos como outro usuário sem digitar sua senha, usando o comando `NOPASSWD: ALL`:
```txt
ansible        ALL=(ALL)       `NOPASSWD: ALL`
```














































