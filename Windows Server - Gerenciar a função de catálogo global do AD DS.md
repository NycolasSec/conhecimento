Como parte do planejamento de implantações de controlador de domínio, é importante identificar o número e o posicionamento ideais da função de catálogo global. Isso é relevante ao expandir o ambiente do AD DS para outros locais, como no caso da expansão planejada da Contoso.

## Gerenciar a função de catálogo global do AD DS
O catálogo global é uma cópia parcial e pesquisável de objetos em uma floresta do Active Directory (AD), contendo um subconjunto de atributos úteis para buscas entre domínios.

Ele permite acelerar pesquisas em controladores de domínio diferentes dentro de uma floresta e é essencial em florestas de múltiplos domínios. O catálogo global contém apenas os atributos mais usados, como *givenName* e *mail*, e pode ser modificado alterando o esquema do AD. Sua função é crucial em cenários como o encaminhamento de emails pelo Exchange Server e na autenticação de usuários verificando grupos universais.

Em florestas grandes, pode ser útil limitar o número de servidores com a função de catálogo global para reduzir o tráfego de replicação, apesar de isso aumentar a dependência de conectividade entre sites.