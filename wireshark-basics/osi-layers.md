# OSI Model — Overview and Layer Functions

The OSI Model (Open Systems Interconnection) is the foundation for understanding network architecture.  
It explains how two devices (on the same network or across different countries) communicate and send data to each other. The OSI model divides the complex process of data communication into **7 layers**, each with specific responsibilities, which makes troubleshooting and design easier.

## Why OSI matters
- Breaks complex transmission into smaller parts (layers).
- Helps pinpoint where problems occur.
- Each layer has defined duties and protocols.
- Useful for troubleshooting, design, and learning how data flows across networks.

## The 7 OSI layers (top → bottom)
1. Application Layer  
2. Presentation Layer  
3. Session Layer  
4. Transport Layer  
5. Network Layer  
6. Data Link Layer  
7. Physical Layer

---

## Application Layer (Layer 7)
- Closest to end users. Provides interface and protocols for network applications (web browsers, email clients, FTP, VoIP apps).
- **Not** the applications themselves — apps use Application layer protocols.
- Common protocols: HTTP, HTTPS, TELNET, DNS, SSH, FTP, SIP, SMTP, SNMP.
- Devices that operate at this layer: PCs, servers, mobile devices.
- PDU: **Data**

---

## Presentation Layer (Layer 6)
- Receives data from the Application layer.
- Main functions:
  1. **Translation** — convert data formats (e.g., ASCII ↔ EBCDIC), encoding to binary.
  2. **Encryption / Decryption** — e.g., TLS handles encryption for HTTPS.
  3. **Compression** — reduce data size for transmission.
- Example protocols: TLS, NCP, XDR.

---

## Session Layer (Layer 5)
- Manages sessions between communicating devices.
- Responsibilities:
  1. **Session Management** — create, maintain, terminate sessions; unique session IDs.
  2. **Authentication** — verify user identity (login processes).
  3. **Authorization** — determine permitted actions after authentication.
  4. **Checkpoints / Recovery** — session restoration (resume interrupted downloads).
- Example protocols: SIP, NetBIOS, SMB.

---

## Transport Layer (Layer 4)
- Receives data from Session layer and prepares it for Network layer.
- Key functions:
  1. **Segmentation** — split data into segments; assign sequence numbers.
     - Segment PDU, sequence numbers help reassemble in order.
     - Typical maximum bytes in a segment ~1460 (for common MTU).
  2. **Process-to-Process Delivery** — uses port numbers to deliver data to the correct application/process.
     - 65,535 ports; well-known ports 0–1023 (e.g., 80 HTTP, 443 HTTPS).
  3. **Flow Control** — ensure sender doesn't overwhelm receiver (window size).
  4. **Error Control** — checksums and retransmission (Automatic Repeat Request).
  5. **Reliable vs Unreliable Delivery**:
     - **TCP**: reliable, connection-oriented (reordering, ACKs, retransmit).
     - **UDP**: unreliable, connectionless, low-latency (used for live streams, VoIP, gaming).
  6. **Other protocols**: SCTP.
- Devices: Firewalls, Gateways (note: firewalls can filter at multiple layers).

---

## Network Layer (Layer 3)
- Receives segments from Transport layer and delivers them to the destination host.
- Main functions:
  1. **Host-to-Host Delivery** — turns segments into packets and moves them across networks.
  2. **Logical Addressing** — adds source and destination IP addresses to packets.
  3. **Routing** — selects the best path through interconnected networks (routers).
     - Routing protocols: EIGRP, BGP, OSPF, RIP.
  4. **Fragmentation** — split packets to match MTU of next network (MTU commonly 1500 bytes; jumbo frames ~9000 bytes).
- Protocols and supporting tech: IP, ICMP, ARP, IPsec, BGP, OSPF.
- Devices: Routers, firewalls (L3), gateways.
- Note: L3 filters by IP addresses & protocols; L4 filters by ports, flags, connection state.

---

## Data Link Layer (Layer 2)
- Receives packets from Network layer and prepares frames for the Physical layer.
- Functions:
  1. **Node-to-Node Delivery** — deliver frames from one node to the next node along the path.
  2. **Flow Control** — control flow between adjacent nodes.
  3. **Error Control** — detect/correct errors at each hop (e.g., CRC, parity).
  4. **Physical Addressing** — MAC addresses assigned to NICs; used for local delivery.
  5. **Framing** — wrap packets into frames (header + body + trailer).
  6. **Media Access Control** — manage access to shared medium (avoid collisions).
- Sub-layers:
  - **LLC (Logical Link Control)** — flow control, error detection, synchronization.
  - **MAC (Media Access Control)** — collision avoidance/control (historically CSMA/CD).
- Modern notes:
  - Switches provide dedicated links and full-duplex, eliminating collisions in typical LANs.
- Protocols and tech: Ethernet, Frame Relay, ATM, HDLC, PPP.
- Devices: Switches, bridges, NICs.

---

## Physical Layer (Layer 1)
- Lowest layer; handles transmission of raw bits over physical media.
- Functions:
  - Convert digital bits into electrical, optical, or radio signals suitable for medium (copper, fiber, wireless).
  - Define connectors, voltages, frequencies, data rates, modulation.
- Devices: Cables, modems, hubs, repeaters, wireless access points.
- PDU: **Bits**

---

## Encapsulation & Decapsulation (How data travels)
- **Encapsulation** (sender side):
  1. Application produces data → Presentation/Session → Transport segments (adds TCP/UDP header) → Network adds IP header → Data Link wraps into frame (header + trailer) → Physical sends signals.
  2. Each layer adds its own header (and DLL adds trailer).
- **Decapsulation** (receiver side):
  1. Physical receives signals → converts back to bits → Data Link checks MAC, removes header/trailer → Network checks IP, removes IP header → Transport reassembles segments, checks ports, hands to Session → Presentation decrypts/decodes → Application receives data.

---

## Important Comparisons / Notes
- **Frames** at Data Link layer, **Packets** at Network layer, **Segments** at Transport layer.
- **MAC address** = physical address (NIC-level). **IP address** = logical address (host-level).
- **Switches** operate at Data Link layer (use MAC addresses). **Hubs** operate at Physical layer (repeat signals to all ports).
- **Flow control & error control** exist at multiple layers (DLL for node-to-node, Transport for end-to-end).
- **Fragmentation** occurs at Network layer to meet MTU constraints; **Segmentation** occurs at Transport layer for application data.
- Use OSI model to diagnose where an issue sits (hardware, addressing, routing, transport reliability, session, presentation, or application).

---

## Quick Reference: Common Protocols by Layer
- Layer 7 (Application): HTTP, HTTPS, FTP, SMTP, DNS, SSH, SIP  
- Layer 6 (Presentation): TLS, compression, encoding (ASCII/EBCDIC)  
- Layer 5 (Session): SIP, SMB, NetBIOS  
- Layer 4 (Transport): TCP, UDP, SCTP  
- Layer 3 (Network): IP, ICMP, ARP, OSPF, BGP, RIP  
- Layer 2 (Data Link): Ethernet, PPP, HDLC, Frame Relay  
- Layer 1 (Physical): DSL, Ethernet physical specs, optical, radio

---

## Summary
- OSI model breaks network communication into 7 layers to simplify design, troubleshooting, and learning.
- Layers add or remove headers/trailers during encapsulation/decapsulation.
- Each layer has distinct responsibilities and associated devices/protocols.
- Understanding OSI is essential for diagnosing network issues and designing reliable networks.
