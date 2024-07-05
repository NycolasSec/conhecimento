
Primeiro criamos uma pasta e depois vamos em propriedades.
![[Pasted image 20240704153309.png]]

Vamos em **Sharing > Advanced Sharing...**

Marque a checkbox 
- [ ] Share this folder

Acrescente um `$` no nome da pasta, para deixá-la oculta
![[Pasted image 20240704153451.png]]

Depois vamos em **Permissions**, removemos todos os usuários que tem acesso e vamos em **add...**, colocamos domain e vamos em **Check Names**
![[Pasted image 20240704154056.png]]

Então escolhemos os **Domains Users** e clicamos em **OK**
![[Pasted image 20240704154246.png]]

Então devemos dar permissão total para esse usuário
![[Pasted image 20240704154419.png]]

Até aqui, todos os usuários que fazem parte do domínio tem acesso as pastas e as sub pastas.

Podemos mudar isso para que somente o usuário que criou a pasta possa acessá-la e ter acesso aos seus arquivos

---

Para isso vamos nas **Propriedades > Segurança > Avançadas**

Por padrão qualquer sub pasta que for criada dentro da pasta base herdará as propriedades da pasta pai.

Vamos em **Desabilitar Herança**

![[Pasted image 20240704155817.png]]

![[Pasted image 20240704155858.png]]

Depois removeremos os **Users**, deixando somente o **System**, **Administrator** e o **CREATOR OWNER**
![[Pasted image 20240704160005.png]]

Então damos ok

































