# TCP Header, 3-Way Handshake, MTU, MSS & IPv4 Header

## TCP — Overview
- TCP = Transmission Control Protocol. Core Transport-layer protocol in TCP/IP.
- Purpose: reliable, ordered, error-checked delivery of a byte stream between applications.
- TCP is **connection-oriented**: establishes a connection before data exchange and tears it down afterward.
- Guarantees: in-order delivery, retransmission of lost data, error detection (checksums), flow control, congestion control.

---

## TCP Header — Fields & Sizes
> TCP header minimum size = **20 bytes**, maximum = **60 bytes** (options up to 40 bytes).

- **Source Port (16 bits)**  
  Port number of the sending application/process.

- **Destination Port (16 bits)**  
  Port number of the receiving application/process.

- **Sequence Number (32 bits)**  
  Sequence number of the **first byte** in this segment. TCP numbers bytes; sequence field refers to the first byte’s number.

- **Acknowledgment Number (32 bits)**  
  Next sequence number the receiver expects (i.e., last received sequence + 1). Used to acknowledge received bytes.

- **Data Offset / Header Length (4 bits)**  
  Number of 32-bit words in the TCP header. Value range 5–15 (5 × 4 = 20 bytes min, 15 × 4 = 60 bytes max).

- **Reserved (6 bits)**  
  Reserved for future use (not used).

- **Control Flags / Control Bits (6 bits commonly described; nowadays 9 flags in total in modern descriptions)**  
  - **URG** — Urgent pointer field significant. Indicates urgent data.  
  - **ACK** — Acknowledgment field is significant.  
  - **PSH** — Push: push buffered data to the application immediately.  
  - **RST** — Reset connection (abrupt termination).  
  - **SYN** — Synchronize sequence numbers; used to initiate a connection.  
  - **FIN** — Finish: gracefully close connection.  
  (Note: exact number/style of flags in implementations can show more bits — e.g., CWR, ECE etc.)

- **Window Size (16 bits)**  
  TCP receive window: number of bytes the sender is allowed to transmit without receiving an ACK (i.e., unacknowledged bytes the receiver can accept). Advertised by each side.

- **Checksum (16 bits)**  
  Covers TCP header + data + pseudo-header for error detection.

- **Urgent Pointer (16 bits)**  
  If URG is set, points to the sequence number after the urgent data. Marks urgent data range.

- **Options (0–40 bytes)**  
  Optional. Common options: MSS (Maximum Segment Size), Window Scale, SACK Permitted, Timestamps, etc.

---

## Important TCP Concepts

### Sequence & Acknowledgment (example)
- Sender chooses a random Initial Sequence Number (ISN).
- Each byte has a sequence number. Sequence field contains the number of the first byte in the segment.
- Receiver acknowledges by sending (last-byte-seq + 1) in the Acknowledgment Number field.
- Example: segment contains bytes with sequence numbers 2021..2024 → receiver ACK = 2025.

### Header length calculation (Data Offset)
- Data Offset value × 4 = header length in bytes.
- Example: Data Offset = `5` → 5 × 4 = 20 bytes (minimum).
- Valid Data Offset values: 5–15 → header lengths 20–60 bytes.

---

## TCP Control Flags Summary
- **SYN** — start a connection (used in handshake).
- **SYN+ACK** — server replies with SYN+ACK to agree and provide its ISN.
- **ACK** — acknowledges received data/sequence number.
- **FIN** — graceful close (no more data).
- **RST** — immediate abort/reset connection (abnormal termination).
- **PSH** — push buffered data to application immediately.
- **URG** — urgent pointer valid (urgent data).

---

## 3-Way Handshake (conceptual)
1. **Client → Server:** SYN (Client ISN)  
2. **Server → Client:** SYN + ACK (Server ISN, ACK = Client ISN + 1)  
3. **Client → Server:** ACK (ACK = Server ISN + 1)  
- After step 3, connection established (full-duplex).
- SYN packets carry ISN; relative sequence numbering in tools like Wireshark often start from 0 for readability.
- If server rejects connection, it may send RST (or RST+ACK).

---

## Finding TCP Conversations in Wireshark
- `tcp.stream eq 0` → first TCP conversation (streams numbered from 0).
- Show only SYN packets: `tcp.flags.syn == 1 and tcp.flags.ack != 1`
- Show SYN+ACK: `tcp.flags.syn == 1 and tcp.flags.ack == 1`
- Show only ACK: `tcp.flags.ack == 1 and tcp.flags.syn != 1`
- Conversations via menu: `Statistics → Conversations → TCP` (shows stream IDs).

---

