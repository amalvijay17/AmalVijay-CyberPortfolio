# TCP/IP Model & Wireless Fundamentals

## TCP/IP (Prerequisite: OSI model)

The TCP/IP model is named after **Transmission Control Protocol (TCP)** and **Internet Protocol (IP)**.  
It is a practical suite of protocols that enables computers to communicate over networks (including the Internet). TCP/IP is implemented on virtually every system and is the foundation of today’s Internet.

### Why TCP/IP and OSI?
- The **OSI model** is a conceptual/theoretical 7-layer model useful for learning and troubleshooting.
- The **TCP/IP model** was developed and implemented earlier and widely adopted, so it became the basis for real networks.
- TCP/IP is a practical, simplified model inspired by OSI. OSI was more detailed but slower to implement; TCP/IP reused existing protocols and was easier to deploy.

### Historical context
- Two organizations competed to define models: ISO (OSI) and US DoD (TCP/IP).
- TCP/IP used existing protocols and was implemented faster, gaining industry adoption.
- OSI remained a reference model — valuable for teaching and troubleshooting — even though TCP/IP is used in practice.

---

## TCP/IP Model Versions

### Original (4-layer)
1. Application
2. Transport
3. Internet
4. Link (Network Access)

### Updated / Commonly used (5-layer)
1. Application
2. Transport
3. Network
4. Data Link
5. Physical

> The updated model splits the original Link layer into Data Link + Physical, and renames Internet → Network.  
> The Application layer in TCP/IP covers OSI’s Application, Presentation, and Session layers.

---

## Mapping & Notes
- TCP/IP merges OSI layers 5–7 into a single **Application** layer (handles sessions, presentation, and application functions).
- Remaining layers (Transport, Network, Data Link, Physical) align closely with OSI functionality.
- OSI remains a useful conceptual model for pinpointing issues (e.g., Layer 3 = addressing/routing, Layer 1 = cables/hardware).

---

## TCP/IP Layer Details

### Application Layer
- Implements high-level protocols and user services.
- Functions include encoding/decoding, encryption/decryption, session management (functions OSI divided among Presentation & Session).
- PDU: **Data**
- Protocols: TELNET, FTP, SSH, SMTP, SNTP, SIP, HTTP, HTTPS, LDP
- Devices: PCs, phones, servers

### Transport Layer
- Functions: segmentation, flow control, error control, end-to-end delivery.
- PDU: **Segment**
- Protocols: TCP, UDP, SCTP
- Devices: Gateways, Firewalls

### Network Layer
- Responsible for logical addressing (IP), routing, fragmentation, and host-to-host delivery.
- PDU: **Packet**
- Protocols: IP, IPsec, ICMP, EGP, EIGRP, OSPF, RIP
- Devices: Routers, Firewalls

### Data Link Layer
- Formats packets into frames, adds MAC addresses (physical addressing), handles node-to-node delivery, flow & error control, media access.
- PDU: **Frame**
- Protocols: Ethernet, HDLC, PPP, ATM, 802.11 MAC, Frame Relay
- Devices: Switches, NICs, Bridges

### Physical Layer
- Transmits raw bits over physical medium; handles electrical/optical/radio signals, connectors, modulation.
- PDU: **Bits**
- Protocols/standards: DSL, Bluetooth, 802.11 physical specs, etc.
- Devices: Repeaters, Modems, Hubs, Cables

---

## Quick Recap: PDU Terms
- Application: **Data**
- Transport: **Segment**
- Network: **Packet**
- Data Link: **Frame**
- Physical: **Bits**

---

## Fundamentals of Wireless Security & WLAN

### WLAN Overview
- **WLAN** = Wireless Local Area Network (Wi-Fi).
- Provides wireless connectivity to devices (laptops, phones, tablets) within a limited area (home, building, campus).
- Uses **radio waves** to transmit data through the air.

### Wired vs Wireless
- Wired: uses Ethernet cables.
- Wireless: uses Wi-Fi (radio waves) according to 802.11 standards.

### 802.11 Standard
- 802.11 is the family of standards that defines Wi-Fi operation:
  - Data transmission methods
  - Device management
  - Connection & disconnection procedures
  - Authentication and security mechanisms
- 802.11 versions define improvements to speed, range, encryption, and features.

### Wireless Communication Complexity
- Wireless involves additional considerations:
  - Securely transmitting data over air (security/encryption)
  - Managing device association/disassociation
  - Authentication (who can join)
  - Handling interference, roaming, QoS, and more

### Role of 802.11
- Acts as a foundational protocol for wireless networks (defines MAC + PHY behaviors for Wi-Fi).
- Works in conjunction with other protocols to provide complete wireless connectivity and security.

---

## Final Notes
- TCP/IP is the practical, implemented model that runs the Internet; OSI is the detailed conceptual model used for teaching and troubleshooting.
- Understanding both models helps in network design, troubleshooting, and mapping protocols/devices to the correct layer.
- Wireless networking (WLAN / 802.11) introduces extra layers of security and device management concerns beyond wired networks.
