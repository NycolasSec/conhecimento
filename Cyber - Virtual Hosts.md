Uma vez que o DNS direciona o tráfego para o servidor correto, a configuração do servidor web se torna crucial para determinar como as solicitações de entrada são tratadas. Servidores web como Apache, Nginx ou IIS são projetados para hospedar vários sites ou aplicativos em um único servidor. Eles conseguem isso por meio de hospedagem virtual, o que lhes permite diferenciar entre domínios, subdomínios ou até mesmo sites separados com conteúdo distinto.

## Como funcionam os hosts virtuais: entendendo VHosts e subdomínios

No centro do `virtual hosting` está a capacidade dos servidores web de distinguir entre vários sites ou aplicativos que compartilham o mesmo endereço IP. Isso é obtido aproveitando o cabeçalho `HTTP Host`, uma informação incluída em cada solicitação `HTTP` enviada por um navegador web.

A principal diferença entre `VHosts`e `subdomains`é sua relação com a `Domain Name System (DNS)`e a configuração do servidor web.

- `Subdomains`: Estas são extensões de um nome de domínio principal (por exemplo, `blog.example.com`é um subdomínio de `example.com`). `Subdomains`normalmente têm seu próprio `DNS records`, apontando para o mesmo endereço IP do domínio principal ou um diferente. Eles podem ser usados ​​para organizar diferentes seções ou serviços de um site.
- `Virtual Hosts`( `VHosts`): Hosts virtuais são configurações dentro de um servidor web que permitem que vários sites ou aplicativos sejam hospedados em um único servidor. Eles podem ser associados a domínios de nível superior (por exemplo, `example.com`) ou subdomínios (por exemplo, `dev.example.com`). Cada host virtual pode ter sua própria configuração separada, permitindo controle preciso sobre como as solicitações são tratadas.

Se um host virtual não tiver um registro DNS, você ainda poderá acessá-lo modificando o arquivo `hosts` na sua máquina local. O arquivo `hosts` permite que você mapeie um nome de domínio para um endereço IP manualmente, ignorando a resolução DNS.

Os sites geralmente têm subdomínios que não são públicos e não aparecerão em registros DNS. Esses `subdomains` são acessíveis apenas internamente ou por meio de configurações específicas. `VHost fuzzing`é uma técnica para descobrir o que é público e o que não é público `subdomains`e `VHosts`testar vários nomes de host em relação a um endereço IP conhecido.

Hosts virtuais também podem ser configurados para usar domínios diferentes, não apenas subdomínios. Por exemplo:

