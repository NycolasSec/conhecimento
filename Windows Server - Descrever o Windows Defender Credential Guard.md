O **Windows Defender Credential Guard** protege contra ataques **Pass-the-Hash (NTLM)** e **Pass-the-Ticket (Kerberos)** ao restringir o acesso a credenciais sensíveis, como hashes de senha NTLM e tickets Kerberos. Ele faz isso isolando esses dados em processos e memórias protegidos, acessíveis apenas por elementos autorizados e verificados digitalmente do sistema operacional.

Além disso, utiliza recursos de segurança de hardware, como inicialização segura e virtualização, para reforçar a proteção das credenciais, garantindo que softwares não autorizados não possam acessar dados de autenticação e autorização.

## Como o Windows Defender Credential Guard funciona
O **Windows Defender Credential Guard** protege as credenciais de usuário isolando-as em um contêiner virtualizado e protegido, separado do sistema operacional principal. Apenas softwares com privilégios elevados podem acessar essas credenciais.

O contêiner virtualizado opera de forma independente, paralelamente ao sistema operacional do host, protegendo os processos contra tentativas externas de acessar ou comprometer essas informações. Assim, mesmo em caso de invasão por malware no sistema principal, as credenciais permanecem protegidas.

### Requisitos do Windows Defender Credential Guard
Você pode implantar o Windows Defender Credential Guard somente em dispositivos que atendem a determinados requisitos de hardware. O Windows Defender Credential Guard deve ser usado em qualquer computador em que a equipe de TI usa credenciais privilegiadas, principalmente estações de trabalho dedicadas para acesso privilegiado.

O Windows Defender Credential Guard requer o seguinte:

- Windows 10 Enterprise ou Windows Server 2016 ou posterior
- Extensões de virtualização de CPU de 64 bits mais tabelas de páginas estendidas (Intel VT-x ou AMD-V)
- TPM (Trusted Platform Module) 1.2 ou 2.0
- UEFI (Firmware Unified Extensible Firmware Interface) versão 2.3.1.c ou mais recente
- Inicialização segura da UEFI
- Atualização de firmware de segurança da UEFI

O Windows Defender Credential Guard protege os segredos em uma máquina virtual do Microsoft Hyper-V quando:

- O host Hyper-V tem uma IOMMU (unidade de gerenciamento de memória de entrada/saída) e executa o Windows Server 2016 ou posterior ou o Windows 10 Enterprise.
- A máquina virtual precisa ser da Geração 2, ter o TPM virtual habilitado e executar um sistema operacional compatível com o Windows Defender Credential Guard.

O Windows Defender Credential Guard não dá suporte a:

- delegação irrestrita de Kerberos
- NTLMv1
- MS-CHAPv2
- Autenticação digest
- Delegação de credencial (CredSSP)
- Criptografia Kerberos DES (Data Encryption Standard)

Não há suporte para o Windows Defender Credential Guard em controladores de domínio. Ele também não fornece proteções para o banco de dados do AD DS (Active Directory) ou o SAM (Security Accounts Manager).











































