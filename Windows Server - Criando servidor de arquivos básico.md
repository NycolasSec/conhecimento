## Recursos

Em **Add Roles and Features Wizard** vá em **Next >**
![[Pasted image 20240709141900.png]]

Escolha **Role-based or features-based installation > Next >**
![[Pasted image 20240709142125.png]]

Escolha o servidor vá em **Next >**
![[Pasted image 20240709142315.png]]

Em **Server Roles > File Storage Services > File and ISCSI Services > File Server Resource Manager > Next >**
![[Pasted image 20240709142444.png]]

**Add Features**
![[Pasted image 20240709142700.png]]

Após isto, apenas confirmes nas próximas telas

---

## Compartilhando pastas

Primeiro criamos as pastas a serem compartilhadas

![[Pasted image 20240709143210.png]]

Na pasta vá em **Properties**
![[Pasted image 20240709143340.png]]

Vá em **Sharing > Advanced Sharing... > \[ x ]Share this folder **
![[Pasted image 20240709143515.png]]

Agora temos que tirar a permissão de **Everyone**, pois não queremos que todos tenham acesso.
**Permissions > Everyone > Remove**

![[Pasted image 20240709143818.png]]

Em seguida vamos adicionar os usuários do domínio.
**Add... > Domain Users > OK**

![[Pasted image 20240709144208.png]]

---

## Herança

Para definirmos as heranças de permissão vamos em **Properties > Security > Advanced**

![[Pasted image 20240709144620.png]]

Então desabilitamos a herança e excluímos os usuários padrão
**Disable inheritance**
**Remove**
![[Pasted image 20240709144921.png]]




















































































