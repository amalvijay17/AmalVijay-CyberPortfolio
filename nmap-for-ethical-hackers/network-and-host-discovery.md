## Ping Scan, ARP Discovery, and Remote Communication

### Ping Scan - Local Network
nmap -sn 192.168.100.0/24

The MAC address is essential for identifying where data should go within the local network.  
When you send data, your device includes the receiver’s MAC address in the Ethernet header, allowing the switch to deliver it correctly.  
This process occurs at Layer 2 (Data Link Layer), which relies on MAC addresses for local communication.

When sending data to another device on the same network, you know its IP but not its MAC address.  
That’s where ARP (Address Resolution Protocol) comes in.

### How ARP Works
1. **ARP Request (Broadcast)**  
   "Who has 192.168.100.3? Tell me 192.168.100.2!"  
   This packet is broadcast to all devices on the network.  
   Opcode = 1

2. **ARP Reply (Unicast)**  
   "I’m 192.168.100.3, my MAC is AA:BB:CC:DD:EE:FF."  
   Sent directly back to the requester.  
   Opcode = 2

### Notes
- Wireshark filter for ARP replies:
  arp.opcode == 2
- Too much ARP traffic could mean someone is scanning the network (e.g., using Nmap).
- This scan operates only within the local subnet.

---

### Remote Communication - Is It Really a Ping?
When communicating outside your local network, you only need the MAC address of your router.  
Routers are responsible for forwarding packets between networks.

#### Scenario
Laptop A (Singapore) → Laptop B (USA, IP: 203.0.113.10)

1. **Check Local vs Remote**
   - Laptop A: 192.168.1.5 (private IP)
   - Laptop B: 203.0.113.10 (public IP)
   - Since it’s remote, the packet is sent to the default gateway (router).

2. **Send Packet to Router**
   - Ethernet destination MAC = router’s MAC  
   - IP destination = 203.0.113.10  
   - The laptop doesn’t need Laptop B’s MAC.

3. **Router Forwarding**
   - Routers send packets hop by hop across the internet.
   - Each router changes only the next-hop MAC address.
   - IP destination remains the same.

4. **Final Delivery**
   - The final router in the USA uses ARP to get Laptop B’s MAC.
   - Data is then delivered locally to Laptop B.

5. **Response**
   - Laptop B replies.
   - The reply travels back through NAT to Laptop A.

You never need to know the MAC address of a remote system — only routers handle that at each hop.

### Example
nmap -sn 45.33.32.0-10

**Note:**  
- ICMP pings usually need admin/root privileges.  
- Linux allows unprivileged ICMP pings.  
- Without privileges, Nmap switches to TCP-based pings (SYN [-PS], ACK [-PA], or Connect [-sT]).

---

### Deep Dive into the Default Scan
Example:  
nmap scanme.nmap.org

**Wireshark Tip:**  
- Go to: Statistics → Capture File Properties → Edit Comments  
  Paste your commands here to save both packet data and scan results.

#### DNS Lookups
| Query Type | Purpose | Address Type |
|-------------|----------|---------------|
| A | Standard DNS query | IPv4 |
| AAAA | Extended query | IPv6 |
| Reverse DNS | Converts IP → Domain Name |

#### Packet Filters
| Purpose | Wireshark Filter |
|----------|------------------|
| Open ports (SYN,ACK) | tcp.flags == 0x0012 |
| Filtered ports (SYN only) | tcp.port == 25 |
| UDP traffic | Statistics → Conversations (UDP) |

#### Why Nmap Doesn’t Use ICMP by Default
Many systems ignore or block ICMP Echo Requests.  
If Nmap relied on ICMP only, it might wrongly mark active hosts as down.  
To avoid this, Nmap uses alternative discovery methods (ARP, TCP SYN, etc.).
