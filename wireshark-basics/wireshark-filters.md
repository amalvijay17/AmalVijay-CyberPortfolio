# Wireshark Filters

Filters help refine and display specific packets or traffic in Wireshark.
By default, Wireshark captures all network traffic, which can easily reach thousands of packets.
Filters remove unnecessary traffic and show only the exact packets you want to analyze.

## Types of Filters

### Display Filters
Used after capturing packets.
Helps you analyze specific traffic from already captured data.

### Capture Filters
Used before capturing packets.
Allows you to decide what traffic to capture.

---

## Application Layer & Protocols

HTTP and HTTPS are both Application layer protocols.
All Application layer protocols either use TCP or UDP for delivering packets.

- HTTP uses TCP.
- HTTPS uses TCP with TLS (HTTP + TLS = HTTPS).
- DNS uses UDP.
- TLS encrypts HTTP traffic to make HTTPS.

Other filters: dns, dhcp, arp, icmpv6, http, tls, ssh, ftp

---

## Logical Operators

AND Operator: &&
OR Operator: || 
NOT Operator: !

Examples:
http or arp or ssh
http || arp || ssh
not tcp
not udp
not (tcp or udp)
!(tcp or udp)

---

## IP Address Filters

ip.addr==192.168.2.69 (Sent or Received)
ip.addr eq 192.168.2.69 (Sent or Received)

Combination example:
ip.addr==192.168.2.60 or ip.addr==192.168.2.61

Example using AND operator:
ip.addr eq 192.168.2.69 and udp
(Show only UDP packets that the IP address sent or received)

Negate example:
not (ip.addr==<IP1> or ip.addr==<IP2>)

ip.src (Packets that a particular IP has sent)
ip.dst (Display packets that an IP has received)

---

## Port Filtering

Port filtering allows filtering traffic based on port numbers.

Port numbers help ensure that data sent over the network reaches the right program or service.

Examples:
tcp.port==443 (TCP packets on port 443)
udp.port==443 (UDP packets on port 443, QUIC protocol also uses UDP)
tcp.port==443 or udp.port==443 (Both TCP and UDP on port 443)
udp.port==443 or tcp.port==80 (UDP packets on port 443 and TCP packets on port 80)
tcp.dstport==80 (TCP packets delivered to port 80)
tcp.srcport==80 (TCP packets originated from port 80)

Source Port: Port from which the data originates.
Destination Port: Port to which the data is delivered.

Examples:
HTTP → Port 80
FTP → Port 20

Extra:
tcp.port >= 1 && tcp.port <= 100

---

## Ethernet (MAC) Filters

eth.addr == <MAC address>
(Display all packets or frames that the MAC address has sent or received)

Combine with OR or use NOT operator (!)

To view packets that a particular MAC address sent or received:
eth.src == <MAC address>
eth.dst == <MAC address>

Add columns:
Ethernet II -> Source -> Right click -> Apply as Column (for destination too)

eth.src==<Source MAC> (Packets this MAC address sent)
eth.dst==<Destination MAC> (Packets this MAC address received)

---

## IN Operator

ip.addr in {<ip address or addresses>}
(All packets that the IP address sent and received)
If multiple IP addresses, separate with commas.

Examples:
ip.addr in {192.168.1.2, 192.168.2.3}
tcp.port in {80,443}
udp.port in {53,443}
eth.addr in {<MAC1>, <MAC2>}

---

## Contains and Matches Operators

### Contains
Filters packets containing a specific word or phrase.
Example:
tcp contains "login"
udp contains "ftp"

### Matches
Perl-based regular expression match.
Example:
tcp matches "(login|success)"

The matches operator overcomes contains limitations and can search multiple words/phrases.
It also supports regular expressions.

---

## Capture Filters

Two fields can be used for capture filters:
1. Welcome to Wireshark area
2. Capture options window

A network interface must be selected for capture.
If multiple interfaces exist, separate filters for each interface.

Example:
tcp (Turns green for valid filter)

Capture Options is the recommended area for capture filters.

Saved bookmarks are available:
"udp" in Capture options captures UDP traffic.

Logical operators work for both display and capture filters.

Combine operations:
tcp or udp (Captures both TCP and UDP)
not udp (Will not capture UDP traffic)

---

## Application Layer Protocol Capture Filters

http is a valid display filter but invalid capture filter.

Use port numbers for such cases:
port http, port https, port ssh, port ftp

Examples:
port ftp or port ssh or port pop3 or port smtp
port 21 (ftp)
port 80 (http)

Wireshark does not allow some application layer protocols (dns, dhcp) in capture filters.
Use port numbers:
port 53 (dns)
port 67 (dhcp)

---

## Host Filters

host <ip address> (Either source or destination)
If the device uses IPv6, combine IPv4 and IPv6 with OR operator:
host <ipv4> or host <ipv6>

Examples:
host <ipaddress> and port ftp
src host <ipaddress> (Source)
dst host <ipaddress> (Destination)
host <domain name> (Example: host www.google.com)
Multiple hosts: host 192.168.1.1 or host 10.0.0.2

---

## MAC (Ethernet) Host Filters

ether host <MAC address> (Capture frames that this MAC sends or receives)
ether src <MAC address> (Frames sent)
ether dst <MAC address> (Frames received)

Combine using OR operator.
Negate example:
not ether host <MAC address>

---

## Port Range

port 443 (HTTPS traffic)
tcp port 443 (TCP only)
udp port 443 (UDP only)
portrange 1-80
portrange 1-80 or 400-443

---

## Saving Capture Filters

Steps:
1. Capture -> Option
2. Select interface
3. Managed Saved Bookmarks -> Manage Capture Filters
4. (+) -> Scroll to bottom -> New capture filter
5. Double click to edit
6. Enter Filter name and expression
7. Press Enter -> OK

To remove filters: (-) button

Examples:
Filter Name: Web Traffic
Filter Expression: port 80 or port 443

---

## Summary

- Display Filters: Used after capturing packets
- Capture Filters: Used before capturing packets
- Combine filters with and, or, not
- Use in, contains, matches, port, and ip/mac filters for deep inspection
