[John the Ripper](https://github.com/openwall/john) ( `JTR`ou `john`) é uma ferramenta essencial de pentesting usada para verificar a força de senhas e quebrar senhas criptografadas (ou com hash) usando ataques de força bruta ou de dicionário.

Com isso, podemos usar várias ferramentas para converter diferentes tipos de arquivos e hashes em um formato que seja utilizável por John. Além disso, o software é atualizado regularmente para acompanhar as tendências e tecnologias de segurança atuais, garantindo a segurança do usuário.

## Tecnologias de Criptografia
|**Tecnologia de Criptografia**|**Descrição**|
|---|---|
|`UNIX crypt(3)`|Crypt(3) é um sistema de criptografia UNIX tradicional com uma chave de 56 bits.|
|`Traditional DES-based`|A criptografia baseada em DES usa o algoritmo Data Encryption Standard para criptografar dados.|
|`bigcrypt`|Bigcrypt é uma extensão da criptografia tradicional baseada em DES. Ele usa uma chave de 128 bits.|
|`BSDI extended DES-based`|A criptografia BSDI estendida baseada em DES é uma extensão da criptografia tradicional baseada em DES e usa uma chave de 168 bits.|
|`FreeBSD MD5-based`(Linux e Cisco)|A criptografia baseada em MD5 do FreeBSD usa o algoritmo MD5 para criptografar dados com uma chave de 128 bits.|
|`OpenBSD Blowfish-based`|A criptografia baseada em Blowfish do OpenBSD usa o algoritmo Blowfish para criptografar dados com uma chave de 448 bits.|
|`Kerberos/AFS`|Kerberos e AFS são sistemas de autenticação que usam criptografia para garantir a comunicação segura entre entidades.|
|`Windows LM`|A criptografia do Windows LM usa o algoritmo Data Encryption Standard para criptografar dados com uma chave de 56 bits.|
|`DES-based tripcodes`|Os códigos de viagem baseados em DES são usados ​​para autenticar usuários com base no algoritmo Data Encryption Standard.|
|`SHA-crypt hashes`|Hashes SHA-crypt são usados ​​para criptografar dados com uma chave de 256 bits e estão disponíveis em versões mais recentes do Fedora e do Ubuntu.|
|`SHA-crypt`e `SUNMD5 hashes`(Solaris)|Os hashes SHA-crypt e SUNMD5 usam os algoritmos SHA-crypt e MD5 para criptografar dados com uma chave de 256 bits e estão disponíveis no Solaris.|
|`...`|e muitos mais.|

## Métodos de Ataque

#### Ataques de dicionário
Ataques de dicionário envolvem o uso de uma lista pré-gerada de palavras e frases (conhecida como dicionário) para tentar quebrar uma senha.

#### Ataques de Força Bruta
Ataques de força bruta envolvem tentar todas as combinações concebíveis de caracteres que podem formar uma senha.

#### Ataques de mesa de arco-íris
Ataques de tabela arco-íris envolvem o uso de uma tabela pré-computada de hashes e suas senhas de texto simples correspondentes, o que é um método muito mais rápido do que um ataque de força bruta.

## Modos de Cracking

`Single Crack Mode`é um dos modos John mais comuns usados ​​ao tentar quebrar senhas usando uma única lista de senhas. É um ataque de força bruta, o que significa que todas as senhas na lista são tentadas, uma por uma, até que a correta seja encontrada.

No entanto, está longe de ser o método mais eficiente, pois pode levar um tempo indefinido para quebrar uma senha, dependendo do tamanho e da complexidade da senha em questão. A sintaxe básica para o comando é:

#### Modo de crack único
```shell-session
NycolasRamos@htb[/htb]$ john --format=<hash_type> <hash or hash_file>
```

Por exemplo, se tivermos um arquivo chamado `hashes_to_crack.txt`que contém `SHA-256`hashes, o comando para quebrá-los seria:
```shell-session
NycolasRamos@htb[/htb]$ john --format=sha256 hashes_to_crack.txt
```

- `john`é o comando para executar o programa John the Ripper
- `--format=sha256`especifica que o formato de hash é SHA-256
- `hashes.txt`é o nome do arquivo que contém os hashes a serem quebrados

O processo de quebra de senhas pode ser `very time-consuming`, pois a quantidade de tempo necessária para quebrar uma senha depende de vários fatores, como a complexidade da senha, a configuração da máquina e o tamanho da lista de palavras.


John irá gerar as senhas quebradas no console e o arquivo "john.pot" ( `~/.john/john.pot`) no diretório home do usuário atual. Além disso, ele continuará quebrando os hashes restantes em segundo plano, e podemos verificar o progresso executando o `john --show`comando. Para maximizar as chances de sucesso, é importante garantir que as listas de palavras e regras usadas sejam abrangentes e atualizadas.

#### Quebrando com John

|**Hash Format**|**Example Command**|**Description**|
|---|---|---|
|afs|`john --format=afs hashes_to_crack.txt`|AFS (Andrew File System) password hashes|
|bfegg|`john --format=bfegg hashes_to_crack.txt`|bfegg hashes used in Eggdrop IRC bots|
|bf|`john --format=bf hashes_to_crack.txt`|Blowfish-based crypt(3) hashes|
|bsdi|`john --format=bsdi hashes_to_crack.txt`|BSDi crypt(3) hashes|
|crypt(3)|`john --format=crypt hashes_to_crack.txt`|Traditional Unix crypt(3) hashes|
|des|`john --format=des hashes_to_crack.txt`|Traditional DES-based crypt(3) hashes|
|dmd5|`john --format=dmd5 hashes_to_crack.txt`|DMD5 (Dragonfly BSD MD5) password hashes|
|dominosec|`john --format=dominosec hashes_to_crack.txt`|IBM Lotus Domino 6/7 password hashes|
|EPiServer SID hashes|`john --format=episerver hashes_to_crack.txt`|EPiServer SID (Security Identifier) password hashes|
|hdaa|`john --format=hdaa hashes_to_crack.txt`|hdaa password hashes used in Openwall GNU/Linux|
|hmac-md5|`john --format=hmac-md5 hashes_to_crack.txt`|hmac-md5 password hashes|
|hmailserver|`john --format=hmailserver hashes_to_crack.txt`|hmailserver password hashes|
|ipb2|`john --format=ipb2 hashes_to_crack.txt`|Invision Power Board 2 password hashes|
|krb4|`john --format=krb4 hashes_to_crack.txt`|Kerberos 4 password hashes|
|krb5|`john --format=krb5 hashes_to_crack.txt`|Kerberos 5 password hashes|
|LM|`john --format=LM hashes_to_crack.txt`|LM (Lan Manager) password hashes|
|lotus5|`john --format=lotus5 hashes_to_crack.txt`|Lotus Notes/Domino 5 password hashes|
|mscash|`john --format=mscash hashes_to_crack.txt`|MS Cache password hashes|
|mscash2|`john --format=mscash2 hashes_to_crack.txt`|MS Cache v2 password hashes|
|mschapv2|`john --format=mschapv2 hashes_to_crack.txt`|MS CHAP v2 password hashes|
|mskrb5|`john --format=mskrb5 hashes_to_crack.txt`|MS Kerberos 5 password hashes|
|mssql05|`john --format=mssql05 hashes_to_crack.txt`|MS SQL 2005 password hashes|
|mssql|`john --format=mssql hashes_to_crack.txt`|MS SQL password hashes|
|mysql-fast|`john --format=mysql-fast hashes_to_crack.txt`|MySQL fast password hashes|
|mysql|`john --format=mysql hashes_to_crack.txt`|MySQL password hashes|
|mysql-sha1|`john --format=mysql-sha1 hashes_to_crack.txt`|MySQL SHA1 password hashes|
|NETLM|`john --format=netlm hashes_to_crack.txt`|NETLM (NT LAN Manager) password hashes|
|NETLMv2|`john --format=netlmv2 hashes_to_crack.txt`|NETLMv2 (NT LAN Manager version 2) password hashes|
|NETNTLM|`john --format=netntlm hashes_to_crack.txt`|NETNTLM (NT LAN Manager) password hashes|
|NETNTLMv2|`john --format=netntlmv2 hashes_to_crack.txt`|NETNTLMv2 (NT LAN Manager version 2) password hashes|
|NEThalfLM|`john --format=nethalflm hashes_to_crack.txt`|NEThalfLM (NT LAN Manager) password hashes|
|md5ns|`john --format=md5ns hashes_to_crack.txt`|md5ns (MD5 namespace) password hashes|
|nsldap|`john --format=nsldap hashes_to_crack.txt`|nsldap (OpenLDAP SHA) password hashes|
|ssha|`john --format=ssha hashes_to_crack.txt`|ssha (Salted SHA) password hashes|
|NT|`john --format=nt hashes_to_crack.txt`|NT (Windows NT) password hashes|
|openssha|`john --format=openssha hashes_to_crack.txt`|OPENSSH private key password hashes|
|oracle11|`john --format=oracle11 hashes_to_crack.txt`|Oracle 11 password hashes|
|oracle|`john --format=oracle hashes_to_crack.txt`|Oracle password hashes|
|pdf|`john --format=pdf hashes_to_crack.txt`|PDF (Portable Document Format) password hashes|
|phpass-md5|`john --format=phpass-md5 hashes_to_crack.txt`|PHPass-MD5 (Portable PHP password hashing framework) password hashes|
|phps|`john --format=phps hashes_to_crack.txt`|PHPS password hashes|
|pix-md5|`john --format=pix-md5 hashes_to_crack.txt`|Cisco PIX MD5 password hashes|
|po|`john --format=po hashes_to_crack.txt`|Po (Sybase SQL Anywhere) password hashes|
|rar|`john --format=rar hashes_to_crack.txt`|RAR (WinRAR) password hashes|
|raw-md4|`john --format=raw-md4 hashes_to_crack.txt`|Raw MD4 password hashes|
|raw-md5|`john --format=raw-md5 hashes_to_crack.txt`|Raw MD5 password hashes|
|raw-md5-unicode|`john --format=raw-md5-unicode hashes_to_crack.txt`|Raw MD5 Unicode password hashes|
|raw-sha1|`john --format=raw-sha1 hashes_to_crack.txt`|Raw SHA1 password hashes|
|raw-sha224|`john --format=raw-sha224 hashes_to_crack.txt`|Raw SHA224 password hashes|
|raw-sha256|`john --format=raw-sha256 hashes_to_crack.txt`|Raw SHA256 password hashes|
|raw-sha384|`john --format=raw-sha384 hashes_to_crack.txt`|Raw SHA384 password hashes|
|raw-sha512|`john --format=raw-sha512 hashes_to_crack.txt`|Raw SHA512 password hashes|
|salted-sha|`john --format=salted-sha hashes_to_crack.txt`|Salted SHA password hashes|
|sapb|`john --format=sapb hashes_to_crack.txt`|SAP CODVN B (BCODE) password hashes|
|sapg|`john --format=sapg hashes_to_crack.txt`|SAP CODVN G (PASSCODE) password hashes|
|sha1-gen|`john --format=sha1-gen hashes_to_crack.txt`|Generic SHA1 password hashes|
|skey|`john --format=skey hashes_to_crack.txt`|S/Key (One-time password) hashes|
|ssh|`john --format=ssh hashes_to_crack.txt`|SSH (Secure Shell) password hashes|
|sybasease|`john --format=sybasease hashes_to_crack.txt`|Sybase ASE password hashes|
|xsha|`john --format=xsha hashes_to_crack.txt`|xsha (Extended SHA) password hashes|
|zip|`john --format=zip hashes_to_crack.txt`|ZIP (WinZip) password hashes

#### Modo de lista de palavras
`Wordlist Mode`é usado para quebrar senhas usando várias listas de palavras. É um ataque de dicionário, o que significa que ele tentará todas as palavras nas listas, uma por uma, até encontrar a correta.
```shell-session
NycolasRamos@htb[/htb]$ john --wordlist=<wordlist_file> --rules <hash_file>
```

#### Modo incremental
`Incremental Mode`é um modo John avançado usado para quebrar senhas usando um conjunto de caracteres. É um ataque híbrido, o que significa que ele tentará corresponder à senha tentando todas as combinações possíveis de caracteres do conjunto de caracteres. Este modo é o mais eficaz, mas o mais demorado de todos os modos John. Este modo funciona melhor quando sabemos qual pode ser a senha, pois ele tentará todas as combinações possíveis em sequência, começando pela mais curta.

O modo incremental gera as suposições em tempo real, enquanto o modo de lista de palavras usa uma lista predefinida de palavras. Ao mesmo tempo, o modo de quebra única é usado para verificar uma única senha em relação a um hash.

#### Modo incremental em John
```shell-session
NycolasRamos@htb[/htb]$ john --incremental <hash_file>
```

Além disso, é importante notar que o conjunto de caracteres padrão é limitado a `a-zA-Z0-9`. Portanto, se tentarmos quebrar senhas complexas com caracteres especiais, precisamos usar um conjunto de caracteres personalizado.

#### Crackeando arquivos com John
```shell-session
cry0l1t3@htb:~$ <tool> <file_to_crack> > file.hash
cry0l1t3@htb:~$ pdf2john server_doc.pdf > server_doc.hash
cry0l1t3@htb:~$ john server_doc.hash
                # OR
cry0l1t3@htb:~$ john --wordlist=<wordlist.txt> server_doc.hash 
```

Além disso, podemos usar diferentes modos para isso com nossas listas de palavras e regras pessoais. Criamos uma lista que inclui muitas, mas não todas, ferramentas que podem ser usadas para John:

|**Ferramenta**|**Descrição**|
|---|---|
|`pdf2john`|Converte documentos PDF para John|
|`ssh2john`|Converte chaves privadas SSH para John|
|`mscash2john`|Converte hashes MS Cash para John|
|`keychain2john`|Converte arquivos de chaveiro do OS X para John|
|`rar2john`|Converte arquivos RAR para John|
|`pfx2john`|Converte arquivos PKCS#12 para John|
|`truecrypt_volume2john`|Converte volumes TrueCrypt para John|
|`keepass2john`|Converte bancos de dados KeePass para John|
|`vncpcap2john`|Converte arquivos VNC PCAP para John|
|`putty2john`|Converte chaves privadas PuTTY para John|
|`zip2john`|Converte arquivos ZIP para John|
|`hccap2john`|Converte capturas de handshake WPA/WPA2 para John|
|`office2john`|Converte documentos do MS Office para John|
|`wpa2john`|Converte handshakes WPA/WPA2 para John|


















































