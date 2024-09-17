Os controladores de domínio autenticam todos os usuários e computadores em um domínio. Portanto, é essencial garantir o número e o posicionamento ideais dos controladores de domínio em qualquer ambiente do AD DS.

## Implantar controladores de domínio do AD DS em um ambiente local
O processo de implantação de um controlador de domínio ocorre em duas etapas. Primeiro, são instalados os binários necessários para a função do controlador de domínio, usando o **Windows Admin Center** ou o **Server Manager**.

Após essa instalação inicial, os arquivos do AD DS estão presentes, mas ainda não configurados. A segunda etapa é a configuração do AD DS, que pode ser feita de forma simples utilizando o **Assistente de Configuração dos Serviços de Domínio do Active Directory**, iniciado a partir do Server Manager.

Como parte da configuração da função do AD DS, você precisa fornecer respostas às perguntas na tabela a seguir:

|                                                                                                                           |                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Você está instalando uma nova floresta, uma nova árvore ou um controlador de domínio adicional para um domínio existente? | Responder a essa pergunta determina quais informações adicionais você pode precisar, como o nome do domínio pai.                                                                                                                                    |
| Qual é o nome do Sistema de Nomes de Domínio (DNS) para o domínio do AD DS?                                               | Ao criar o primeiro controlador de domínio para um domínio, você deve especificar o nome de domínio totalmente qualificado (FQDN). Ao adicionar um controlador de domínio a um domínio ou floresta existente, você usa o nome de domínio existente. |
| Qual nível você escolherá para o nível funcional da floresta?                                                             | O nível funcional da floresta determina os recursos de floresta disponíveis e o sistema operacional (SO) do controlador de domínio suportado. Isso também define o nível funcional de domínio mínimo para os domínios na floresta.                  |
| Qual nível você escolherá para o nível funcional do domínio?                                                              | O nível funcional do domínio determina os recursos do domínio que estarão disponíveis e os sistemas operacionais do controlador de domínio suportados.                                                                                              |
| O controlador de domínio será um servidor DNS?                                                                            | Você pode instalar a função DNS como parte da implantação do controlador de domínio.                                                                                                                                                                |
| O controlador de domínio hospedará o catálogo global?                                                                     | Esta opção é selecionada por padrão.                                                                                                                                                                                                                |
| O controlador de domínio será um controlador de domínio somente leitura (RODC)?                                           | Esta opção não está disponível para o primeiro controlador de domínio em uma floresta.                                                                                                                                                              |
| Qual será a senha do Modo de Restauração dos Serviços de Diretório (DSRM)?                                                | Isso é necessário para restaurar objetos de banco de dados do AD DS a partir de um backup.                                                                                                                                                          |
| Qual é o nome NetBIOS para o domínio do AD DS?                                                                            | Ao criar o primeiro controlador de domínio para um domínio, você deve especificar o nome NetBIOS do domínio.                                                                                                                                        |
| Onde o banco de dados, os arquivos de log e as pastas SYSVOL serão criados?                                               | Por padrão, a pasta do banco de dados e dos arquivos de log está localizada em **C:\Windows\NTDS** . Por padrão, a pasta SYSVOL está localizada em **C:\Windows\SYSVOL** .                                                                          |

![[Pasted image 20240917145947.png]]

**Instalar um controlador de domínio da mídia** pode ser útil em locais remotos com conexões de rede lentas ou instáveis. Nesse caso, você pode criar um backup do AD DS em um dispositivo de armazenamento, como uma unidade USB, e usá-lo no local remoto para instalar o AD DS a partir da mídia, reduzindo o tráfego pela rede de longa distância (WAN). Apenas o tráfego relacionado à segurança e as atualizações subsequentes ao backup serão transferidos via WAN.

**Considerações sobre filiais**: Em locais onde a segurança física não pode ser garantida, pode ser mais seguro usar um **RODC (Read-Only Domain Controller)**. O RODC armazena uma cópia somente leitura do AD DS e, por padrão, não armazena senhas, minimizando o risco em caso de comprometimento.

**Atualizar controladores de domínio**: Para atualizar para o Windows Server 2022, você pode atualizar o sistema operacional de controladores de domínio existentes ou adicionar novos controladores de domínio executando o Windows Server 2022. O segundo método é preferível, pois oferece uma instalação limpa do Windows Server e do AD DS, além de atualizar automaticamente os registros DNS.






























