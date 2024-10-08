Você não precisa apenas controlar a sua emissão, mas também o seu uso e, sempre que necessário, impor sua revogação. Isso é crítico para atenuar e corrigir o comprometimento potencial da segurança baseada em certificado.

## O que é uma revogação de certificado?
Uma revogação é o processo no qual você desabilita a validade de um ou mais certificados. Ao iniciar um processo de revogação, você publica uma impressão digital do certificado na CRL correspondente. Isso indica que um certificado específico deixou de ser válido.

![[Pasted image 20241008164129.png]]

Geralmente, o processo de revogação é composto pela seguinte sequência de etapas:

1. Revogar um certificado e fornecer o motivo, além da data e hora de revogação. Você pode executar essa tarefa no console da AC.
2. Publicar uma CRL. Você tem a opção de disparar a publicação no console da AC ou agendar a publicação automática em intervalos regulares. Você pode publicar CRLs no AD DS, em uma pasta compartilhada ou em um site.
3. Se sistemas operacionais, aplicativos ou serviços iniciarem uma ação segura que envolva o uso de um certificado, isso acionará uma verificação automática do status de revogação desse certificado por meio da consulta à AC emissora e ao local de CDP correspondente. Esse processo determina se o certificado está revogado.

Os sistemas operacionais Windows incluem o CryptoAPI, que é responsável pelos processos de revogação e verificação de status de certificados. O CryptoAPI usa as seguintes fases no processo de verificação de certificado:

- **Descoberta do certificado**. A descoberta do certificado coleta certificados de AC, informações do AIA em certificados emitidos e detalhes do processo de registro de certificado.
- **Validação de caminho**. A validação de caminho é o processo de verificar o certificado por meio da cadeia de AC ou do caminho, até que o certificado de AC raiz seja atingida.
- **Verificação de revogação**. Cada certificado na cadeia de certificados é verificado para garantir que nenhum dos certificados esteja revogado.
- **Recuperação de rede e cache**. A recuperação de rede é executada usando o OCSP. A CryptoAPI é responsável por verificar o cache local para, primeiro, obter informações de revogação e, se não houver nenhuma correspondência, fazer uma chamada usando o OCSP, que se baseia na URL fornecida pelo certificado emitido.

## O que é um serviço Respondente Online?
- **Serviço Respondente Online**: Proporciona uma maneira eficiente de verificar o status de revogação de certificados, dependendo do **OCSP** (Protocolo de Status de Certificado Online) para determinar esse status. O OCSP utiliza o protocolo HTTP para enviar solicitações de status de certificado.
    
- **Verificação por CRLs**: Os clientes normalmente acessam as **CRLs** (Listas de Revogação de Certificados) para verificar o status de revogação, mas essas listas podem ser grandes, tornando a pesquisa demorada. O serviço Respondente Online pode pesquisar as CRLs dinamicamente, respondendo rapidamente ao cliente com o status do certificado solicitado.
    
- **Implementação**: Um único Respondente Online pode verificar o status de certificados emitidos por uma ou várias ACs. É possível implementar múltiplos Respondentes Online para gerenciar melhor as solicitações de revogação.
    
- **Configuração**: As ACs devem incluir a URL do Respondente Online na extensão **AIA** dos certificados emitidos, permitindo que o cliente OCSP use essa URL para validar o status do certificado. Além disso, é necessário emitir um modelo de certificado para autenticação de resposta do OCSP, permitindo que os Respondentes Online registrem esse certificado.
























