
Caso tenhamos de compartilhas pastas, que estão em **Unidades**  de rede diferentes(\\\\server1 e \\\\server2) em vez de criarmos um compartilhamento de rede pra cada unidade, podemos centralizar esses compartilhamentos com DFS.

---
#### Roles and Features > Role
`File and Storage Services`
	`File and iSCSI Services`
		[ x ] `DFS Namespaces`  


---

## Criando a namespace

**Server Manager > DFS Management**
![[Pasted image 20240711145917.png]]

**DFS Management > Namespace**
![[Pasted image 20240711150510.png]]

Agora fazemos as configurações básicas da Namespace, e depois adicionamos as unidades.

**Namespace Server**
![[Pasted image 20240711151415.png]]
Aqui indicamos em qual servidor ficará localizado.

**Namespace Name and Settings**
![[Pasted image 20240711150827.png]]
Aqui dizemos qual o nome da namespace, e em **Edit Settings** podemos mudar a pasta onde ela ficará localizada, e suas permissões.

**Namespace Name and Settings > Edit Settings**
![[Pasted image 20240711151028.png]]

**Namespace Type**
![[Pasted image 20240711151224.png]]
Podemos definir se vai ser baseada em domínio ou padrão(normalmente para workgroups)

---

## Adicionando pastas

Em **DFS Management > Namespace** selecionamos a nossa namespace e vamos em **New Folder...**

![[Pasted image 20240711153426.png]]

**New Folder**
![[Pasted image 20240711153747.png]]
Podemos ir em **Add...** e então escolher a pasta compartilhada.

**New Folder > Add...**
![[Pasted image 20240711153938.png]]
Podemos colocar o caminho direto, ou colocar o nome do servidor e ir em **Browse...**, então ele mostrará todas as pastas compartilhadas.

**New Folder > Add... > Browse...**
![[Pasted image 20240711154007.png]]
Selecione a pasta e clique em **ok**

Podemos adicionar pastas de servidores diferentes, desde que estejam compartilhados na rede.

Podemos ver o caminho dessa namespace em **DFS Management > Namespaces** selecione a namespace e vá em **Properties**
![[Pasted image 20240711154902.png]]

Aqui são as propriedades da Namespace
![[Pasted image 20240711155104.png]]

---

## Compartilhando via GPO

**Server Manager > Group Policy Management**
![[Pasted image 20240711155257.png]]

Selecionamos o domínio a ser utilizado e vamos em **Create a GPO in this domain, an Link it here...**
![[Pasted image 20240711155618.png]]

**New GPO**
![[Pasted image 20240711155820.png]]
Aqui escolhemos nome da nossa GPO

Agora selecionamos a nossa GPO e vamos em **Edit...**
![[Pasted image 20240711155946.png]]

Aqui temos de achar A configuração **Drive Maps** e criar um novo **Mapped Drive**
![[Pasted image 20240711160338.png]]

Aqui podemos definir configurações do mapeamento de drive que fizemos.
![[Pasted image 20240711160725.png]]
As configurações incluem, a letra de unidade onde será mapeada. A sua Label, e o caminho.

Podemos deixá-la como **Enforced** para ter certeza de que ela estará disponível.
![[Pasted image 20240711161120.png]]

---

Podemos forçar a atualização de política de grupo em uma máquina com o comando : 

```powershell
gpupdate /force
```

---

## Resultado 

![[Pasted image 20240711161623.png]]

![[Pasted image 20240711161639.png]]

































































