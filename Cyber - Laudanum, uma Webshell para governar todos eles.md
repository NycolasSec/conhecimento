**Laudanum** é um repositório de arquivos injetáveis usados para obter acesso a sistemas alvos e executar comandos. Ele inclui arquivos para diversas linguagens de aplicativos web, como ASP, ASPX, JSP e PHP. Integrado por padrão ao Parrot OS e Kali Linux, pode ser baixado para outras distribuições. É uma ferramenta útil para pentesting, permitindo injeção e controle remoto através de shells reversos e outras técnicas.

## Trabalhando com Laudanum
Os arquivos do Laudanum estão localizados em `/usr/share/laudanum`. Você pode copiar e usar esses arquivos diretamente na vítima, mas para shells e similares, é necessário editar o arquivo para inserir o IP do seu host. Certifique-se de revisar o conteúdo e os comentários dos arquivos antes de usá-los.

## Demonstração de Laudanum
```shell-session
NycolasRamos@htb[/htb]$ cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```
Adicione seu endereço IP à variável `allowedIps` na linha `59`. Faça quaisquer outras alterações que desejar.

#### Modificar o Shell para uso
![[Pasted image 20240830111816.png]]

Estamos aproveitando a função de upload na parte inferior da página de status( `Green Arrow`) para que isso funcione. Selecione seu arquivo shell e clique em upload. Se for bem-sucedido, ele deve imprimir o caminho para onde o arquivo foi salvo (Seta Amarela). Use a função de upload. O sucesso imprime para onde o arquivo foi, navegue até ele.

#### Aproveite a função de upload
![[Pasted image 20240830111940.png]]

Você precisará navegar até seu shell da web para utilizar suas funções. Na última imagem, nosso shell foi carregado no diretório `\\files\`, e o nome foi mantido o mesmo. Com este aplicativo da web em particular, nosso arquivo foi para `status.inlanefreight.local\\files\demo.aspx`, exigirá que naveguemos para o upload usando esse \ no caminho em vez de / como de costume. Depois que você fizer isso, seu navegador o limpará na janela de URL para aparecer como `status.inlanefreight.local//files/demo.aspx`.

#### Navegue até o nosso Shell
![[Pasted image 20240830112238.png]]
Agora podemos utilizar o shell Laudanum que carregamos para emitir comandos para o host. Podemos ver no exemplo que o `systeminfo`comando foi executado.

#### Sucesso da Shell
![[Pasted image 20240830112311.png]]


























