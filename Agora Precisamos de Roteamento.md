#redes 
# Agora Precisamos de Roteamento

Na maioria das situações, queremos que os nossos dispositivos possam se conectar além da rede local: a outras residências, a empresas e à Internet. Os dispositivos que estão além do segmento de rede local são conhecidos como hosts remotos. Quando um dispositivo de origem envia um pacote a um dispositivo de destino remoto, é necessária a ajuda de roteadores e do roteamento. O roteamento é o processo de identificação do melhor caminho até um destino.

Um roteador é um dispositivo de rede que conecta várias redes IP de Camada 3. Na camada de distribuição da rede, os roteadores direcionam o tráfego e realizam outras funções essenciais em uma operação de rede eficiente. Os roteadores, como switches, conseguem decodificar e ler as mensagens que são enviadas para eles. Ao contrário dos switches, que tomam uma decisão de encaminhamento com base no endereço MAC da Camada 2, os roteadores fundamentam suas decisões de encaminhamento no endereço IP da Camada 3.

O formato do pacote contém os endereços IP dos hosts de destino e de origem, assim como os dados da mensagem que está sendo enviada entre eles. O roteador lê a porção de rede do endereço IP de destino e a utiliza para descobrir qual das redes conectadas é a melhor forma de encaminhar a mensagem para o destino.

Sempre que a porção de rede dos endereços IP dos hosts de origem e de destino não coincidir, deverá ser usado um roteador para encaminhar a mensagem. Quando um host localizado na rede 1.1.1.0 precisa enviar uma mensagem para um host na rede 5.5.5.0, ele encaminha a mensagem ao roteador. O roteador recebe a mensagem, desencapsula o quadro Ethernet e lê o endereço IP de destino no pacote IP. Em seguida, ele determina para onde deve encaminhar a mensagem. Ele reencapsula o pacote de volta em um novo quadro e encaminha o quadro para o destino.

#### O que é roteamento ?

O roteamento é um processo para determinar o melhor caminho até um destino.

