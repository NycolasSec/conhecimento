É imperativo que examinemos cada byte para detectar os traços sutis deixados por adversários cibernéticos. Tendo abordado a perícia de memória, vamos agora mudar nossa atenção para a área de `disk forensics`(exame e análise de imagem de disco).

Muitas ferramentas forenses de disco, tanto comerciais quanto de código aberto, vêm repletas de recursos. No entanto, para equipes de resposta a incidentes, certas funcionalidades se destacam:

- `File Structure Insight`: Ser capaz de navegar e ver a hierarquia de arquivos do disco é crucial. Ferramentas forenses de primeira linha devem exibir essa estrutura, permitindo acesso rápido a arquivos específicos, especialmente em locais conhecidos em um sistema suspeito.
- `Hex Viewer`: Para aqueles momentos em que você precisa se aproximar e se aproximar dos seus dados, visualizar arquivos em hexadecimal é essencial. Esse recurso é especialmente útil ao lidar com ameaças como malware personalizado ou exploits exclusivos.
- `Web Artifacts Analysis`: Com tantos dados de usuários vinculados a atividades na web, uma ferramenta forense deve filtrar e apresentar esses dados de forma eficiente. É uma virada de jogo quando você está juntando eventos que levaram um usuário a acessar um site malicioso.
- `Email Carving`: Às vezes, a trilha leva a ameaças internas. Talvez seja um funcionário desonesto ou apenas alguém que escorregou. Em tais casos, os e-mails geralmente são a chave. Uma ferramenta que pode extrair e apresentar esses dados simplifica o processo, tornando mais fácil conectar os pontos.
- `Image Viewer`: Às vezes, as imagens armazenadas em sistemas podem contar uma história própria. Seja para verificações de políticas ou mergulhos mais profundos, ter um visualizador integrado é uma bênção.
- `Metadata Analysis`: Detalhes como timestamps de criação de arquivo, hashes e localização de disco podem ser inestimáveis. Considere um cenário em que você está tentando combinar o tempo de inicialização de um aplicativo com um alerta de malware. Essas correlações podem ser o ponto-chave na sua investigação.

Entre [no Autopsy](https://www.autopsy.com/) : uma plataforma forense amigável ao usuário construída sobre o conjunto de ferramentas Sleuth Kit de código aberto. Ela espelha muitos recursos que você encontraria em suas contrapartes comerciais.

Depois de carregar uma imagem forense e processar os dados, você verá os artefatos forenses organizados de forma organizada no painel lateral. A partir daqui, você pode:

![[Pasted image 20240906133150.png]]

###### Mergulhe para explorar arquivos `Data Sources` e diretórios.
![[Pasted image 20240906133207.png]]

###### Examinar `Web Artifacts`.
![[Pasted image 20240906133529.png]]
	
###### Verificar `Attached Devices`.
![[Pasted image 20240906133612.png]]

###### Recuperar `Deleted Files`.
![[Pasted image 20240906133644.png]]

###### Conduta `Keyword Searches`.
![[Pasted image 20240906133656.png]]

![[Pasted image 20240906133713.png]]

###### Use `Keyword Lists`para pesquisas direcionadas.
![[Pasted image 20240906133822.png]]

![[Pasted image 20240906133755.png]]

###### Comprometa `Timeline Analysis`-se a mapear eventos.
![[Pasted image 20240906133943.png]]

Utilizaremos bastante a ``Autopsy`` na próxima seção "Cenário Prático de Perícia Digital".



























































