### Descrição dos contêineres
Um contêiner é um processo encapsulado que inclui todas as dependências necessárias para sua execução, tornando suas bibliotecas específicas independentes do sistema operacional do host. 

O engine de contêiner cria um sistema de arquivos unindo camadas imutáveis de imagens de contêiner e adiciona uma camada gravável para modificações em tempo de execução. Por padrão, contêineres são efêmeros, ou seja, a camada gravável é removida ao excluir o contêiner.

![[Pasted image 20240913095932.png]]

Os contêineres utilizam recursos do kernel do Linux, como ``namespaces`` e ``cgroups``, para isolar processos e gerenciar recursos como CPU e memória. Essas tecnologias, junto com o ``SELinux`` e o ``seccomp``, garantem segurança e isolamento dentro dos contêineres. Embora sejam baseados em Linux, em sistemas não Linux, essas funções são virtualizadas.
A conteinerização evoluiu de tecnologias como o *chroot* e agora segue padrões da ``Open Container Initiative`` (OCI), permitindo que os contêineres sejam implantados de forma padronizada em diferentes ambientes.

#### Imagens versus instâncias
Os contêineres podem ser divididos em duas ideias semelhantes, mas distintas: _imagens de contêiner_ e _instâncias de contêiner_. Uma _imagem de contêiner_ contém dados efetivamente imutáveis que definem um aplicativo e suas bibliotecas. Você pode usar imagens de contêiner para criar _instâncias de contêiner_, que são versões executáveis da imagem que incluem referências a redes, discos e outras necessidades de tempo de execução.

Você pode usar uma única imagem de contêiner várias vezes para criar várias instâncias de contêiner diferentes. Você também pode executar essas instâncias em vários hosts. O aplicativo dentro de um contêiner é independente do ambiente de host.

### Comparação entre contêineres e máquinas virtuais

Os contêineres e as máquinas virtuais (``VMs``) têm funções semelhantes, mas diferem em desempenho e consumo de recursos. Enquanto as ``VMs`` fornecem um ambiente de computação completo, ideal para aplicações que precisam de hardware dedicado ou sistemas operacionais diferentes, os contêineres são mais leves e rápidos.

Contêineres utilizam menos memória e espaço em disco, geralmente medidos em megabytes, em comparação às ``VMs``, que costumam ocupar gigabytes.

#### Máquinas virtuais versus contêineres
Máquinas virtuais e contêineres utilizam softwares distintos para gerenciamento. **Hipervisores** como ``KVM``, ``Xen``, ``VMware`` e ``Hyper-V`` são usados para virtualização de ``VMs``, enquanto engines de contêiner, como o ``Podman``, são o equivalente para a execução e gerenciamento de contêineres.

|                                        | Máquinas virtuais                     | Contêineres                                     |
| :------------------------------------- | :------------------------------------ | ----------------------------------------------- |
| **Funcionalidade no nível da máquina** | Hipervisor                            | Engine de contêiner                             |
| **Gerenciamento**                      | Interface de gerenciamento de VM      | Engine de contêiner ou software de orquestração |
| **Nível de virtualização**             | Ambiente totalmente virtualizado      | Somente partes relevantes                       |
| **Tamanho**                            | Medido em gigabytes                   | Medido em megabytes                             |
| **Portabilidade**                      | Geralmente, apenas o mesmo hipervisor | Qualquer engine compatível com OCI              |

Você pode gerenciar ``hipervisors`` com softwares específicos, como o Virtual Machine Manager para ``KVM``, ou com ferramentas de gerenciamento incluídas com o ``hipervisor``. Para contêineres, o gerenciamento pode ser feito diretamente através do engine de contêiner ou por meio de ferramentas de orquestração, como ``Red Hat OpenShift Container Platform`` (``RHOCP``) e ``Kubernetes``, que também podem gerenciar VMs. Contêineres que seguem a especificação OCI são mais interoperáveis do que VMs, já que não dependem de um engine específico e podem ser executados por diferentes engines de contêiner.

#### Implantação em escala
Tanto os contêineres quanto as VMs podem funcionar bem em várias escalas. Como um contêiner exige consideravelmente menos recursos do que uma VM, eles oferecem benefícios de desempenho e de recursos em uma escala maior.

Um método comum em ambientes de grande escala é usar contêineres que são executados dentro de VMs. Essa configuração aproveita os pontos fortes de cada tecnologia.

### Desenvolvimento para contêineres
A conteinerização oferece muitas vantagens para o processo de desenvolvimento, como facilitar a testagem e as implantações, fornecendo ferramentas para estabilidade, segurança e flexibilidade.

#### Testes e fluxos de trabalho
Contêineres oferecem aos desenvolvedores a capacidade de dimensionar facilmente, permitindo que o software seja desenvolvido e testado localmente e, em seguida, implantado em servidores ou clusters com poucas mudanças.

Esse fluxo de trabalho é ideal para microsserviços, que são pequenos e efêmeros, e para integração com pipelines de CI/CD. O Red Hat OpenShift Container Platform (RHOCP) é particularmente eficiente nesse aspecto, oferecendo recursos integrados para suportar fluxos de trabalho e pipelines de CI/CD.

#### Estabilidade
Imagens de contêiner oferecem uma solução estável para desenvolvedores, pois incluem todas as bibliotecas necessárias para o aplicativo, eliminando problemas de dependência e variações entre ambientes de teste e produção.

Isso garante que, por exemplo, uma versão específica do Python será utilizada consistentemente em todos os ambientes onde o contêiner for implantado.

#### Aplicativos de vários contêineres
Um aplicativo de vários contêineres é distribuído entre diferentes contêineres, podendo usar réplicas da mesma imagem para alta disponibilidade (HA) ou diferentes imagens para diferentes funções, como um banco de dados e uma API da web.

O software de gerenciamento de contêineres, como o ``Podman`` ou a especificação ``compose-spec``, pode ser usado para gerenciar essas réplicas e garantir alta disponibilidade.