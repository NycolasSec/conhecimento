[[Cyber - TNS]] | [[Redes - TNS]]

## Configuração padrão
A configuração padrão varia dependendo da versão e edição instalada. Mas algumas configurações comuns são geralmente configuradas por padrão no Oracle TNS.

Por padrão, o listener escuta conexões de entrada na porta `TCP/1521`.O listener TNS é configurado para suportar vários protocolos de rede, incluindo `TCP/IP`, `UDP`, `IPX/SPX`e `AppleTalk`. Também suporta várias interfaces de rede e escutar em endereços de rede específicos em todas as interfaces de rede disponíveis.
Por padrão, o Oracle TNS pode ser gerenciado remotamente em `Oracle 8i`/, `9i`mas não no Oracle 10g/11g.

A configuração básica inclui alguns recursos básicos de segurança.Por exemplo, o listener aceitará somente conexões de hosts autorizados e executará autenticação básica usando uma combinação de nomes de host, endereços IP e nomes de usuário e senhas. Além disso, o listener usará o Oracle Net Services para criptografar a comunicação entre o cliente e o servidor.

Os arquivos de configuração para o Oracle TNS são chamados `tnsnames.ora` e `listener.ora`e normalmente estão localizados no diretório `$ORACLE_HOME/network/admin`.

O Oracle TNS é frequentemente usado com outros serviços Oracle como Oracle DBSNMP, Oracle Databases, Oracle Application Server, Oracle Enterprise Manager, Oracle Fusion Middleware, servidores web e muitos mais. Foram feitas alterações para a instalação padrão nos serviços Oracle, por exemplo, o Oracle 9 tem uma senha padrão, `CHANGE_ON_INSTALL`, enquanto o Oracle 10 não tem uma senha padrão definida. O serviço Oracle DBSNMP também usa uma senha padrão, `dbsnmp`que devemos lembrar quando nos depararmos com esta.

Outro exemplo seria que muitas organizações ainda usam o serviço `finger`junto com o Oracle, o que pode colocar o serviço do Oracle em risco e torná-lo vulnerável quando temos o conhecimento necessário de um diretório home.

Cada banco de dados ou serviço tem uma entrada exclusiva no arquivo [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007) contendo as informações de conexão. A entrada consiste em um nome para o serviço, o local de rede do serviço e o nome do banco de dados ou serviço que os clientes devem usar ao se conectar ao serviço.

Exemplo de um arquivo ``tnsnames.ora`` simples :
```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

Aqui podemos ver um serviço chamado `ORCL`, que está escutando na porta `TCP/1521`no endereço IP `10.129.11.102`. Os clientes devem usar o nome do serviço `orcl`ao se conectar ao serviço. As entradas também podem incluir informações adicionais, como detalhes de autenticação, configurações de pool de conexão e configurações de balanceamento de carga.

Por outro lado, o `listener.ora`arquivo é um arquivo de configuração do lado do servidor que define as propriedades e os parâmetros do processo listener, responsável por receber solicitações de clientes e encaminhá-las para a instância apropriada do banco de dados Oracle.

**Listener.ora**
```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```
Resumindo, o software Oracle Net Services do lado do cliente usa o `tnsnames.ora`arquivo para resolver nomes de serviços em endereços de rede, enquanto o processo do ouvinte usa o `listener.ora`arquivo para determinar os serviços que ele deve escutar e o comportamento do ouvinte.

Os bancos de dados Oracle podem ser protegidos usando a chamada Lista de Exclusão PL/SQL ( `PlsqlExclusionList`). É um arquivo de texto criado pelo usuário que precisa ser colocado no `$ORACLE_HOME/sqldeveloper`diretório e contém os nomes dos pacotes ou tipos PL/SQL que devem ser excluídos da execução. Depois que o arquivo da Lista de Exclusão PL/SQL é criado, ele pode ser carregado na instância do banco de dados. Ele serve como uma lista negra que não pode ser acessada por meio do Oracle Application Server.

| **Contexto**         | **Descrição**                                                                                                                |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `DESCRIPTION`        | Um descritor que fornece um nome para o banco de dados e seu tipo de conexão.                                                |
| `ADDRESS`            | O endereço de rede do banco de dados, que inclui o nome do host e o número da porta.                                         |
| `PROTOCOL`           | O protocolo de rede usado para comunicação com o servidor                                                                    |
| `PORT`               | O número da porta usada para comunicação com o servidor                                                                      |
| `CONNECT_DATA`       | Especifica os atributos da conexão, como o nome do serviço ou SID, protocolo e identificador da instância do banco de dados. |
| `INSTANCE_NAME`      | O nome da instância do banco de dados que o cliente deseja conectar.                                                         |
| `SERVICE_NAME`       | O nome do serviço ao qual o cliente deseja se conectar.                                                                      |
| `SERVER`             | O tipo de servidor usado para a conexão do banco de dados, como dedicado ou compartilhado.                                   |
| `USER`               | O nome de usuário usado para autenticação no servidor de banco de dados.                                                     |
| `PASSWORD`           | A senha usada para autenticação no servidor de banco de dados.                                                               |
| `SECURITY`           | O tipo de segurança da conexão.                                                                                              |
| `VALIDATE_CERT`      | Se o certificado deve ser validado usando SSL/TLS.                                                                           |
| `SSL_VERSION`        | A versão do SSL/TLS a ser usada para a conexão.                                                                              |
| `CONNECT_TIMEOUT`    | O limite de tempo em segundos para o cliente estabelecer uma conexão com o banco de dados.                                   |
| `RECEIVE_TIMEOUT`    | O limite de tempo em segundos para o cliente receber uma resposta do banco de dados.                                         |
| `SEND_TIMEOUT`       | O limite de tempo em segundos para o cliente enviar uma solicitação ao banco de dados.                                       |
| `SQLNET.EXPIRE_TIME` | O limite de tempo em segundos para o cliente detectar uma conexão falhou.                                                    |
| `TRACE_LEVEL`        | O nível de rastreamento da conexão do banco de dados.                                                                        |
| `TRACE_DIRECTORY`    | O diretório onde os arquivos de rastreamento são armazenados.                                                                |
| `TRACE_FILE_NAME`    | O nome do arquivo de rastreamento.                                                                                           |
| `LOG_FILE`           | O arquivo onde as informações de log são armazenadas.                                                                        |

---
## Referências

https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985

https://academy.hackthebox.com/module/112/section/2117
