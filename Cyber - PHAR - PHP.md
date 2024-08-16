O PHP tem o método PHAR para leitura de arquivos zip. Também podemos usa-lo como url.

Caso alguma aplicação use algum parâmetro como `?page=home`, podemos testar. Mas para usa-lo temos de saber o nome do arquivo, pois ele trabalha com arquivos locais.

##### Exemplo
```URL
http://itrc.ssg.htb/?page=phar://uploads/b1e91350ea990d09681e09f32b2d16fe05b8b634.zip/shell
```
 Aqui subimos um arquivo ``b1e91350ea990d09681e09f32b2d16fe05b8b634.zip`` que contém o arquivo `shell.php` dentro da pasta uploads. Quando vamos apontar o arquivo passamos sem a extensão ``.php``.
Assim o PHP vai tentar carregar o arquivo ``shell``, que teria o conteúdo que quisessemos.