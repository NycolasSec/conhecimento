O modificador _short_ não pode ser usado junto com o flutuador, mas podemos usar o modificador _long_ aqui. Supõe-se que o tipo long ``float`` seja sinônimo de outro tipo chamado double.

As variáveis do tipo double podem diferir das variáveis do tipo float, não apenas no **alcance**, mas também na **precisão**.

Por exemplo, esperamos que o valor:
``1111111111111111111.111111111111111111111``

serão armazenados por um tipo específico de computador como:
``1111111131851653120.000000``

Dizemos que a variável salva (apenas) **8 dígitos precisos**. Isso está dentro da precisão esperada de 32 bits long floats. Usar um duplo (que geralmente tem 64 bits) garante que a variável salvará um número mais significativo de dígitos – cerca de **15-17**.














