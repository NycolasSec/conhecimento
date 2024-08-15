## Servidor DNS primário

O servidor DNS primário é o servidor do arquivo de zona, que contém todas as informações autoritativas para um domínio e é responsável por administrar essa zona.

Os registros DNS de uma zona só podem ser editados no servidor DNS primário, que então atualiza os servidores DNS secundários.

## Servidor DNS secundário

Servidores DNS secundários contêm cópias somente leitura do arquivo de zona do servidor DNS primário. Esses servidores comparam seus dados com o servidor DNS primário em intervalos regulares e, portanto, servem como um servidor de backup.

É útil porque uma falha do servidor de nomes primário significa que conexões sem resolução de nomes não são mais possíveis. Para estabelecer conexões de qualquer maneira, o usuário teria que saber os endereços IP dos servidores contatados. No entanto, essa não é a regra.

## Arquivos de Zona

Já sabemos que os arquivos de zona contêm os registros e informações correspondentes para o domínio específico. Aqui distinguimos entre:

- `Primary zone`( `master zone`)
- `Secondary zone`( `slave zone`)

O `secondary zone`no servidor DNS secundário serve como um substituto para o `primary zone`no servidor DNS primário se o servidor DNS primário se tornar inacessível. A criação e transferência da cópia `primary zone` do servidor DNS primário para o servidor DNS secundário é chamada de " `zone transfer`."

A atualização dos arquivos de zona só pode ser feita no servidor DNS primário, que então atualiza o servidor DNS secundário. Cada arquivo de zona pode ter apenas um servidor DNS primário e um número ilimitado de servidores DNS secundários.

Também podemos considerar o TLD " `.com`," como um gerente de empresa para um departamento específico, que conhece e gerencia apenas seus líderes de equipe ( `Zone 1`). Cada líder de equipe também conhece apenas seu gerente e os membros de sua equipe ( `Zone 2`e `Zone 3`).

#### Zona 1
```shell-session
.
└── com.
    ├── domain-A.
    ├── domain-B.
    └── domain-C.
```

#### Zona 2
```shell-session
com.
└── domain-A.
    ├── blog.
    ├── ns1.
    └── www.
```

#### Zona 3
```shell-session
com.
└── domain-B.
    └── www2.
```

## Transferência de Zona

Existem dois tipos diferentes de transferências de zona.

- `AXFR`- Zona de transferência completa assíncrona
- `IXFR`- Transferência de Zona Incremental

Uma `AXFR` é uma transferência completa de todas as entradas do arquivo de zona. Em contraste com a transferência assíncrona completa, apenas os registros DNS alterados e novos do arquivo de zona são transferidos de uma  `IXFR`para os servidores DNS secundários.

Um problema com servidores DNS e transferências de zona é a falta de autenticação, permitindo que qualquer cliente solicite esses dados. Se o administrador não definir hosts ou endereços IP confiáveis que podem receber essas zonas, é possível consultar todo o arquivo de zona, revelando todos os endereços IP e hosts associados. Isso pode ampliar significativamente o vetor de ataque, especialmente se o escopo não for limitado. Muitos servidores DNS ainda estão mal configurados e permitem esse tipo de acesso.












































