Ao ser criado um arquivo recebe permissões iniciais. Dois fatores afetam essas permissões iniciais. A primeira é se está criando um arquivo ou um diretório, o segundo é o ``umask`` atual, o que significa máscara de criação usuário.

As permissões iniciais de um diretório são ``0777``(``drwxrwxrwx``.). Se você criar um arquivo regular, suas permissões octais iniciais serão ``0666`` (`-rw-rw-rw-`). Sempre se precisa incluir explicitamente a permissão de execução em um arquivo regular. Essa etapa torna mais difícil de um invasor comprometer um sistema.

A sessão do shell define um umask para restringir ainda mais as permissões iniciais de um arquivo. O umask é um bitmask octal que limpa as permissões de novos arquivos e diretórios que um processo cria.

Se um bit for definido no umask, a permissão correspondente será desmarcada nos novos arquivos. Por exemplo, o umask 0002, limpa o bit de gravação para outros usuários.

O comando `umask` sem argumentos exibe o valor atual do umask do shell:
```sh
umask

#0022
```

Use o comando `umask` com apenas um argumento octal para alterar o umask do shell atual. Pode-se ocultar zeros á esquerda.
```sh
umask 077
```

Os valores de umask padrão do sistema para usuários do shell Bash estão definidos nos arquivos `/etc/login.defs` e `/etc/bashrc`.  Os usuários podem substituir os padrões do sistema em seus arquivos `.bash_profile` ou `.bashrc` nos diretórios pessoais.


O usuário `root` pode alterar o umask padrão para shells interativos sem login adicionando um script de inicialização do shell `local-umask.sh` no diretório `/etc/profile.d/`. O seguinte exemplo mostra um arquivo `local-umask.sh`:

```sh
[root@host ~] cat /etc/profile.d/local-umask.sh

# Overrides default umask configuration asda sda
if [ $UID -gt 199 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 007
else
    umask 022
fi
```




































