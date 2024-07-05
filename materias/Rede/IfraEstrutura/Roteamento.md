![[Pasted image 20240704115459.png]]

Com esse comando mostramos a tabela de roteamento do Router
```R1
R1# sh ip route
```
![[Pasted image 20240704115234.png]]

Aqui ele só conhece as interfaces diretamente conectadas nele

## Estático
Para passarmos uma rota estática, temos de fornecer.

- IP da rede de destino Máscara
- Próximo salto ()

```R1
R1(config) ip route
```

