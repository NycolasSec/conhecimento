Durante o processo de pentesting, é quase certo que encontraremos servidores web, dado que muitos serviços estão migrando para plataformas baseadas na web acessíveis via navegadores e HTTP/S. Além de entretenimento moderno, como jogos e streaming, muitos serviços de software estão agora na web. Em testes de penetração externos, é comum que redes de perímetro sejam bem protegidas, expondo menos serviços vulneráveis como SMB.

Em vez disso, muitas vezes obtemos acesso à rede interna por meio de ataques a aplicativos web (como upload de arquivos maliciosos, injeção de SQL e outros ataques) ou por técnicas de engenharia social e pulverização de senhas contra diversos sistemas de autenticação.

Os aplicativos da web, frequentemente a principal superfície de ataque exposta em avaliações externas, oferecem múltiplas oportunidades para exploração. Podem incluir formulários de upload de arquivos que permitem o envio de shells web PHP, JSP ou ASP.NET, funcionalidades de autorregistro para uploads maliciosos ou servidores como Tomcat, Axis2 ou WebLogic que aceitam código JSP.

Também é possível encontrar serviços FTP mal configurados que permitem uploads diretamente para a raiz da web do servidor. Após identificar uma vulnerabilidade de upload irrestrito ou configuração incorreta, o próximo passo é explorar essas falhas para comprometer o sistema.

## O que é um Web Shell?

**Web Shells**: São sessões de shell acessadas via navegador para interagir com o sistema operacional de um servidor web. Para utilizar um web shell, primeiro é necessário encontrar uma vulnerabilidade que permita o upload de arquivos no servidor. Usualmente, os web shells são carregados em uma linguagem web que possibilita a execução remota de código. No entanto, confiar exclusivamente em um web shell pode ser instável, pois alguns aplicativos web removem arquivos carregados após um tempo. Web shells são frequentemente usados como uma primeira etapa para obter execução remota de código, podendo ser posteriormente substituídos por shells mais interativos para garantir persistência.
























