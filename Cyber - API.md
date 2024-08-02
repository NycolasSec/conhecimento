## Tipos de API

- REST
- GRAPHQL
- SOAP

### REST
O modelo de API mais utilizado hoje pelas aplicações web e apps. O modelo REST pode ser utilizado com vários padrões como HTTP, JSON, XML e URL.

**Exemplo :**
```REST
GET:http://api.crowsec.com.br/users/1
```

```REST
DELETE:http://api.crowsec.com.br/users
```

```REST
POST:http://api.crowsec.com.br/users
{"name":"Carlos", "password":"senha@123"}
```

### GRAPHQL
``GraphQL`` é uma linguagem de consulta criada pelo Facebook em 2012. O padrão ``GraphQL`` é HTTP com requisições ``POST`` e ``GET``.

**Exemplo :**
```GraphQL
{
	users{
		id,
		name
	}
}
```

### SOAP
Foi o modelo de API mais utilizado no passado pelas aplicações web. O modelo SOAP utiliza HTTP e XML.

![[Pasted image 20240802125717.png]]

![[Pasted image 20240802125809.png]]

