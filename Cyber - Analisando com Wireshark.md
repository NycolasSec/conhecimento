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

### Other Notable Features
When looking at the Wireshark interface, we will notice a few different option areas and radial buttons. These areas are control points in which we can modify the interface and our view of the packets in the current capture. 

#### Wireshark Menu

![[wireshark-menu.webp]]

## Performing our first capture in Wireshark
Starting a capture with Wireshark is a simple endeavor. The gif below will show the steps.

#### Steps To Start A Capture
![[first-capture-ws.gif]]

Keep in mind, any time we change the capture options, Wireshark will restart the trace. Much like TCPDump, Wireshark has capture and display filter options that can be used.


## The Basics

#### The Toolbar
![[Pasted image 20241007084100.jpg]]

Wireshark's Toolbar is a central point to manage the many features Wireshark includes. From here, we can start and stop captures, change interfaces, open and save .pcap files and apply different filters or analysis add-ins.

#### How to Save a Capture
Let's say we need to capture what we have in our window currently for troubleshooting later. Saving a capture is super simple:

- Select File ⇢ save OR
- From the toolbar, select the file option and choose where to save the file and in what format.

Be aware that Wireshark can save captures into multiple formats. Choose the one needed for the scenario, but we will use the `.pcap` format for now.

## Pre-capture and Post-capture Processing and Filtering
In Wireshark, traffic can be filtered using two types of filters: Capture filters (applied before capturing starts) and Display filters (used during or after capture). Although Wireshark offers many useful features, it can struggle with large capture files, slowing down display or analysis filtering. For large ``pcap`` files, it's recommended to split them into smaller chunks to improve performance.

#### Capture Filters
`Capture Filters-` are entered before the capture is started. These use BPF syntax like `host 214.15.2.30` much in the same fashion as TCPDump. We have fewer filter options this way, and a capture filter will drop all other traffic not explicitly meeting the criteria set. This is a great way to trim down the data you write to disk when troubleshooting a connection, such as capturing the conversations between two hosts.

Here is a table of common and helpful capture filters with a description of each:

|**Capture Filters**|**Result**|
|:-:|---|
|host x.x.x.x|Capture only traffic pertaining to a certain host|
|net x.x.x.x/24|Capture traffic to or from a specific network (using slash notation to specify the mask)|
|src/dst net x.x.x.x/24|Using src or dst net will only capture traffic sourcing from the specified network or destined to the target network|
|port #|will filter out all traffic except the port you specify|
|not port #|will capture everything except the port specified|
|port # and #|AND will concatenate your specified ports|
|portrange x-x|portrange will grab traffic from all ports within the range only|
|ip / ether / tcp|These filters will only grab traffic from specified protocol headers.|
|broadcast / multicast / unicast|Grabs a specific type of traffic. one to one, one to many, or one to all.|

#### Applying a Capture Filter
Before we apply a capture filter, let us take a look at the built-in filters. To do so: Click on the capture radial at the top of the Wireshark window → then select capture filters from the drop-down.

#### Filter List
![[capture-filter-list.webp]]

From here, we can modify the existing filters or add our own.

To apply the filter to a capture, we will: Click on the capture radial at the top of the Wireshark window → then select Options from the drop-down → in the new window select the drop-down for Capture filter for selected interfaces or type in the filter we wish to use. `below the red arrow in the picture below`

#### Applying A Capture Filter
![[how-to-add-cap.webp]]

#### Display Filters
`Display Filters-` are used while the capture is running and after the capture has stopped. Display filters are proprietary to Wireshark, which offers many different options for almost any protocol.

Here is a table of common and helpful display filters with a description of each:

|**Display Filters**|**Result**|
|:-:|---|
|ip.addr == x.x.x.x|Capture only traffic pertaining to a certain host. This is an OR statement.|
|ip.addr == x.x.x.x/24|Capture traffic pertaining to a specific network. This is an OR statement.|
|ip.src/dst == x.x.x.x|Capture traffic to or from a specific host|
|dns / tcp / ftp / arp / ip|filter traffic by a specific protocol. There are many more options.|
|tcp.port == x|filter by a specific tcp port.|
|tcp.port / udp.port != x|will capture everything except the port specified|
|and / or / not|AND will concatenate, OR will find either of two options, NOT will exclude your input option.|

#### Applying a Display Filter
Applying a display filter is even easier than a capture filter. From the main Wireshark capture window, all we need to do is: select the bookmark in the Toolbar → , then select an option from the drop-down. Alternatively, place the cursor in the text radial → and type in the filter we wish to use. If the field turns green, the filter is correct. `Just like in the image below.`

#### Applying Display Filters
![[display-filter.webp]]

When using capture and display filters, keep in mind that what we specify is taken in a literal sense. For example, filtering for port 80 traffic is not the same as filtering for HTTP. Think of ports and protocols more like guidelines instead of rigid rules. Ports can be bound and used for different purposes other than what they were originally intended. For example, filtering for HTTP will look for key markers that the protocol uses, such as GET/POST requests, and show results from them. Filtering for port 80 will show anything sent or received over that port regardless of the transport protocol.















Encontre evidências de varredura e filtro de porta para "tcp.flags.syn == True && tcp.flags.ack == True && tcp.len == 0"



172.31.35.23
172.31.40.241


HP LaserJet pro 4001dn Printer


0:/saveDevice/SavedJobs/InProgress/scheduled.ps1

LetsDefend Corp Company

/backup/jumphost/2023

172.31.23.97