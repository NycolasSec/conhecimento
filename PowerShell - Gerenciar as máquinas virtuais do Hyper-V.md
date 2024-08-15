O PowerShell oferece mais de 200 cmdlets para gerenciar VMs (máquinas virtuais), discos rígidos virtuais e outros componentes de um ambiente Hyper-V. Os cmdlets do Hyper-V estão disponíveis no módulo **Hyper-V** do PowerShell.

Os cmdlets do Hyper-V estão disponíveis quando você instala o recurso **Ferramentas de gerenciamento do Hyper-V** em um sistema operacional cliente Windows ou o recurso **Módulo Hyper-V para Windows PowerShell** no Windows Server.

**Os cmdlets do Hyper-V usam um de três prefixos:**
- "VM" para cmdlets de máquina virtual
- "VHD" para cmdlets de disco rígido virtual
- "VFD" para cmdlets de disquete virtual

A tabela a seguir lista cmdlets comumente usados para gerenciar máquinas virtuais do Hyper-V.

|**Cmdlet**|**Descrição**|
|---|---|
|**Get-VM**|Obtém as propriedades de uma VM|
|**Set-VM**|Define as propriedades de uma VM|
|**New-VM**|Cria uma nova VM|
|**Start-VM**|Inicia uma VM|
|**Stop-VM**|Interrompe uma VM|
|**Restart-VM**|Reinicia uma VM|
|**Suspend-VM**|Pausa uma VM|
|**Resume-VM**|Retoma uma VM pausada|
|**Import-VM**|Importa uma VM de um arquivo|
|**Export-VM**|Exporta uma VM para um arquivo|
|**Checkpoint-VM**|Cria um ponto de verificação de uma VM|