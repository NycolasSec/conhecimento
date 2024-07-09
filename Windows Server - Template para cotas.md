`cota` seria a quantidade de dados que permitimos que o usuário use.

#### Roles and Features
- `File Server Resource Manager

---



Vá em **Server Manager > Tools > File Server Resource Manager**.

![[Pasted image 20240709151157.png]]

### Template

Temos alguns templates já prontos em **Quota Management > Quota Templates**
![[Pasted image 20240709151906.png]]

#### Criando modelo

Para criarmos um template de cota vamos em : **Quota Management > Quota Templates > Create Quota Template...**

![[Pasted image 20240709152233.png]]

Agora temos de preencher as informações do modelo da `quota`
![[Pasted image 20240709152615.png]]
- `Hard quota` : Não permite que o usuário exceda o limite da quota
- `Soft quota` : Permite que o usuário exceda o limite, mas gera log, que podemos monitorar.

Então damos um **OK**

#### Aplicando

Para aplicar esta cota vamos em, **Quotas Management > Quotas > Create Quota...**

![[Pasted image 20240709153240.png]]

Então aqui podemos definir as propriedades da cota.
![[Pasted image 20240709153438.png]]
- `Quota Path` : Onde podemos selecionar a pasta que receberá a quota.
- `Auto apply templated and crate quotas on existing and new subfolders` : Indica que a quota será aplicada nas sub pastas.
- `How do you want to configure quota properties?` : Define se vamos usar um template ou vamos configurar uma configuração exclusiva para aquela quota.

Então vamos em **Create**

---

### Adicionando pasta padrão aos usuários

Selecionamos os usuário e vamos em **Properties**
![[Pasted image 20240709155010.png]]

Então vamos em **Profile** e configuramos
![[Pasted image 20240709155648.png]]
- **\[x] Home folder** : Se vamos habilitar uma pasta base para um usuário
- **Connect** : Indica onde será montada a Pasta Base de cada usuário e qual o caminho dessa pasta.

Então damos **OK**












































































































