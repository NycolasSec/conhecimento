Caso o banco de dados esteja em outra máquina devemos especificar o ``host``, também podemos especificar a ``port`` se diferir da padrão.

Se o banco de dados estiver em outro sistema devemos definir um intervalo de tempo maior que o padrão com `connect-timeout`, o número máximo de tentativas de conexão com `max-reconnect-tries`, o tempo em milissegundos que o servidor aguarda entre as tentativas de reconexão ao banco dedados com o `reconnect-wait-time` e o que ele fará se ocorrer a perda de conectividade com o `on-fail`.

E também podemos pedir para que o serviço tente recuperar a conexão na inicialização como ``retry-on-startup``.

Também devemos definir tanto o usuário do banco quanto a senha com `user` e `password`.

```conf
"lease-database":{
        "type": "mysql",
        "host": "db.corp.com",
        "port": "336",
		"name": "kea_leases",
		
		"connect-timeout":     30,
		"max-reconnect-tries": 5,
		"reconnect-wait-time": 1800,
		"on-fails":            "stop-retry-exit",
		"retry-on-startup":    true,
		
		"user":     "db_user",
		"password": "db_password",
		
        "persist":        true,
        "lfc-interval":   1800,
        "max-row-errors": 100
    },
```

Os valores possíveis para o ``on-fail`` são:
- `stop-retry-exit`- desabilita o serviço DHCP enquanto tenta recuperar automaticamente as conexões perdidas e desliga o servidor em caso de falha após esgotar `max-reconnect-tries`. Este é o valor padrão para o backend de lease, o backend de host e o backend de configuração.
- `serve-retry-exit`- continua o serviço DHCP enquanto tenta recuperar automaticamente as conexões perdidas e desliga o servidor em caso de falha após esgotar `max-reconnect-tries`.
- `serve-retry-continue`- continua o serviço DHCP e não desliga o servidor mesmo se a recuperação falhar. Este é o valor padrão para log forense.

---
---
#### Ajustando os tempos limite do banco de dados
Em casos raros, a leitura ou gravação no banco de dados pode travar. Isso pode ser causado por um problema temporário de rede ou por uma configuração incorreta do servidor proxy alternando a conexão entre diferentes instâncias do banco de dados. Definir valores de tempo limite apropriados pode atenuar esses problemas.

O MySQL expõe duas opções de conexão distintas para configurar os timeouts de leitura e gravação. Os parâmetros de configuração `read-timeout`e correspondentes do Kea `write-timeout` especificam os timeouts em segundos. Por exemplo:

```conf
"Dhcp4": {
	"lease-database":{
		"read-timeout" : 10,
		"write-timeout": 20,
		... },
... }
```

Para definir um timeout em segundos para o PostgreSQL, use o `tcp-user-timeout` parâmetro. Por exemplo:

```conf
"Dhcp4": {
	"lease-database":{
		"tcp-user-timeout" : 10,
	...},
... }
```






















































































