
#### TypeScript

```sh
yarn add typescript -D

yarn add @types/node -D

yarn add tsx -D

npx tsc --init

yarn add fastify

```

**tsconfig.json**
```json
{
	"target":"es2020"
}
```

#### Minimal server
**server.ts**
```js
const app = fastify()

// Routes

app.listen({
    port: 3333
}).then(()=>{
    console.log("HTTP server running on port 3333.")
})

```
#### Prisma
```sh
yarn add prisma -D

npx prisma init --datasource-provider SQLite
```