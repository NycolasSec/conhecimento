_Módulos_ são grupos de funcionalidades relacionadas do PowerShell que são reunidas em uma única unidade. Eles ajudam a organizar os cmdlets em unidades distribuíveis. A Microsoft e outras empresas de software fornecem módulos como parte das ferramentas de gerenciamento para os aplicativos e serviços.

Para usar os cmdlets de um módulo, o módulo deve ser carregado na sessão atual do PowerShell. Costuma ser carregado automaticamente, mas dependendo da configuração pode ser que seja exigido que o modo seja carregado explicitamente com o comando `Impor-Module`.

## Carregamento automático

Por padrão, essas pastas nos caminhos de carga do módulo incluem **%systemdir%\\WindowsPowerShell\\v1.0\\Modules** e **%userprofiles%\\Documents\\WindowsPowerShell\\Modules**.

A lista de pastas é armazenada na variável de ambiente `$env:PSModulePath`. Quando você importa explicitamente um módulo por nome, o PowerShell verifica os locais referenciados por essa variável de ambiente.

#### Para o PowerShell 7, o **PSModulePath** inclui os seguintes locais:
`C:\Users\<user>\Documents\PowerShell\Modules` `C:\Program Files\PowerShell\Modules` `C:\Program Files\PowerShell\7\Modules` `C:\Program Files\WindowsPowerShell\Modules` `C:\WINDOWS\System32\WindowsPowerShell\v1.0\Modules`






















