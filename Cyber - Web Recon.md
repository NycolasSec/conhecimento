Temos que seguir alguns passos para fazer uma análise

**Pre-engagement**
Definição do escopo de atuação, regras de testes e limites

**Intelligence Gathering**
Também conhecido como ``Information Gathering`` e ``Recon``, esta é uma das fases mais importantes de um teste, seja ``Pentest``, ``Bug bount`` ou até ``CTF``.

**Threat Modeling**
Esta é uma etapa muito importante pra definição real de quais são os riscos vs probabilidade de um ataque e o impacto desse ataque olhando para o negócio.

**Vulnerability Analysis**
Etapa de análise e identificação de vulnerabilidades baseados nos dados coletados durante a etapa de recon, identificação real das falhas e vulnerabilidades e catalogação das falhas com nível de criticidade como **CVSS**, **CVE** e **CWE**.

**Exploitation**
Aqui consistimos somente a exploração e foco em obter acesso a um determinado sistema ou informação utilizando as vulnerabilidades detectadas na etapa anterior, a ideia é testar e burlar proteções de segurança.

**Post Exploitation**
Esta é uma etapa extremamente importante, normalmente é aqui onde conseguimos comprovar a efetividade de um ataque, aqui é onde conseguimos de fato acesso a dados sigilosos de uma empresa fazendo ataques internos de movimentação lateral e escalação de privilégios dentro de uma rede até ter controle total da rede.

**Reporting**
Este é o documento que deve informar todas as vulnerabilidades e brechas de segurança detectadas em todas as etapas do processo de Pentesting, normalmente dividido em duas partes como relatório executivo e relatório técnico.

---

## Intelligence Gathering

**SUBDOMAIN DISCOVERY**
A ideia nesta etapa é obter o máximo de subdomínios de um domínio raiz/principal, aqui eu vou demostrar algumas técnicas para obter o máximo de informações internas e externas apenas pelos subdominios... 

**APPLICATION DISCOVERY**
O objetivo nessa etapa é obter informações sobre as aplicações que estão rodando nos subdominos, utilizamos print's de sites e informações como URL's e parâmetros das aplicações.

**AUTOMATION**
Podemos utilizar algumas automações para nos ajudar a deixar este trabalho mais rápido e efetivo, vou falar um pouco de uma ferramenta muito boa pra isso!

**MANUAL APPLICATION TESTING**
Mesmo depois de várias automações ainda assim é necessário realizar testes manuais nas aplicações, testar parâmetros, comportamentos de uma aplicação e identificação manual.

### Subdomain Discovery

- Histórico
- Brute Force
- Certificados e passivos

#### Histórico
- securitytrails.com
- subdomainfinder.c99.nl
- web.archive.org
- dnsdumpster.com
- github.com/Screetsec/Sudomy

#### Brute force
- github.com/aboul3la/Sublist3r
- github.com/TheRook/subbrute
- github.com/OWASP/Amass

#### Certificados e passivos
- github.com/projectdiscovery/subfinder
- sslmate.com/certspotter
- github.com/UnaPibaGeek/ctfr (usa o crt.sh)
- chaos.projectdiscovery.io

### Application Discovery

- URL discovery
- Parameter discovery
- Content discovery

#### URL discovery
- github.com/lc/gau
- github.com/hakluke/hakrawler
- github.com/xmendez/wfuzz
- github.com/ffuf/ffuf
- github.com/maurosoria/dirsearch

#### Parameter discovery
- github.com/devanshbatham/ParamSpider
- github.com/tomnomnom/gf
- github.com/s0md3v/Parth

#### Content discovery
- github.com/projectdiscovery/httpx
- github.com/michenriksen/aquatone
- github.com/m4ll0k/SecretFinder
- https://github.com/projectdiscovery/katana

### Automation
- github.com/yogeshojha/rengine
- github.com/edoardottt/scilla
- github.com/kpcyrd/sn0int
















