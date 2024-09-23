Tanto arquivos de configurações do Linux quanto arquivos de logs são declarados em texto claro. Por isso podemos visualiza-los com ferramentas de texto simples como `cat`, `less` e filtra-los com `grep`.

![[Pasted image 20240923140932.png]]

```shell
cat auth.log | grep 'session closed for user root'
```
![[Pasted image 20240923140628.png]]

```shell
cat auth.log | grep 'sudo'
```
![[Pasted image 20240923140723.png]]











