Exibe uma lista dos comandos digitados
```bash
$ history
```

Executa o comando pelo número especificado no comando history
```bash
$ !2
```

Mostra a data e o horário atual
```bash
date
```

Mostra a data e o horário atual no formato de 24 horas
```bash
date %R
```

O comando `touch` atualiza o carimbo de data e hora de um arquivo para a data e a hora atuais, sem modificá-lo. Caso o arquivo não exista , ele o cria
```bash
touch info.txt
```

O comando ``mkdir -p`` irá criar os diretórios pais do arquivo que estamos tentando criar.
```bash
mkdir -p Class/Watched
```
Digamos que a pasta `Class` não existe, se usássemos comente o `mkdir` ele apresentaria um erro.

---

Podemos criar dois tipos de links no linux, o link físico e o link simbólico.

O link físico aponta direto para a tabela de inodes do arquivo linkado, então se deletarmos o arquivo original, ele não será excluído da tabela de inode até deletarmos todos os links. Ele só pode linkar arquivos regulares, no mesmo ponto de montagem.

O link simbólico aponta para um arquivo regular, pasta ou arquivo especial em pontos de montagem diferentes. Como ele aponta para o arquivo, se excluirmos o arquivo, ele deixa de existir

Link Físico
```bash
ln newfile.txt /tmp/newfile-hlink2.txt
```

Link Simbólico
```bash
ln -s /home/user/newfile-link2.txt /tmp/newfile-symlink.txt
```





























