## CentOS
```sh
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

**Troque o BOOTPROTO PARA static e coloque o ip e a mascara**
```txt
BOOTPROTO=static
IPADDR=192.168.1.100
NETMASK=255.255.255.0
```


**Agora edite o arquivo abaixo para adicionar o gateway:**
```sh
vim /etc/sysconfig/network
```

```txt
GATEWAY=192.168.1.1
```


**E para finalizar é necessario configurar o dns:**
```txt
vim /etc/resolv.conf
```

```txt
nameserver 8.8.8.8
nameserver 8.8.4.4
```


**Para finalizar de um restart**
```txt
/etc/init.d/network restart
```