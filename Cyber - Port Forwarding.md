
```sh
ssh -L 8080:localhost:8080 user@srv.corp

sudo lsof -i:8080
```

`sudo lsof -i:8080`
Verifica qual serviço está rodando nesta porta.
