A tradução de uma linguagem de programação de alto nível para linguagem de máquina é realizada por um compilador, em um processo chamado compilação. O código que você escreve, chamado de código-fonte, é salvo em um arquivo-fonte (por exemplo, "proggie.c" para programas em C). Para escrever o código-fonte, é recomendado usar um editor de texto simples, como o Bloco de Notas, em vez de editores de texto com formatação, como o Microsoft Word.

Depois de escrever o código-fonte, ele precisa ser compilado. O compilador lê o código, verifica erros e, se nenhum for encontrado, gera um arquivo executável, que é o programa traduzido para a linguagem de máquina. O nome do arquivo executável depende do sistema operacional e do compilador utilizado. No Unix/Linux, o arquivo geralmente é chamado de "a.out", enquanto no Windows, ele pode ter o mesmo nome do arquivo-fonte, mas com a extensão ".exe".

Se houver erros simples, como usar o símbolo "#" em vez de "+", o compilador informará, mas não pode adivinhar erros mais sutis, como usar "-" em vez de "+". Isso significa que, embora o compilador seja uma ferramenta poderosa, ele não substitui a necessidade de desenvolvedores que entendam a lógica e a intenção por trás do código.

![[Pasted image 20240829185656.png]]

Temos que admitir que todo o processo é, na verdade, um pouco mais complicado.

Quando o código-fonte é dividido em vários arquivos, especialmente em projetos em equipe, a compilação ocorre em duas fases: primeiro, cada arquivo é compilado em código de máquina; depois, os códigos resultantes são combinados em um único programa, em um processo chamado **linking**, realizado por um programa chamado **linker**.