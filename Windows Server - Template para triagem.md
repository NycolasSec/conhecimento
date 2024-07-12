#### Roles and Features
- `File Server Resource Manager`

---

Vá em **Server Manager > Tools > File Server Resource Manager**.

![[Pasted image 20240709151157.png]]

---

### Grupo

Para criarmos um novo grupo de arquivos para triagem vamos em **File Screening Management > File Group > Create File Group...**
![[Pasted image 20240709163723.png]]

Então podemos escolher o nome do grupo de triagem, quais arquivos ficarão dentro da triagem, ou fora. E damos **OK**
![[Pasted image 20240709164052.png]]

### Template

Para a criação de um template de triagem vamos em **File Screening Management > File Screen Templates > Create File Screen Template...**
![[Pasted image 20240709164904.png]]

Aqui podemos definir o nome do template, se será ativo ou passivo, e quais grupos farão parte desse template.
![[Pasted image 20240709165029.png]]

Aqui definimos se ele lançará uma mensagem de log e qual a mensagem do log, caso alguém tente violar ou viole a triagem
![[Pasted image 20240709165053.png]]

Aqui definimos um script a ser executado caso alguém tente violar ou viole a triagem
![[Pasted image 20240709165111.png]]

### Criando a triagem

Para criarmos a triagem vamos em **File Screening Management > File Screens > Create File Screen...**
![[Pasted image 20240709165755.png]]

E aqui definimos :
- Em qual pasta será aplicada a triagem.
- Qual modelo vamos aplicar, ou se vamos personalizar um.
![[Pasted image 20240709165953.png]]
E vamos em **Create**.