Remete a códigos que não validam o tipo de um dado.

Digamos que temos um campo de usuário na página de atualização, se esse campo não for tratado em questão do tipo, poderíamos passar um array de usuários. Desse modo o código percorreria o array de usuários.

**Exemplo em node.js**
```js
<...SNIP>

app.post('login', async (req, res)=>{
	const {username, password} = req.body
	
	const currentUser = Users.findOne({
		username,
		password
	})
})

<...SNIP>
```

No caso do mongoose, ele aceita que façamos condições nas queries. Se o tipo de dados não for tratado, poderemos fazer uma solicitação maliciosa :
```js
{
	"username":"admin",
	"password":{"$regex":".*"}
}
```

Aqui passamos o usuário *admin* e no `password` passamos uma expressão regular. 

**`{"$regex":".*"}`** : Indica se a senha deu match com qualquer string.



























