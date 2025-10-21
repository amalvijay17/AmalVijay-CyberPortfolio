# Nmap — Evasion Techniques (IP Fragmentation, Spoofing, Decoys, IDS Evasion)

## IP Fragmentation

**What it does**

* The `-f` option instructs Nmap to fragment IP packets into tiny pieces (typically 8 bytes each) so that protocol headers (e.g., TCP header) are split across multiple IP datagrams.
* Rationale: fragmented packets can bypass simplistic packet filters or old IDS/firewalls that do not reassemble fragments before inspection.

**How IP fragmentation works**

* IP can split large datagrams into fragments; each fragment shares the same IP Identification field.
* At the destination the kernel/stack reassembles fragments back into the original datagram.
* If a firewall/IDS does not reassemble, it may not see the full TCP header and therefore fail to detect a scan.

**Example command**

```bash
nmap -sS -f scanme.nmap.org -p 80
```

**Observed behaviour (notes/Wireshark)**

* Wireshark shows IP fragments with `More fragments` flag set and increasing `Fragment Offset` values (0, 8, 16...).
* The first fragment contains the first 8 bytes of payload, the second fragment the next 8 bytes, and so on.
* If the endpoint or inline device refuses to reassemble or drops fragments, you may see no TCP responses (e.g., no SYN-ACK/RST) while ICMP (echo) still works.

**Verdict**

* IP fragmentation is an "old-school" evasion technique. Modern stateful firewalls and IDS commonly reassemble fragments or apply other countermeasures.

---

## Spoofing IP Addresses

**What it does**

* Changing the source IP address of packets you send so they appear to originate from another host.

**Tools**

* Ettercap, Bettercap, and `nmap` (with `-S`) can be used to craft spoofed-source packets.

**Example command**

```bash
# send SYN scans with a spoofed source IP using a specific interface
nmap -sS -p 80 -S <spoofed-ip> -e <adapter> <target>
```

**Important caveats**

* The target will reply to the *spoofed* IP address, not your real host. Unless you control the spoofed IP's path (e.g., you are on its network or have an analyzer there), replies will not reach you.
* ARP and local-layer addressing: if you spoof an address that's not present on your subnet, local ARP responses won't be generated for that spoofed IP.
* If you have access to the spoofed host’s network or run a man-in-the-middle/analyzer on that host, you may be able to observe replies (e.g., using a decoy/relay).

**Practical note**

* IP spoofing can be useful for certain tests but is limited in practice because responses rarely route back to the attacker.

---

## Using Decoys to Evade Detection

**What it does**

* Decoy (cloaked) scans hide the attacker’s real IP among multiple fake sources so the target cannot easily determine which host is the true scanner.
* Nmap sends probes from several source IPs (some fake, some real) so the target sees many distinct scanners.

**Usage & examples**

```bash
# explicit list of decoys (include your real IP in the list)
nmap -sS -p 80 -D <fake1>,<fake2>,<your-ip> -e Wi-Fi scanme.nmap.org

# 20 random decoys
nmap -sS -p 80 -D RND:20 -e Wi-Fi scanme.nmap.org
```

**Notes**

* Decoys can be used with multiple scan types (SYN, ACK, UDP, etc.), not only SYN scans.
* If one of the decoy IPs belongs to an active host that notices the traffic, it may introduce additional noise or undesired consequences.

---

## Try to Avoid IDS Systems Altogether — Stealth Tips

**General guidance**

* Modern IDS/Firewall systems are typically capable of detecting IP fragments, spoofing patterns, and decoy scans; evasion success depends on the target environment and countermeasures in place.
* Traditional “stealth” techniques (e.g., adjusting TCP window size `--window`, fragmentation, timing, and low & slow scans) may still be useful for penetration tests but carry lower chance of bypassing well-defended networks.

**Example: Window trick**

* `--window 1024` (or other values) is an old trick to attempt evasion by using non-standard TCP window sizes.

---
