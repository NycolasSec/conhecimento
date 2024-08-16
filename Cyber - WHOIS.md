WHOIS é um protocolo de consulta e resposta amplamente usado, projetado para acessar bancos de dados que armazenam informações sobre recursos registrados da internet. Principalmente associado a nomes de domínio, o WHOIS também pode fornecer detalhes sobre blocos de endereços IP e sistemas autônomos. Pense nele como uma lista telefônica gigante para a internet, permitindo que você pesquise quem possui ou é responsável por vários ativos online.

```shell-session
whois inlanefreight.com
```
![[Pasted image 20240816083850.png]]

- `Domain Name`: O próprio nome de domínio (por exemplo, example.com)
- `Registrar`: A empresa onde o domínio foi registrado (por exemplo, GoDaddy, Namecheap)
- `Registrant Contact`: A pessoa ou organização que registrou o domínio.
- `Administrative Contact`: A pessoa responsável por gerenciar o domínio.
- `Technical Contact`: A pessoa que lida com questões técnicas relacionadas ao domínio.
- `Creation and Expiration Dates`: Quando o domínio foi registrado e quando está prestes a expirar.
- `Name Servers`: Servidores que traduzem o nome de domínio em um endereço IP.

## Por que o WHOIS é importante para o Web Recon

Os dados WHOIS servem como um tesouro de informações para testadores de penetração durante a fase de reconhecimento de uma avaliação. Eles oferecem insights valiosos sobre a pegada digital da organização-alvo e vulnerabilidades potenciais:

- `Identifying Key Personnel`: Os registros WHOIS frequentemente revelam os nomes, endereços de e-mail e números de telefone de indivíduos responsáveis ​​por gerenciar o domínio. Essas informações podem ser aproveitadas para ataques de engenharia social ou para identificar alvos potenciais para campanhas de phishing.
- `Discovering Network Infrastructure`: Detalhes técnicos como servidores de nomes e endereços IP fornecem pistas sobre a infraestrutura de rede do alvo. Isso pode ajudar os testadores de penetração a identificar potenciais pontos de entrada ou configurações incorretas.
- `Historical Data Analysis`: Acessar registros históricos do WHOIS por meio de serviços como [WhoisFreaks](https://whoisfreaks.com/) pode revelar mudanças na propriedade, informações de contato ou detalhes técnicos ao longo do tempo. Isso pode ser útil para rastrear a evolução da presença digital do alvo.



































