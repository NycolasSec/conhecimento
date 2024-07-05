# CMD - Navegação do sistema

Continuando com esse fluxo, nosso próximo objetivo deve ser utilizar nosso prompt de comando para movimentar-se com sucesso no sistema. Nesta seção, tentamos conquistar nosso entorno:

- `dir <caminho>`
Lista o diretório passado como parâmetro, caso não haja parâmetro, ele lista o diretório atual.

- `chdir`
Imprime o caminho absoluto do diretório onde estamos

- `cd`
Caso não receba algum argumento, imprime o diretório atual, caso receba um caminho como argumento, acessará esse caminho

Para economizar um pouco de tempo e ganhar alguma eficiência, podemos obter uma impressão de todo o caminho que especificamos e seus subdiretórios utilizando o comando `tree`.

```cmd
C:\Users\nycol\OneDrive\Documentos\Virtual Machines\Host>tree
```
![[Pasted image 20240704170445.png]]

Podemos utilizar o parâmetro `/F` com o comando tree para ver uma listagem de cada arquivo e os diretórios junto com a árvore de diretórios do caminho.

```cmd
C:\Users\nycol\OneDrive\Documentos\Virtual Machines\Host>tree /F
```
![[Pasted image 20240704170705.png]]

## Diretório interessantes

Abaixo está uma tabela de diretórios comuns que um invasor pode abusar para soltar arquivos no disco, executar reconhecimento e ajudar a facilitar o mapeamento da superfície de ataque em um host alvo.

| Nome:               | Localização:                         | Descrição:                                                                                                                                                                                                                                                                                                            |
| ------------------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| %SYSTEMROOT%\Temp   | `C:\Windows\Temp`                    | Diretório global contendo arquivos temporários do sistema acessíveis a todos os usuários no sistema. Todos os usuários, independentemente da autoridade, recebem permissões totais de leitura, gravação e execução neste diretório. Útil para soltar arquivos como um usuário de baixo privilégio no sistema.         |
| %TEMP%              | `C:\Users\<user>\AppData\Local\Temp` | Diretório local contendo arquivos temporários de um usuário acessíveis somente à conta de usuário à qual ele está anexado. Fornece propriedade total ao usuário que possui esta pasta. Útil quando o invasor obtém controle de uma conta de usuário local/associada ao domínio.                                       |
| %PUBLIC%            | `C:\Users\Public`                    | Diretório acessível publicamente, permitindo que qualquer conta de logon interativa tenha acesso total para ler, escrever, modificar, executar, etc., arquivos e subpastas dentro do diretório. Alternativa ao Diretório Temp global do Windows, pois é menos provável que seja monitorado para atividades suspeitas. |
| %ProgramFiles%      | `C:\Program Files`                   | pasta contendo todos os aplicativos de 64 bits instalados no sistema. Útil para ver que tipo de aplicativos estão instalados no sistema de destino.                                                                                                                                                                   |
| %ProgramFiles(x86)% | `C:\Program Files (x86)`             | Pasta contendo todos os aplicativos de 32 bits instalados no sistema. Útil para ver que tipo de aplicativos estão instalados no sistema de destino.                                                                                                                                                                   |
|                     |                                      |                                                                                                                                                                                                                                                                                                                       |





