```apacheconf
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

Aqui, `example1.com`, `example2.org`, e `another-example.net`são domínios distintos hospedados no mesmo servidor. O servidor web usa o cabeçalho `Host` para servir o conteúdo apropriado com base no nome de domínio solicitado.

### Pesquisa de VHost do Servidor

O seguinte ilustra o processo de como um servidor web determina o conteúdo correto a ser veiculado com base no cabeçalho ``Host``:


![[Pasted image 20240819091409.png]]

1. `Browser Requests a Website`:Quando você insere um nome de domínio (por exemplo, `www.inlanefreight.com`) no seu navegador, ele inicia uma solicitação HTTP para o servidor web associado ao endereço IP desse domínio.
2. `Host Header Reveals the Domain`: O navegador inclui o nome de domínio no `Host`cabeçalho da solicitação, que atua como um rótulo para informar ao servidor web qual site está sendo solicitado.
3. `Web Server Determines the Virtual Host`: O servidor web recebe a solicitação, examina o `Host`cabeçalho e consulta sua configuração de host virtual para encontrar uma entrada correspondente para o nome de domínio solicitado.
4. `Serving the Right Content`:Ao identificar a configuração correta do host virtual, o servidor web recupera os arquivos e recursos correspondentes associados a esse site da raiz do documento e os envia de volta ao navegador como resposta HTTP.

Em essência, o `Host`cabeçalho funciona como um switch, permitindo que o servidor web determine dinamicamente qual site servir com base no nome de domínio solicitado pelo navegador.

### Tipos de hospedagem virtual

Existem três tipos principais de hospedagem virtual, cada um com suas vantagens e desvantagens:

1. `Name-Based Virtual Hosting`: Este método depende unicamente do `HTTP Host header` para distinguir entre sites. É o método mais comum e flexível, pois não requer vários endereços IP. É econômico, fácil de configurar e suporta a maioria dos servidores web modernos. No entanto, requer que o servidor web suporte `virtual hosting` baseado em nome e pode ter limitações com certos protocolos como `SSL/TLS`.
2. `IP-Based Virtual Hosting`: Este tipo de hospedagem atribui um endereço IP exclusivo a cada site hospedado no servidor. O servidor determina qual site servir com base no endereço IP para o qual a solicitação foi enviada. Ele não depende do `Host header`, pode ser usado com qualquer protocolo e oferece melhor isolamento entre sites. Ainda assim, ele requer vários endereços IP, o que pode ser caro e menos escalável.
3. `Port-Based Virtual Hosting`: Diferentes sites são associados a diferentes portas no mesmo endereço IP. Por exemplo, um site pode ser acessível na porta 80, enquanto outro pode ser acessado na porta 8080. `Port-based virtual hosting`pode ser usado quando endereços IP são limitados, mas não é tão comum ou amigável quanto `name-based virtual hosting`e pode exigir que os usuários especifiquem o número da porta na URL.

## Ferramentas de descoberta de host virtual

Embora a análise manual do `HTTP headers` e `DNS lookups` reversa possa ser eficaz, `virtual host discovery tools` automatizam e agilizam o processo, tornando-o mais eficiente e abrangente. Essas ferramentas empregam várias técnicas para sondar o servidor alvo e descobrir potenciais `virtual hosts`.

Várias ferramentas estão disponíveis para auxiliar na descoberta de hosts virtuais:

| Ferramenta                                           | Descrição                                                                                                                                        | Características                                                                       |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------- |
| [gobuster](https://github.com/OJ/gobuster)           | Uma ferramenta multifuncional frequentemente usada para força bruta de diretórios/arquivos, mas também eficaz para descoberta de hosts virtuais. | Rápido, suporta múltiplos métodos HTTP e pode usar listas de palavras personalizadas. |
| [Feroxbuster](https://github.com/epi052/feroxbuster) | Semelhante ao Gobuster, mas com uma implementação baseada em Rust, conhecida por sua velocidade e flexibilidade.                                 | Suporta recursão, descoberta de curinga e vários filtros.                             |
| [ffuf](https://github.com/ffuf/ffuf)                 | Outro web fuzzer rápido que pode ser usado para descoberta de host virtual por meio do fuzzing do cabeçalho `Host`.                              | Opções de entrada e filtragem de lista de palavras personalizáveis.                   |

### gobuster

Gobuster é uma ferramenta versátil comumente usada para força bruta de diretórios e arquivos, mas também se destaca na descoberta de host virtual.

Ele envia sistematicamente solicitações HTTP com diferentes cabeçalhos `Host` para um endereço IP de destino e, em seguida, analisa as respostas para identificar hosts virtuais válidos.

Há algumas coisas que você precisa preparar para forçar cabeçalhos `Host`:

1. `Target Identification`: Primeiro, identifique o endereço IP do servidor web alvo. Isso pode ser feito por meio de pesquisas de DNS ou outras técnicas de reconhecimento.
2. `Wordlist Preparation`: Prepare uma lista de palavras contendo nomes de host virtual em potencial. Você pode usar uma lista de palavras pré-compilada, como SecLists, ou criar uma personalizada com base no setor do seu alvo, convenções de nomenclatura ou outras informações relevantes.

O comando `gobuster` para forçar brutamente vhosts geralmente se parece com isto:

```shell-session
NycolasES6@htb[/htb]$ gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

- O sinalizador `-u` especifica o URL de destino (substitua `<target_IP_address>`pelo IP real).
- O sinalizador `-w` especifica o arquivo da lista de palavras (substitua `<wordlist_file>`pelo caminho para sua lista de palavras).
- O sinalizador `--append-domain` anexa o domínio base a cada palavra na lista de palavras.

Analise os resultados cuidadosamente, anotando quaisquer descobertas incomuns ou interessantes. Pode ser necessária uma investigação mais aprofundada para confirmar a existência e a funcionalidade dos hosts virtuais descobertos.

Há alguns outros argumentos que vale a pena conhecer:

- Considere usar o sinalizador `-t` para aumentar o número de threads e obter uma varredura mais rápida.
- O  sinalizador`-k` pode ignorar erros de certificado SSL/TLS.
- Você pode usar o sinalizador `-o` para salvar a saída em um arquivo para análise posterior.

```shell-session
NycolasES6@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://inlanefreight.htb:81
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: forum.inlanefreight.htb:81 Status: 200 [Size: 100]
[...]
Progress: 114441 / 114442 (100.00%)
===============================================================
Finished
===============================================================
```



















































































