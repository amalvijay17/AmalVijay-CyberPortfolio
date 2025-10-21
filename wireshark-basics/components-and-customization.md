# Wireshark and Network Interface Overview

## Network Interfaces

Each system has two real or physical network cards:

- **Windows:** Wi-Fi and Ethernet  
- **Linux:** `wlan0` and `eth0` (or `ens33`)

### Selecting Network Interface

Select the network interface that is used to connect or access the network or internet.  
Spikes indicate that this interface is sending and receiving packets — meaning it’s active.

---

## Loopback Traffic

### What It Is
Loopback traffic is traffic from a machine to itself on the loopback interface. The loopback interface is a virtual network interface that allows communication between different processes running on the same device.

Loopback traffic does **not** capture external network traffic; it only monitors traffic within the system.

### Additional Information
Loopback traffic can involve communication between applications installed on a computer, but it is not limited to just application communication. It generally refers to any network traffic that a computer sends to itself, including communication between different network-related services or even testing of network protocols.

---

## Loopback Traffic vs Loopback Adapter

### 1. Loopback Traffic

**What it is:**  
This is the data that a computer sends to itself. It’s like the computer sending a message to its own address (127.0.0.1, also called "localhost") and receiving it back, without the data ever leaving the computer.

**Why it's useful:**  
It’s mainly used to test the computer’s networking functions.  
For example, if you type:
```bash
ping 127.0.0.1
```
Your computer sends a message to itself to check if the network stack is working properly.

### 2. Loopback Adapter

**What it is:**  
A loopback adapter is a virtual or physical network interface on the computer that makes loopback traffic possible. It creates a special network connection inside the computer, allowing the computer to communicate with itself through network protocols.

**Why it's useful:**  
It helps with testing and development, especially when no external network connection is needed.  
The loopback adapter makes the computer think it is connected to a network, even though it's only communicating with itself.

**In short:**  
Loopback traffic is the actual communication, and the loopback adapter is the tool that makes it possible.

---

## Example of Loopback Traffic

**Testing with Ping**

1. Open a terminal or command prompt.  
2. Type:
   ```bash
   ping 127.0.0.1
   ```
3. **What happens:**
   - The ping command sends an ICMP Echo Request packet to the IP address 127.0.0.1.
   - The packet doesn't go out to a physical network. Instead, it is handled by the loopback interface.
   - The system receives its own packet and replies with an ICMP Echo Reply.
   - The result confirms that the network stack and loopback interface are functioning correctly.

---

## Wireshark Display Components

**View → Components appearing in the display area:**

- **Main Toolbar:** Tools like start, stop capturing packets.
- **Filter Toolbar:** For applying or writing filters.
- **Status Bar:** Summary of captured/lost packets and profile info at the bottom.
- **Profile (Bottom right):** Displays all profiles.
- **Capture File Properties (Bottom left):** Shows file details.

### Packet Panels
- **Packet List:** Displays captured packets.
- **Packet Details:** Shows all encapsulated protocols in a selected packet.
- **Packet Bytes:** Displays raw contents (binary/hex). If unencrypted, data appears as plain text.

> Wireshark displays packet details in hexadecimal form.  
> Right-click on blank space → “Show bytes as bits.”  
> To revert → “Show bytes as hexadecimal.”

---

## Customizing Wireshark Layout

- `Edit → Preferences → Layout`
  - **Packet Diagram:** Displays encapsulated protocols in diagram form.
  - Right-click → Show Field Values
  - **Show Packet Separator:** `Edit → Preferences → Layout`
  - **Restore Defaults:** To revert layout.
  - To remove Packet Bytes pane: `View → Packet Bytes`
  - Double-click a packet to open in a separate window.

---

## Saving Capture Files

- **File → Save As…** or **Save this capture file** (Main toolbar)
- **Default extension:** `.pcapng`
- **Legacy extension:** `.pcap`
- To convert: `File → Save As`
- To save space: enable **Compress with gzip**
- Double-click compressed file to open.

