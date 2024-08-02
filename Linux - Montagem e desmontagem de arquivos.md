
Lista os detalhes de um determinado dispositivo de blocos ou de todos os dispositivos disponíveis.
```bash
lsblk
```
![[Pasted image 20240729074626.png]]

```bash
mount /dev/vda4 /mnt/data
```
Monta o dispositivo ``/dev/vda4`` nas pasta ``/mnt/data``

Podemos listar o caminho completo do dispositivo, com as UUIDs e os pontos de montagem, bem como o tipo de sistema de arquivos da partição.
```bash
lsblk -fp
```
![[Pasted image 20240729074812.png]]

Podemos montar um dispositivo pela sua UUID, apenas especificando com o argumento UUID.
```bash
mount UUID="efd314d0-b56e-45db-bbb3-3f32ae98f652" /mnt/data
```


## Desmontagem

O comando `umount` desmonta os pontos de montagem.
```bash
umount /mnt/data
```

O comando `lsof` lista todos os arquivos abertos e os processos que estão acessando o sistema de arquivos. A lista ajuda a identificar quais são os processos que estão atualmente impedindo o sistema de arquivos de obter êxito na desmontagem.
```bash
lsof /mnt/data
```
![[Pasted image 20240729075203.png]]