## TCP Flow Control: Window & Window Scaling
- **Window Size**: a 16-bit field (max raw = 65,535). Advertised by receiver; limits how many unacknowledged bytes sender can send.
- **Window Scaling** (RFC 1323): extends effective window beyond 65,535 by a scale factor negotiated in SYN/SYN-ACK options.
  - Option contains a small exponent N; scale factor = 2^N.
  - Effective window = Window field × (2^N).
  - Max scale exponent typically 14 → very large effective window (~1 GB).
- Window scaling values are advertised in the SYN and SYN+ACK packets and used for the remainder of the connection.

---

## MSS — Maximum Segment Size (TCP option)
- **MSS**: maximum TCP payload (bytes) a side can accept in a single TCP segment.
- MSS is communicated in SYN and SYN+ACK options (once, during handshake).
- Relationship with MTU: MSS = MTU − IP header − TCP header (assuming minimal headers).
  - On Ethernet (MTU 1500): MSS typically = 1500 − 20 (IP) − 20 (TCP) = **1460 bytes**.
- MSS applies to TCP payload only; it does not include IP/TCP headers.

---

## MTU, Ethernet Frame & Relation to MSS
- **MTU** (Maximum Transmission Unit): maximum size of IP packet payload the data link/physical layer can carry (Ethernet default 1500 bytes).
- **Ethernet Frame structure** (high level):
  - Preamble (7 bytes) + SFD (1 byte) — added at Physical layer.
  - Destination MAC (6 bytes)
  - Source MAC (6 bytes)
  - Length / Type (2 bytes)
  - Data / Payload (46–1500 bytes) ← **this is where the IP packet goes** (MTU = 1500)
  - FCS (4 bytes)
- **Max Ethernet frame size** (without preamble/SFD) = 1518 bytes (14 bytes header + 1500 payload + 4 FCS). With preamble & SFD = 1526 bytes.
- **Minimum frame size** = 64 bytes (includes padding if payload < 46 bytes).
- MTU can be changed per interface but default Ethernet MTU = 1500.

---

## MSS vs MTU (concise)
- MTU = maximum size of the data link payload (Ethernet payload) — typically 1500 bytes.
- MSS = maximum TCP payload that fits inside an IP packet that will fit in the MTU after subtracting IP and TCP headers.
  - Typical MSS (Ethernet) = 1460 bytes.

---

## IPv4 Header — Key Fields (summary)
- **Version (4 bits):** `4` for IPv4.
- **Header Length (IHL) (4 bits):** number × 32-bit words → ×4 yields bytes (min 20, max 60).
- **DSCP / TOS (8 bits):** Quality of Service / traffic classification.
- **Total Length (16 bits):** total size of IP packet (header + data), max 65,535 bytes.
- **Identification (16 bits):** used for fragmentation/reassembly.
- **Flags (3 bits) & Fragment Offset (13 bits):** control fragmentation (DF, MF, offset).
- **Time To Live (TTL) (8 bits):** decremented at each hop; prevents infinite routing loops.
- **Protocol (8 bits):** indicates next-level protocol (e.g., 6 = TCP, 17 = UDP, 1 = ICMP).
- **Header Checksum (16 bits):** checksum over IP header fields (not payload).
- **Source Address (32 bits):** sender IP.
- **Destination Address (32 bits):** receiver IP.
- **Options (variable):** rarely used (source route, timestamps).

---

## Fragmentation (IPv4)
- IP may fragment packets to meet downstream MTU limits.
- Fragmentation fields: Identification, Flags (MF=more fragments), Fragment Offset.
- Fragments reassembled at destination using Identification + offsets.
- Path MTU issues can be mitigated by Path MTU Discovery (PMTUD) and proper MSS negotiation.

---

## Example Calculations & Notes
- **TCP header minimum**: Data Offset = 5 → 5 × 4 = 20 bytes.
- **Typical packet size calculation** (example):
  - TCP payload = 491 bytes  
  - TCP header = 20 bytes  
  - IP header = 20 bytes  
  - Ethernet header = 14 bytes  
  - Total on-wire bytes = 491 + 20 + 20 + 14 = **545 bytes**
- **Sequence numbering**: if a segment starts at seq=492 and carries 387 bytes, the last byte seq = 492 + 387 − 1 = 878. Next expected ACK = 879.

---

## Practical Wireshark Tips (related to content)
- Use **relative sequence numbers** view to simplify reading (Wireshark option).
- To locate a single TCP conversation: `tcp.stream eq N`.
- Display flags & options by expanding the TCP header in Wireshark packet details.
- View TCP options in SYN / SYN-ACK to see MSS and Window scale negotiation.

---

## Best Practices & Reminders
- MSS and Window Scaling are negotiated during the TCP handshake; they significantly affect throughput on high-bandwidth/long-latency links.
- Do not change MTU unless you understand the network implications (path MTU, fragmentation).
- Use Wireshark to validate handshake, MSS, window scaling, retransmissions, out-of-order segments, and ACK behavior.
- Always interpret sequence and acknowledgment numbers carefully (relative vs raw values).
