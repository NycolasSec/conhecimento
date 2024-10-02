Wireshark é capaz de capturar dados ao vivo de diferentes formatos, como WIFI, USB e Bluetooth e salva-lo em diferentes formatos. Wireshark permite a inspeção muito mais profunda do que outras ferramentas.

### Features and Capabilities
- Deep packet inspection for hundreds of different protocols
- Graphical and TTY interfaces
- Capable of running on most Operating systems
- Ethernet, IEEE 802.11, PPP/HDLC, ATM, Bluetooth, USB, Token Ring, Frame Relay, FDDI, among others
- Decryption capabilities for IPsec, ISAKMP, Kerberos, SNMPv3, SSL/TLS, WEP, and WPA/WPA2.
- Many many more ...

## TShark VS. Wireshark
TShark is a terminal tool based on Wireshark. TShark shares many of the same features that are included in Wireshark and even shares syntax options. Wireshark is the feature rich GUI option for traffic capture and analysis. 

#### Basic TShark Switches

|**Switch Command**|**Result**|
|:-:|---|
|D|Will display any interfaces available to capture from and then exit out.|
|L|Will list the Link-layer mediums you can capture from and then exit out. (ethernet as an example)|
|i|choose an interface to capture from. (-i eth0)|
|f|packet filter in libpcap syntax. Used during capture.|
|c|Grab a specific number of packets, then quit the program. Defines a stop condition.|
|a|Defines an autostop condition. Can be after a duration, specific file size, or after a certain number of packets.|
|r (pcap-file)|Read from a file.|
|W (pcap-file)|Write into a file using the pcapng format.|
|P|Will print the packet summary while writing into a file (-W)|
|x|will add Hex and ASCII output into the capture.|
|h|See the help menu|

#### Selecting an interface  & Writing to a File
```sh
sudo tshark -i eth0 -w /tmp/test.pcap
```

#### Applying Filters
```sh
sudo tshark -i eth0 -f "host 172.16.146.2"
```
![[Pasted image 20240930092838.png]]
`-f` allows us to apply filters to the capture. In the example we utilized `host`, but you can use almost any filter Wireshark recognizes.

## Termshark
Termshark is a text-based user interface application that provides the user with a Wireshark-like interface right in your terminal window.

#### Termshark
![[termshark.webp]]

Termshark can be found at [Termshark](https://github.com/gcla/termshark). It can be built from the source by cloning the repo, or pull down one of the current stable releases from https://github.com/gcla/termshark/releases , extract the file, and hit the ground running.

For help navigating this TUI, see image below.

#### Termshark Help 
![[termshark-help.webp]]
To start Termshark, issue the same strings, much like TShark or tcpdump. We can specify an interface to capture on, filters, and other settings from the terminal. The Termshark window will not open until it senses traffic in its capture filter. So give it a second if nothing happens.

### Wireshark GUI Walkthrough
Let's dissect this view of the Wireshark GUI.

#### Wireshark GUI
![[wireshark-interface.webp]]

Three Main Panes: `See Figure above`

1. Packet List: `Orange`
    - In this window, we see a summary line of each packet that includes the fields listed below by default. We can add or remove columns to change what information is presented.
        - Number- Order the packet arrived in Wireshark
        - Time- Unix time format
        - Source- Source IP
        - Destination- Destination IP
        - Protocol- The protocol used (TCP, UDP, DNS, ETC.)
        - Information- Information about the packet. This field can vary based on the type of protocol used within. It will show, for example, what type of query It is for a DNS packet.
2. Packet Details: `Blue`
    - The Packet Details window allows us to drill down into the packet to inspect the protocols with greater detail. It will break it down into chunks that we would expect following the typical OSI Model reference. `The packet is dissected into different encapsulation layers for inspection.`
    - Keep in mind, Wireshark will show this encapsulation in reverse order with lower layer encapsulation at the top of the window and higher levels at the bottom.
3. Packet Bytes: `Green`
    - The Packet Bytes window allows us to look at the packet contents in ASCII or hex output. As we select a field from the windows above, it will be highlighted in the Packet Bytes window and show us where that bit or byte falls within the overall packet.
    - This is a great way to validate that what we see in the Details pane is accurate and the interpretation Wireshark made matches the packet output.
    - Each line in the output contains the data offset, sixteen hexadecimal bytes, and sixteen ASCII bytes. Non-printable bytes are replaced with a period in the ASCII format.

### 
















































































