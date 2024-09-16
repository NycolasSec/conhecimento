`Credential Hunting` é o processo de executar pesquisas detalhadas no sistema de arquivos e por meio de vários aplicativos para descobrir credenciais.

## Pesquisa Centrada
Muitas das ferramentas disponíveis para nós no Windows têm funcionalidade de pesquisa. Pode até haver credenciais padrão que podem ser encontradas em vários arquivos. Seria sensato basear nossa busca por credenciais no que sabemos sobre como o sistema de destino está sendo usado. Nesse caso, sabemos que temos acesso à estação de trabalho de um administrador de TI.

`What might an IT admin be doing on a day-to-day basis & which of those tasks may require credentials?`

Podemos usar essa pergunta e consideração para refinar nossa busca e reduzir ao máximo a necessidade de suposições aleatórias.

#### Termos-chave para pesquisar
Quer acabemos com acesso à GUI ou CLI, sabemos que teremos algumas ferramentas para usar na busca, mas de igual importância é o que exatamente estamos buscando. Aqui estão alguns termos-chave úteis que podemos usar para nos ajudar a descobrir algumas credenciais:

|   |   |   |
|---|---|---|
|Senhas|Frases-senha|Chaves|
|Nome de usuário|Conta de usuário|Créditos|
|Usuários|Chaves de acesso|Frases-senha|
|configuração|credencial db|senha do banco de dados|
|Pcd|Conecte-se|Credenciais|

Vamos usar alguns desses termos-chave para pesquisar na estação de trabalho do administrador de TI.

## Ferramentas de pesquisa
Com acesso à GUI, vale a pena tentar usar `Windows Search` para encontrar arquivos no alvo usando algumas das palavras-chave mencionadas acima.

![[Pasted image 20240913193732.png]]

Por padrão, ele pesquisará em várias configurações do sistema operacional e no sistema de arquivos por arquivos e aplicativos que contenham o termo-chave inserido na barra de pesquisa.

Também podemos aproveitar ferramentas de terceiros como [o Lazagne](https://github.com/AlessandroZ/LaZagne) para descobrir rapidamente credenciais que navegadores da web ou outros aplicativos instalados podem armazenar de forma insegura.

Podemos usar nosso cliente RDP para copiar o arquivo para o alvo a partir de nosso host de ataque. Se estivermos usando, `xfreerdp`tudo o que precisamos fazer é copiar e colar na sessão RDP que estabelecemos.

#### executando Lazagne All

```shell-session
C:\Users\bob\Desktop> start lazagne.exe all
```

Isso executará o Lazagne e executará `all`os módulos incluídos. Podemos incluir a opção `-vv`de estudar o que ele está fazendo em segundo plano. Assim que pressionarmos enter, ele abrirá outro prompt e exibirá os resultados.

#### Saída Lazagne
![[Pasted image 20240913194325.png]]

Se usássemos a `-vv`opção, veríamos tentativas de coletar senhas de todos os softwares suportados pelo Lazagne

#### Usando findstr
Também podemos usar [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr) para pesquisar padrões em muitos tipos de arquivos. Mantendo em mente termos-chave comuns, podemos usar variações deste comando para descobrir credenciais em um alvo do Windows:
```shell-session
C:\> findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

## Considerações adicionais
Existem milhares de ferramentas e termos-chave que podemos usar para procurar credenciais em sistemas operacionais Windows. Saiba que quais escolheremos usar serão baseados principalmente na função do computador. Se chegarmos a um sistema operacional Windows Server, podemos usar uma abordagem diferente do que se chegarmos a um sistema operacional Windows Desktop. Esteja sempre atento a como o sistema está sendo usado, e isso nos ajudará a saber onde procurar. Às vezes, podemos até encontrar credenciais navegando e listando diretórios no sistema de arquivos enquanto nossas ferramentas são executadas.

Aqui estão alguns outros lugares que devemos ter em mente ao procurar credenciais:

- Senhas na Política de Grupo no compartilhamento SYSVOL
- Senhas em scripts no compartilhamento SYSVOL
- Senha em scripts em compartilhamentos de TI
- Senhas em arquivos web.config em máquinas de desenvolvimento e compartilhamentos de TI
- unattend.xml
- Senhas nos campos de descrição do usuário ou computador do AD
- Bancos de dados KeePass --> extraia hash, quebre e obtenha muito acesso.
- Encontrado em sistemas de usuários e compartilhamentos
- Arquivos como pass.txt, passwords.docx, passwords.xlsx encontrados em sistemas de usuários, compartilhamentos, [Sharepoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration)

Você obteve acesso à estação de trabalho Windows 10 de um administrador de TI e iniciou seu processo de busca de credenciais pesquisando-as em locais de armazenamento comuns.