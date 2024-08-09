O GraphQL é mais uma interface de API assim como o JSON e o XML. Temos alguns programas para interagir com ele, como :

- GraphQL voyager - Online
- GraphQL Playground

## Introspection
 Se refere a conseguirmos mapear todo o banco relacionado a uma ou várias interfaces.
https://graphql.org/learn/introspection/

O Introspection não é uma vulnerabilidade mas sim um recurso para desenvolvedores, que se não for desabilitado pode ajudar algum atacante a mapear o banco.

**Exemplo de Introspection**
```json

  query IntrospectionQuery {
    __schema {
      queryType { name }
      mutationType { name }
      subscriptionType { name }
      types {
        ...FullType
      }
      directives {
        name
        description
        args {
          ...InputValue
        }
        onOperation
        onFragment
        onField
      }
    }
  }

  fragment FullType on __Type {
    kind
    name
    description
    fields(includeDeprecated: true) {
      name
      description
      args {
        ...InputValue
      }
      type {
        ...TypeRef
      }
      isDeprecated
      deprecationReason
    }
    inputFields {
      ...InputValue
    }
    interfaces {
      ...TypeRef
    }
    enumValues(includeDeprecated: true) {
      name
      description
      isDeprecated
      deprecationReason
    }
    possibleTypes {
      ...TypeRef
    }
  }

  fragment InputValue on __InputValue {
    name
    description
    type { ...TypeRef }
    defaultValue
  }

  fragment TypeRef on __Type {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
        }
      }
    }
  }
```