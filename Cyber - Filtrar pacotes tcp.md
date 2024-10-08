##TCP FLAGS##

Unskilled Attackers Pester Real Security Folks
==============================================
                     TCPDUMP FLAGS
Unskilled =  URG  =  (Not Displayed in Flag Field, Displayed elsewhere) 
Attackers =  ACK  =  (Not Displayed in Flag Field, Displayed elsewhere)
Pester    =  PSH  =  [P] (Push Data)
Real      =  RST  =  [R] (Reset Connection)
Security  =  SYN  =  [S] (Start Connection)
Folks     =  FIN  =  [F] (Finish Connection)
          SYN-ACK =  [S.] (SynAcK Packet)
                     [.] (No Flag Set)

##USAGE##
Basic communication // see the basics without many options
# tcpdump -nS

Basic communication (very verbose) // see a good amount of traffic, with verbosity and no name help
# tcpdump -nnvvS

A deeper look at the traffic // adds -X for payload but doesn’t grab any more of the packet
# tcpdump -nnvvXS

Heavy packet viewing // the final “s” increases the snaplength, grabbing the whole packet
# tcpdump -nnvvXSs 1514

host // look for traffic based on IP address (also works with hostname if you’re not using -n) 
# tcpdump host 1.2.3.4

src, dst // find traffic from only a source or destination (eliminates one side of a host conversation) 
# tcpdump src 2.3.4.5 
# tcpdump dst 3.4.5.6

net // capture an entire network using CIDR notation 
# tcpdump net 1.2.3.0/24

proto // works for tcp, udp, and icmp. Note that you don’t have to type proto 
# tcpdump icmp

port // see only traffic to or from a certain port 
# tcpdump port 3389

src, dst port // filter based on the source or destination port 
# tcpdump src port 1025 # tcpdump dst port 389

src/dst, port, protocol // combine all three 
# tcpdump src port 1025 and tcp 
# tcpdump udp and src port 53

You also have the option to filter by a range of ports instead of declaring them individually, and to only see packets that are above or below a certain size.

Port Ranges // see traffic to any port in a range 
tcpdump portrange 21-23

Packet Size Filter // only see packets below or above a certain size (in bytes) 
tcpdump less 32 
tcpdump greater 128
[ You can use the symbols for less than, greater than, and less than or equal / greater than or equal signs as well. ]

// filtering for size using symbols 
tcpdump > 32 
tcpdump <= 128

[ Note: Only the PSH, RST, SYN, and FIN flags are displayed in tcpdump‘s flag field output. URGs and ACKs are displayed, but they are shown elsewhere in the output rather than in the flags field ]

Keep in mind the reasons these filters work. The filters above find these various packets because tcp[13] looks at offset 13 in the TCP header, the number represents the location within the byte, and the !=0 means that the flag in question is set to 1, i.e. it’s on.

##### Show all URG packets:
``tcpdump 'tcp[13] & 32 != 0'``

##### Show all ACK packets:
``tcpdump 'tcp[13] & 16 != 0'``

##### Show all PSH packets:
``tcpdump 'tcp[13] & 8 != 0'``

##### Show all RST packets:
``tcpdump 'tcp[13] & 4 != 0'``

##### Show all SYN packets:
``tcpdump 'tcp[13] & 2 != 0'``

##### Show all FIN packets:
``tcpdump 'tcp[13] & 1 != 0'``

##### Show all SYN-ACK packets:
``tcpdump 'tcp[13] = 18'``

##### Show icmp echo request and reply
``tcpdump -n icmp and 'icmp[0] != 8 and icmp[0] != 0'``

##### Show all IP packets with a non-zero TOS field (one byte TOS field is at offset 1 in IP header):
``# tcpdump -v -n ip and ip[1]!=0``

##### Show all IP packets with TTL less than some value (on byte TTL field is at offset 8 in IP header):
``tcpdump -v ip and 'ip[8]<2'``

##### Show TCP SYN packets:
``tcpdump -n tcp and port 80 and 'tcp[tcpflags] & tcp-syn == tcp-syn'``

``tcpdump tcp and port 80 and 'tcp[tcpflags] == tcp-syn'``

``tcpdump -i <interface> "tcp[tcpflags] & (tcp-syn) != 0"``

##### Show TCP ACK packets:
``# tcpdump -i <interface> "tcp[tcpflags] & (tcp-ack) != 0"``

Show TCP SYN/ACK packets (typically, responses from servers):
``tcpdump -n tcp and 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'``

``tcpdump -n tcp and 'tcp[tcpflags] & tcp-syn == tcp-syn' and 'tcp[tcpflags] & tcp-ack == tcp-ack'``

``tcpdump -i <interface> "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"``

##### Show TCP FIN packets:
``tcpdump -i <interface> "tcp[tcpflags] & (tcp-fin) != 0"``

##### Show ARP Packets with MAC address
``tcpdump -vv -e -nn ether proto 0x0806``

##### Show packets of a specified length (IP packet length (16 bits) is located at offset 2 in IP header):
``tcpdump -l icmp and '(ip[2:2]>50)' -w - |tcpdump -r - -v ip and '(ip[2:2]<60)'``

More Details: 
http://danielmiessler.com/study/tcpdump/