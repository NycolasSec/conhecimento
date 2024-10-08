 Para obter certificados para sua infraestrutura de AD DS, você pode solicitá-los de uma AC pública ou emiti-los usando sua própria infraestrutura. O AD CS é uma tecnologia de identidade do Windows Server que permite que você implemente o PKI na sua **organização**.

## O que é a PKI?
A PKI é a combinação de software, tecnologias de criptografia, processos e serviços, a qual permite que uma organização proteja dados, comunicações e transações de negócios.

A PKI conta com a troca de certificados digitais entre usuários autenticados e recursos confiáveis. Você usa certificados para proteger os dados e gerenciar as credenciais de identificação de usuários e computadores dentro e fora da sua organização.

## O que é o AD CS?
Você pode implementar uma solução PKI usando a função do Windows Server do AD CS. O AD CS fornece todos os componentes relacionados à PKI no formato de serviços de função.

Cada serviço de função é responsável por uma parte específica da infraestrutura de certificado durante o trabalho em conjunto para formar uma solução completa.

- **Autoridade de Certificação (AC)**: Emite, revoga e publica informações sobre certificados. A primeira AC instalada se torna a raiz da PKI (Infraestrutura de Chave Pública), e as ACs subordinadas confiam implicitamente na AC raiz.
  
- **Registro via Web**: Permite emissão e renovação de certificados para dispositivos fora do domínio ou que usam sistemas operacionais diferentes do Windows.
  
- **Respondente Online**: Configura a verificação do status de revogação de certificados usando OCSP (Online Certificate Status Protocol), respondendo solicitações de validação de certificados.

- **NDES**: Permite que dispositivos de rede, como roteadores e switches, obtenham certificados do AD CS.

- **CES**: Atua como um intermediário entre computadores e a AC para solicitação, renovação e instalação de certificados via Web. Também facilita a recuperação de listas de certificados revogados (CRL) e registro entre florestas ou pela Internet.

- **Serviço Web de Política de Registro**: Fornece informações sobre políticas de registro de certificados e trabalha com o CES para registrar certificados em dispositivos fora do domínio ou sem acesso ao controlador de domínio.

![[Pasted image 20241008151336.png]]

































