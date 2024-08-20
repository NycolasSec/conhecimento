### Modos
**``>``** : Indica modo de usuário.
**`#`** : Indica modo privilegiado.
**`(config) #`** : Indica modo de configuração global.
**`(config-if) #`** : Indica modo de configuração de interface.

#### Saindo dos modos
**`end`** : Volta para a raiz ( **`#`** ).
**`exit`** : Sempre volta um nível nos modos de configuração.

### Memórias
Os equipamentos tem a memória volátil e a não volátil.

A volátil representa a memória que está sendo usada que ao desligar o equipamento, se perde todos os dados.

A memória não volátil ou persistente, permanece no equipamento mesmo ao ser desligado.

---

Também temos dois tipos de configuração, a de running e a de startup.

A configuração running é a que está rodando no equipamento, mas como ela fica na memória volátil, ao desligarmos o equipamento ela se perde.

A configuração de startup, é a  que o equipamento aplica ao ser iniciado. Como é armazenado na memória não volátil, ela não se perde ao desligarmos o equipamento.

---

Para não perdermos as configurações que fazemos, devemos salvar a running na de startup.

```IOS
# copy running startup
ou
# write
```
Com esse comando salvamos as configurações atuais.

```IOS
# show startup-config

# show running-config
```
Exibe as configurações de running e de startup

### Adicionais

```IOS
(config)# hostname R1
```
Muda o nome do equipamento.

```IOS
(config)# no ip domain-lookup
```
Desabilita o serviço de tradução de endereço.