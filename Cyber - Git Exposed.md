Se refere a exposição inadequada dos arquivos do versionamento do git.
## Intro

Git é uma solução para versionamento de projetos, criando um repositório local com várias versões do projeto.

No git temos as ``branchs`` que é uma nova versão do projeto, normalmente criadas para corrigir um erro ou testar uma nova feature sem alterar o projeto original, caso haja sucesso no objetivo da branch, a branch seria integrada ao projeto principal.

## Comandos

```sh
git status
```
Mostra o status do repositório. Caso haja algum arquivo marcado como `Untracked` significa de que ele não está na nossa "linha do tempo" da branch atual e que o git não o está monitorando.

```sh
git add [file]
```
Adiciona um arquivo a linha do tempo da branch atual. Assim o git irá o monitorar.

```sh
git commit -m "Mensagem do commit."
```
Adiciona um ponto na linha do tempo da branch atual, para que possamos voltar a ele caso necessário.

> OBS: Caso façamos alguma alteração relevante devemos fazer um commit, para que possamos restaurá-lo para o ponto criado, caso haja algum erro ou para consultarmos versões do código anteriores.

Retorna o histórico de alterações( commits ) da branch atual.
```sh
git log
```
![[Pasted image 20240802172342.png]]

Podemos usar o ID do commit para obtermos informações do commit.
```sh
git show [id do commit]
```
![[Pasted image 20240802172711.png]]

Podemos também voltar o projeto para um commit em específico usando seu id
```sh
git checkout [id do commit]
```

## Exploração
Caso haja arquivos do git expostos inadequadamente, poderemos explorara isso.

https://github.com/arthaud/git-dumper

A ferramenta ``gitdumper`` baixa arquivos git comuns, para que possamos analisar.

**Exemplo :**
```sh
python3 dit_dumper.py http://corp.hc/ arquivos_git
```

Ele irá baixar os arquivos git que ele achar e vai jogar dentro da pasta `arquivos_git`

























