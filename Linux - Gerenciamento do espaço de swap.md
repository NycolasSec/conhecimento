## Conceitos de espaço de swap
O espaço de swap é uma área de disco usada pelo kernel do Linux para complementar a memória RAM do sistema, armazenando páginas inativas de memória. Quando a RAM está cheia, o kernel move páginas inativas para o swap e libera RAM para outros processos. A troca de dados entre a RAM e o disco é lenta em comparação com a RAM, e o espaço de swap não deve ser considerado uma solução para RAM insuficiente.

### Cálculo de espaço de swap
Os administradores devem dimensionar o espaço de troca com base na carga de trabalho de memória no sistema. Os fornecedores de aplicativos às vezes fazem recomendações sobre cálculos de espaço de swap. A tabela a seguir fornece orientações com base no total de memória física.

|RAM|Espaço de troca|Espaço de swap se a hibernação for permitida|
|:--|:--|:--|
|2 GB ou menos|Duas vezes a RAM|Três vezes a RAM|
|Entre 2 GB e 8 GB|O mesmo que a RAM|Duas vezes a RAM|
|Entre 8 GB e 64 GB|Pelo menos 4 GB|1,5 vezes a RAM|
|Mais de 64 GB|Pelo menos 4 GB|Hibernação não recomendada|

A função de hibernação de laptop e desktop usa o espaço de swap para salvar o conteúdo da RAM antes de desligar o sistema. Quando você ativa o sistema novamente, o kernel restaura o conteúdo da RAM a partir do espaço de swap e não precisa de um boot completo. Para esses sistemas, o espaço de swap precisa ser maior que a quantidade da RAM.

## Criação de um espaço de swap
Para criar um espaço de troca, execute as seguintes etapas:
- Crie uma partição com um tipo de sistema de arquivos `linux-swap`.
- Coloque uma assinatura de swap no dispositivo.

### Criação de uma partição de swap
Use o comando `parted` para criar uma partição do tamanho adequado e definir seu tipo de sistema de arquivos como `linux-swap`.
Embora os utilitários não usem mais o tipo de sistema de arquivos de partição, os administradores podem determinar a finalidade da partição a partir desse tipo.

O exemplo a seguir cria uma partição de 256 MB.
```shell-session
[root@host ~]# parted /dev/vdb
```
![[Pasted image 20240830160348.png]]

Depois de criar a partição, execute o comando `udevadm settle`. Esse comando aguarda que o sistema detecte a nova partição e crie o arquivo de dispositivo associado no diretório `/dev`. O comando retorna somente quando é concluído.

### Formatação do espaço de swap
O comando `mkswap` aplica uma assinatura de swap ao dispositivo. Diferentemente de outros utilitários de formatação, o comando `mkswap` grava um único bloco de dados no começo do dispositivo, deixando o resto do dispositivo não formatado para que o kernel o use para armazenar páginas de memória.

```shell-session
[root@host ~]# mkswap /dev/vdb2
```
![[Pasted image 20240830160621.png]]

## Ativação do espaço de swap.
Você pode usar o comando `swapon` para ativar um espaço de swap formatado.

Use `swapon` com o dispositivo como parâmetro, ou use `swapon -a` para ativar todos os espaços de swap listados no arquivo `/etc/fstab`. Use os comandos `swapon --show` e `free` para inspecionar os espaços de swap disponíveis.

```shell-session
[root@host ~]# free
```
![[Pasted image 20240830160922.png]]

```shell-session
[root@host ~]# swapon /dev/vdb2
[root@host ~]# free
```
![[Pasted image 20240830161014.png]]

Você pode desativar um espaço de swap usando o comando `swapoff`. Se as páginas estiverem escritas no espaço de swap, o comando `swapoff` tenta mover essas páginas para outros espaços de swap ativos ou de volta à memória. Se o comando `swapoff` não puder gravar dados em outros lugares, ele falhará apresentando um erro, e o espaço de swap permanecerá ativo.

## Ativação do espaço de swap de modo persistente
Crie uma entrada no arquivo `/etc/fstab` para garantir um espaço de swap ativo no boot do sistema. O exemplo a seguir mostra uma linha típica no arquivo `/etc/fstab` com base no espaço de swap criado anteriormente.

```shell-session
UUID=39e2667a-9458-42fe-9665-c5c854605881   swap   swap   defaults   0 0
```
O exemplo usa a UUID como o primeiro campo. Quando você formata o dispositivo, o comando `mkswap` exibe essa UUID. Se você tiver perdido a saída de `mkswap`, use o comando `lsblk --fs`. Como alternativa, você pode usar o nome do dispositivo no primeiro campo.

O segundo campo geralmente é reservado para o ponto de montagem. Entretanto, para dispositivos de swap, que não são acessíveis por meio da estrutura do diretório, esse campo usa o valor do espaço reservado `swap`. A página do man `fstab(5)` usa um valor de espaço reservado de `none`. No entanto, um valor de `swap` oferece mensagens de erro mais informativas em caso de problemas.

O terceiro campo é o tipo de sistema de arquivos. O tipo de sistema de arquivos para o espaço de swap é `swap`.

O quarto campo é para as opções. O exemplo usa a opção `defaults`. A opção `defaults` inclui a opção de montagem `auto`, o que ativa o espaço de swap automaticamente no boot do sistema.

Os últimos dois campos são o sinalizador de `dump` e o pedido de `fsck`. Os espaços de swap não exigem backup nem verificação do sistema de arquivos e, por isso, esses campos devem ser definidos como zero.

Quando você adiciona ou remove uma entrada no arquivo `/etc/fstab`, execute o comando `systemctl daemon-reload` ou reinicialize o servidor para que o `systemd` registre a nova configuração.

```shell-session
[root@host ~]# systemctl daemon-reload
```

### Definição da prioridade de espaço de swap
Por padrão, o sistema utiliza espaços de swap com base em prioridades. O kernel usa primeiro o espaço de swap com a maior prioridade até que esteja cheio, e então passa a utilizar o espaço de swap com prioridade mais baixa.

Novos espaços de swap recebem prioridade mais baixa em comparação com os mais antigos. Quando os espaços de swap têm a mesma prioridade, o kernel alterna entre eles ao gravar dados.

Para definir a prioridade, use a opção `pri` no arquivo `/etc/fstab`. O kernel usa o espaço de swap com a prioridade mais alta primeiro. A prioridade padrão é -2.

O exemplo a seguir mostra três espaços de swap definidos no arquivo `/etc/fstab`. O kernel usa a última entrada primeiro, pois sua prioridade é definida como 10. Quando esse espaço está cheio, ele usa a segunda entrada, porque sua prioridade é definida como 4. Por fim, ele usa a primeira entrada, que tem uma prioridade padrão de -2.

![[Pasted image 20240830163258.png]]

Use o comando `swapon --show` para exibir as prioridades do espaço de swap.




































