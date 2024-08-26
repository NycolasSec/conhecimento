Como testadores de penetração, frequentemente acessamos dados sensíveis, como credenciais e informações críticas sobre a infraestrutura de rede e o Active Directory (AD). É essencial criptografar esses dados ou usar conexões seguras, como SSH, SFTP e HTTPS. No entanto, quando essas opções não estão disponíveis, precisamos adotar abordagens alternativas para proteger as informações.

Criptografar dados ou arquivos antes da transferência é essencial para evitar que sejam lidos se interceptados. Vazamentos de dados durante um teste de penetração podem ter consequências graves para o testador, sua empresa e o cliente. Portanto, é crucial agir com profissionalismo e responsabilidade, tomando todas as medidas necessárias para proteger as informações durante uma avaliação.

## Criptografia de arquivos no Windows
Muitos métodos diferentes podem ser usados ​​para criptografar arquivos e informações em sistemas Windows. Um dos métodos mais simples é o script PowerShell [Invoke-AESEncryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1). Este script é pequeno e fornece criptografia de arquivos e strings.

#### Invocar-AESEncryption.ps1
```powershell
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text" 

Description
-----------
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
 
Description
-----------
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
 
Description
-----------
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
 
Description
-----------
Decrypts the file "file.bin.aes" and outputs an encrypted file "file.bin"
#>
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
```

Podemos usar qualquer método de transferência de arquivo mostrado anteriormente para obter esse arquivo em um host de destino. Após o script ter sido transferido, ele só precisa ser importado como um módulo, conforme mostrado abaixo.

#### Módulo de importação Invoke-AESEncryption.ps1
```powershell-session
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1
```

Após o script ser importado, ele pode criptografar strings ou arquivos, como mostrado nos exemplos a seguir. Este comando cria um arquivo criptografado com o mesmo nome do arquivo criptografado, mas com a extensão "`.aes`."

#### Exemplo de criptografia de arquivo
```powershell-session
PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

File encrypted to C:\htb\scan-results.txt.aes
PS C:\htb> ls

    Directory: C:\htb

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/18/2020  12:17 AM           9734 Invoke-AESEncryption.ps1
-a----        11/18/2020  12:19 PM           1724 scan-results.txt
-a----        11/18/2020  12:20 PM           3448 scan-results.txt.aes
```


Usar senhas  fortes e únicas para criptografia para cada empresa onde um teste de penetração é realizado é essencial. Isso é para evitar que arquivos e informações sensíveis sejam descriptografadas usando uma única senha que pode ter vazado e quebrado por terceiros.

## Criptografia de arquivos no Linux
[O OpenSSL](https://www.openssl.org/) é frequentemente incluído em distribuições Linux, com administradores de sistemas usando-o para gerar certificados de segurança, entre outras tarefas. O OpenSSL pode ser usado para enviar arquivos "estilo nc" para criptografar arquivos.

Para criptografar um arquivo usando `openssl`podemos selecionar diferentes cifras, veja [a página de manual do OpenSSL](https://www.openssl.org/docs/man1.1.1/man1/openssl-enc.html) . Vamos usar `-aes256`como exemplo. Também podemos substituir as contagens de iterações padrão com a opção `-iter 100000`e adicionar a opção `-pbkdf2`para usar o algoritmo Password-Based Key Derivation Function 2. Quando apertamos enter, precisaremos fornecer uma senha.

#### Criptografando /etc/passwd com openssl
```shell-session
NycolasES6@htb[/htb]$ openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

enter aes-256-cbc encryption password:                                                         
Verifying - enter aes-256-cbc encryption password:       
```

Lembre-se de usar uma senha forte e única para evitar ataques de cracking de força bruta caso uma parte não autorizada obtenha o arquivo. Para descriptografar o arquivo, podemos usar o seguinte comando:

#### Descriptografar passwd.enc com openssl
```shell-session
NycolasES6@htb[/htb]$ openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd                    

enter aes-256-cbc decryption password:
```






















































