---

## Main Toolbar / Menu Bar Functions

| Action | Description |
|--------|-------------|
| Start capturing packets | Begin live capture |
| Stop capturing packets | Stop current capture |
| Restart current capture | Terminates current session and starts new |
| Capture options | Configure interfaces (unavailable during capture) |
| Open / Save / Close / Reload file | Manage capture sessions |
| Find a packet | Search via hex, filter, string, regex |
| Navigate packets | Go to next, previous, first, last, or specific packet |
| Auto-scroll | Automatically scroll during live capture |
| Colour rules | View → Colouring Rules (change background/foreground) |
| Text size | Enlarge, shrink, or reset text size |
| Resize columns | Fit contents |

---

## Packet Columns

| Column | Description |
|--------|-------------|
| **No.** | Packet number |
| **Time** | Timestamp (default: microseconds) |
| **Source** | Sender IP address |
| **Destination** | Receiver IP address |
| **Protocol** | Protocol type |
| **Length** | Packet length (Wireshark excludes FCS = +4 bytes) |
| **Info** | Brief summary of packet content |

### Customizing Columns

- Right-click → Enable/Disable Columns
- Right-click → Column Preferences → Add (`+`) or Remove (`–`)
- Example: Add MAC Addresses
  - Title: `SRC_MAC` → Type: `Hardware src_address`
  - Title: `DEST_MAC` → Type: `Hardware dest_address`
- Or directly from a packet:
  - Expand TCP field → Right-click on **Destination Port** → **Apply as Column**

---

## Capture Options

`Capture → Options`

From here you can start packet capture by selecting the network interface and clicking OK.

### Promiscuous Mode

Promiscuous mode is available in the capture options window. It works on one condition — only if devices are connected via a **hub**.

- A hub broadcasts all incoming traffic to all ports, allowing Wireshark to see all packets.
- In **switched networks**, promiscuous mode alone is not enough — you need **port mirroring (SPAN)**.

**Example:**

| Port | Device |
|------|---------|
| 1 | Printer |
| 2 | Computer A |
| 3 | Wireshark Laptop |

Mirror:
- **Source Ports:** Port 1 (Printer), Port 2 (Computer A)  
- **Destination Port:** Port 3 (Wireshark laptop)

This way, your laptop receives a copy of all traffic between A and the printer.

---

### Monitor Mode (RFMON)

- Used in **wireless networks** only.  
- Captures all 802.11 frames: data, management (beacons, probe requests/responses, auth/assoc), and control frames (RTS/CTS/ACK).
- Works without connecting to any network.
- Windows **does not support** monitor mode (security reasons).
- Not all Wi-Fi cards support it.

---

### Promiscuous vs Monitor Mode

| Mode | Network Type | Function |
|------|---------------|-----------|
| **Promiscuous Mode** | Wired/Wireless | Captures all traffic visible to NIC (requires hub or port mirroring) |
| **Monitor Mode** | Wireless | Captures all 802.11 frames without association |

---

## Quick Preferences and Options

| Setting | Location | Purpose |
|----------|-----------|----------|
| Time format | View → Time Display Format | Set to seconds or time of day |
| Add Delta time | Edit → Preferences → Appearance → Columns (+) Delta time displayed |
| Add columns | Right-click → Apply as column |
| Colouring rules | View → Colouring Rules |
| Resize columns | Right-click → Resize to fit contents |

---

## Summary

Wireshark allows deep analysis of network traffic through active and virtual interfaces.  
Understanding:
- **Loopback traffic** (self-communication)
- **Promiscuous mode** (captures all visible traffic)
- **Monitor mode** (captures all wireless frames)  
is essential for effective **network troubleshooting** and **ethical hacking**.

---

**File Extensions:**
- `.pcapng` (default)
- `.pcap` (legacy)
- `.gz` (compressed)

**Test Local Network Stack:**
```bash
ping 127.0.0.1
```

---
