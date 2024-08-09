Local File Inclusion permite a leitura de arquivos no servidor alvo. Sempre que virmos um link ou parâmetro que chama, ler ou baixa algum arquivo podemos testar essa vulnerabilidade.

<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/?file=about.php">
Podemos injetar o caminho de algum arquivo do sistema.
<input style="font-size:18pt;padding:5px;width:100%;" value="http://corp.com/?file=../../../../../../etc/passwd">
