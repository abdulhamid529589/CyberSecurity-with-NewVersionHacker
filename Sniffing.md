# Sniffing, MAC Flooding, DHCP Attack, ARP Poisoning & MITM — Complete Notes

## Passive vs Active Sniffing, CAM Table Attacks, DHCP Starvation, ARP Spoofing, and Man-in-the-Middle Attacks

> **Course:** NewVersionHacker
> **Topic:** Sniffing — Network-Layer Attack Techniques
> **Disclaimer:** All content is strictly for **educational purposes only**. Never perform these attacks against systems or networks you do not own or have explicit written authorization to test. These are criminal offenses in most jurisdictions.

---

## Table of Contents

1. [What Is Sniffing?](#1-what-is-sniffing)
2. [Passive Sniffing — Hubs and How They Enable It](#2-passive-sniffing--hubs-and-how-they-enable-it)
3. [Active Sniffing — Switches and Why They Require Extra Work](#3-active-sniffing--switches-and-why-they-require-extra-work)
4. [Hub vs Switch — Core Difference That Drives Everything](#4-hub-vs-switch--core-difference-that-drives-everything)
5. [Active Sniffing Attacks — Overview](#5-active-sniffing-attacks--overview)
6. [The CAM Table — What It Is and Why It Matters](#6-the-cam-table--what-it-is-and-why-it-matters)
7. [Attack 1 — MAC Flooding (MAC Flood)](#7-attack-1--mac-flooding-mac-flood)
8. [Attack 2 — DHCP Starvation Attack](#8-attack-2--dhcp-starvation-attack)
9. [Attack 3 — ARP Poisoning / ARP Spoofing](#9-attack-3--arp-poisoning--arp-spoofing)
10. [Man-in-the-Middle (MITM) Attack — The Bigger Picture](#10-man-in-the-middle-mitm-attack--the-bigger-picture)
11. [Practical MITM with Cain and Abel — Windows Tool](#11-practical-mitm-with-cain-and-abel--windows-tool)
12. [Vulnerable Protocols — Why Some Traffic Is Easy to Sniff](#12-vulnerable-protocols--why-some-traffic-is-easy-to-sniff)
13. [OSI Layer Context — Where Sniffing Operates](#13-osi-layer-context--where-sniffing-operates)
14. [Sniffing Tools Reference](#14-sniffing-tools-reference)
15. [Defenses Against Sniffing Attacks](#15-defenses-against-sniffing-attacks)
16. [Why This Matters for Cybersecurity](#16-why-this-matters-for-cybersecurity)
17. [Quick Revision Table](#17-quick-revision-table)
18. [Self-Test Questions](#18-self-test-questions)

---

## 1. What Is Sniffing?

**Sniffing** is the process of capturing and monitoring network traffic — intercepting data packets as they travel across a network to read, analyze, or steal their contents.

- **Hindi equivalent:** "Sangana" — keeping an eye on something without necessarily interacting with it
- **Technical equivalent:** Packet capture, packet interception, network eavesdropping

### Simple analogy

Sniffing is like tapping a telephone line — you position yourself between two communicating parties and listen to everything being said without the parties knowing. In networking, this means intercepting packets traveling between devices.

### Two types of sniffing

| Type                 | How It Works                               | When Used                          |
| -------------------- | ------------------------------------------ | ---------------------------------- |
| **Passive Sniffing** | Just listen — no extra packets sent        | When the network uses **hubs**     |
| **Active Sniffing**  | Send crafted packets to manipulate traffic | When the network uses **switches** |

---

## 2. Passive Sniffing — Hubs and How They Enable It

### What is a Hub?

A **hub** is a simple network device that connects multiple devices. It uses a single shared communication channel — when any device sends data, the hub **broadcasts that data to ALL connected devices simultaneously**, regardless of who the intended recipient is.

### How passive sniffing works on a hub network

```
PC-A sends packet to PC-D:

Hub receives the packet → sends it to PC-B, PC-C, PC-D (everyone)

PC-B and PC-C receive the packet → see it's not for them → normally discard it
BUT if PC-B is sniffing → it keeps and reads the packet instead of discarding
```

**Why no extra packets are needed:** The hub already sends all traffic to all ports — an attacker connected to the hub automatically receives all traffic without doing anything special. They just need to put their network interface into **promiscuous mode** (accepting all packets, not just those addressed to them) using a tool like Wireshark.

**The key:** Passive sniffing requires zero interaction with the target — no packets, no tools attacking the network. Just connect and listen.

### Why hubs are no longer common

Hubs are legacy technology. Modern networks almost exclusively use **switches**, which are smarter and don't broadcast all traffic to all ports. This is what makes active sniffing necessary.

---

## 3. Active Sniffing — Switches and Why They Require Extra Work

### What is a Switch?

A **switch** is a smarter network device than a hub. It learns which device (MAC address) is connected to which port, and sends packets **only to the specific port where the destination device is connected** — not to everyone.

### How switches handle traffic

**First time (unknown destination MAC):**

```
PC-A → Switch: "Send this to PC-D" (switch doesn't know PC-D's port yet)
Switch → Broadcasts to all ports (multicast) to find PC-D
PC-D responds with its MAC address
Switch records: "PC-D is on Port 4" → stores in CAM table
```

**All subsequent times (known destination MAC):**

```
PC-A → Switch: "Send this to PC-D"
Switch → Sends ONLY to Port 4 (unicast — only PC-D receives it)
```

**Why this breaks passive sniffing:** Once the switch knows where everyone is (via its CAM table), it sends packets only to the intended recipient. PC-B can no longer "accidentally" receive traffic meant for PC-D. Passive sniffing no longer works.

**Solution:** Active sniffing — use attacks to manipulate the switch into behaving like a hub again, or to redirect traffic through the attacker's machine.

---

## 4. Hub vs Switch — Core Difference That Drives Everything

| Aspect                   | Hub                               | Switch                                   |
| ------------------------ | --------------------------------- | ---------------------------------------- |
| **Intelligence**         | None — just repeats to all        | Smart — learns MAC-to-port mappings      |
| **Traffic delivery**     | Always broadcast/multicast        | Unicast once MAC is known                |
| **CAM table**            | No                                | Yes — stores MAC-to-port mappings        |
| **Sniffing requirement** | Passive — just connect and listen | Active — must attack to redirect traffic |
| **Modern usage**         | Obsolete — rarely seen            | Standard in all modern networks          |

---

## 5. Active Sniffing Attacks — Overview

Because switches filter traffic intelligently, attackers must use additional techniques to intercept traffic on switched networks. All of these fall under **active sniffing**:

| Attack                       | What It Does                                                           |
| ---------------------------- | ---------------------------------------------------------------------- |
| **MAC Flooding**             | Overflow the CAM table → switch behaves like a hub → broadcasts to all |
| **DHCP Starvation**          | Exhaust all available IP addresses → prevent new users from connecting |
| **ARP Poisoning / Spoofing** | Send fake ARP replies → redirect traffic through attacker              |
| **DNS Poisoning**            | Corrupt DNS cache → redirect domain names to attacker's IP             |
| **Switch Port Stealing**     | Flood switch with attacker's MAC on target's port → steal the port     |
| **Spoofing Attack**          | Impersonate another device using its MAC/IP                            |

The first three (MAC Flooding, DHCP Starvation, ARP Poisoning) are demonstrated practically in this lecture.

---

## 6. The CAM Table — What It Is and Why It Matters

**CAM = Content Addressable Memory**

The CAM table (also called the MAC address table) is the switch's internal database that maps:

```
MAC Address ↔ Switch Port Number
```

**Example CAM table:**

| MAC Address       | Port   |
| ----------------- | ------ |
| AA:BB:CC:DD:EE:01 | Port 1 |
| AA:BB:CC:DD:EE:02 | Port 2 |
| AA:BB:CC:DD:EE:03 | Port 3 |
| AA:BB:CC:DD:EE:04 | Port 4 |

The switch uses this table to make intelligent unicast forwarding decisions. When the table is full or corrupted, the switch can no longer forward intelligently and falls back to broadcasting — which is exactly what attackers exploit.

---

## 7. Attack 1 — MAC Flooding (MAC Flood)

### What It Is

MAC Flooding is an attack that **floods the switch's CAM table with fake MAC addresses** until the table is completely full. Once full, the switch cannot learn any new MAC addresses and loses its ability to make unicast forwarding decisions — so it reverts to broadcasting all traffic to all ports, just like a hub.

### Why This Enables Sniffing

```
Before MAC Flooding:
PC-A → Switch → Port 4 only (unicast — only PC-D sees it)

After MAC Flooding (CAM table full):
PC-A → Switch → All ports (broadcast — attacker's PC also sees it)
```

The attacker, connected to the switch, now receives ALL traffic — equivalent to being on a hub network. They can then sniff all communications.

### Tool: macof

**`macof`** is the standard tool for MAC flooding in Kali Linux and Parrot OS.

### Checking Your Network Interface

Before using macof, identify your network adapter:

```bash
ifconfig
```

The interface shown (e.g., `eth0`, `ens33`, `ta0`) is what you pass to macof.

### macof Command

```bash
sudo macof -i ta0 -n 10
```

| Part     | Meaning                                                      |
| -------- | ------------------------------------------------------------ |
| `macof`  | Tool name (MAC address overflow)                             |
| `-i ta0` | Use network interface `ta0` (use your actual interface name) |
| `-n 10`  | Send exactly 10 MAC flood packets                            |

> Remove `-n 10` to flood continuously until stopped with `Ctrl+C`.

### What Happens in Wireshark

After running macof, observe in Wireshark:

- A large number of packets appear from **completely random source IP and MAC addresses**
- None of these IP addresses exist on your actual network (confirmed by running NetDiscover)
- Each packet has a **different source MAC address** — these are the fake MACs filling the CAM table
- At the Ethernet layer (Layer 2 / Data Link), the source and destination MAC addresses change with every packet

### Verifying the Fake Addresses

```bash
netdiscover -i ta0 -r 10.10.10.0/24
```

The IP addresses used in the flood packets will NOT appear in NetDiscover results — confirming they are fabricated and don't correspond to real devices.

### Clicking Into a Packet in Wireshark

**Ethernet II → Source / Destination MAC addresses:**

- Each packet will show a different source MAC
- IP addresses will also be random/non-existent

This proves the CAM table is being flooded with fake entries.

---

## 8. Attack 2 — DHCP Starvation Attack

### What Is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** is the protocol that automatically assigns IP addresses to devices when they join a network. The router/DHCP server has a limited pool of available IP addresses (e.g., 192.168.1.2 through 192.168.1.254 = 253 addresses).

> **Note on transcript error:** The transcript repeatedly says "DSCP" when referring to this attack. The correct term is **DHCP (Dynamic Host Configuration Protocol)**. DSCP (Differentiated Services Code Point) is an unrelated QoS field in IP headers.

### What DHCP Starvation Does

The attacker sends **massive numbers of fake DHCP DISCOVER requests**, each using a different spoofed MAC address. The DHCP server responds to each request by assigning an IP address to that (fake) MAC. Once all available IP addresses in the pool are exhausted:

```
DHCP Server: "Pool is empty — I have no IPs left to assign"

New real device connects → Requests an IP → Server: "Sorry, no IPs available"
Real user cannot connect to the network
```

This is a **Denial of Service (DoS) attack** specifically against the network's ability to onboard new users.

### Tool: Yersinia

**`yersinia`** is the standard tool for DHCP starvation attacks (and several other Layer 2 protocol attacks).

### Opening Yersinia

```bash
sudo yersinia -I
```

| Part       | Meaning                                     |
| ---------- | ------------------------------------------- |
| `yersinia` | Tool name                                   |
| `-I`       | Interactive mode (opens a visual interface) |

After opening, press any key to dismiss the initial notification. Press `H` for help to see all available commands and attacks.

### Entering DHCP Attack Mode

Inside yersinia's interactive interface:

```
Press: F2
```

This switches to **DHCP mode** — you'll see DHCP-specific fields (SIP, DIP, etc.) appear.

### Launching the Attack

Inside DHCP mode:

```
Press: X  (to see available attacks)
Select: 1  (Sending Discover Packets — the standard DHCP starvation attack)
```

This begins flooding the network with DHCP DISCOVER requests using fake MAC addresses. Each request asks the DHCP server to assign an IP, gradually exhausting the pool.

### Stopping the Attack

```
Press: Q  (to stop)
```

### What Wireshark Shows

After launching the attack, filter in Wireshark:

```
dhcp
```

or

```
bootp
```

You'll see a massive stream of DHCP DISCOVER packets, each with a different source MAC address — confirming the MAC address flooding component of the attack.

**At the Ethernet layer:** Same as MAC flooding — each packet has a different source MAC address, flooding the CAM table as a side effect.

---

## 9. Attack 3 — ARP Poisoning / ARP Spoofing

### What Is ARP?

**ARP (Address Resolution Protocol)** is the protocol that maps **IP addresses to MAC addresses** within a local network. When Device A wants to communicate with Device B:

1. Device A knows Device B's IP address (from DNS or configuration)
2. Device A doesn't know Device B's MAC address (needed for Layer 2 communication)
3. Device A broadcasts an ARP request: _"Who has IP 10.10.10.11? Tell 10.10.10.5"_
4. Device B responds: _"I have 10.10.10.11 — my MAC is AA:BB:CC:DD:EE:04"_
5. Device A stores this IP→MAC mapping in its local **ARP cache**
6. All future communication with 10.10.10.11 goes directly to AA:BB:CC:DD:EE:04

### The ARP Vulnerability

**ARP has no authentication.** Any device can send an ARP reply claiming to be any IP address — and the recipient will accept it, updating their ARP cache without verification.

### What ARP Poisoning Does

The attacker sends **forged ARP replies** to both the victim and the router:

```
To the victim:   "I am the router (10.10.10.1) — my MAC is [attacker's MAC]"
To the router:   "I am the victim (10.10.10.11) — my MAC is [attacker's MAC]"
```

Result:

- Victim sends traffic meant for the router → goes to attacker instead
- Router sends replies meant for victim → goes to attacker instead
- **All traffic flows through the attacker's machine**
- Attacker can read, modify, or drop any packet

This is the classic **Man-in-the-Middle (MITM) position**.

```
Before ARP Poisoning:
Victim ←→ Router

After ARP Poisoning:
Victim ←→ Attacker ←→ Router
(attacker sees and can manipulate all traffic)
```

### Tool: arpspoof

**`arpspoof`** (part of dsniff package) performs ARP poisoning from the command line.

### Command

```bash
sudo arpspoof -i ta0 -t 10.10.10.1 10.10.10.11
```

| Part            | Meaning                                                         |
| --------------- | --------------------------------------------------------------- |
| `arpspoof`      | Tool name                                                       |
| `-i ta0`        | Network interface to use                                        |
| `-t 10.10.10.1` | **Target** — the device to send fake ARP replies to (router)    |
| `10.10.10.11`   | The IP address to **impersonate** (victim's Windows 11 machine) |

**This tells the router:** "I am 10.10.10.11 — send all traffic for it to me."

### Reversing the Attack (Poison in Both Directions)

To complete the MITM position, run a second arpspoof in reverse — poisoning the victim to think the attacker is the router:

```bash
sudo arpspoof -i ta0 -t 10.10.10.11 10.10.10.1
```

Now both the router AND the victim think they should send traffic to the attacker.

### What Wireshark Shows

Filter in Wireshark:

```
arp
```

You'll see the spoofed ARP packets being sent. Key indicators:

1. **Duplicate IP detection warning:** Wireshark shows "_Duplicate use of 10.10.10.1 detected!_" — because two devices (the real router and the attacker) are now claiming the same IP address with different MAC addresses
2. **Multiple ARP replies for the same IP** — attacker's MAC appearing in replies that should be from the router or victim
3. **ARP reply source MAC doesn't match expected device** — the attacker's MAC appears where the router's or victim's MAC should be

### Verifying the Attack Succeeded

Once ARP poisoning is active and IP forwarding is enabled on the attacker machine:

- Open any browser on the victim machine and load a website
- In Wireshark on the attacker machine, filter by the victim's IP — all their HTTP/HTTPS traffic will now be visible flowing through the attacker

---

## 10. Man-in-the-Middle (MITM) Attack — The Bigger Picture

MITM is the **strategic goal** that ARP poisoning (and other sniffing attacks) are used to achieve. It is not a single tool or technique — it is a **position** in the network.

### What MITM Means

```
Normal communication:
[Victim] ←————————————————————→ [Server/Router]

MITM attack:
[Victim] ←→ [Attacker] ←→ [Server/Router]
              (reads/modifies all traffic)
```

The attacker sits in the middle, intercepting and potentially modifying all communication between the victim and the rest of the network.

### What an Attacker Can Do in MITM Position

| Action                       | Description                                                                |
| ---------------------------- | -------------------------------------------------------------------------- |
| **Passive eavesdropping**    | Read all traffic — credentials, messages, browsing history                 |
| **Session hijacking**        | Steal authenticated session cookies to take over logged-in sessions        |
| **SSL stripping**            | Downgrade HTTPS connections to HTTP, making encrypted traffic readable     |
| **Credential harvesting**    | Capture usernames and passwords from unencrypted protocols                 |
| **Inject malicious content** | Insert malicious scripts or content into HTTP responses                    |
| **DNS spoofing**             | Redirect domain lookups to fake IP addresses (phishing)                    |
| **Packet modification**      | Alter data in transit (change bank transfer amounts, alter file downloads) |

### The FTP Demonstration from the Lecture

The lecturer demonstrates MITM capability by:

1. Establishing ARP poisoning between two machines
2. Connecting to an FTP server from the victim machine
3. Logging in with a username and password
4. Capturing those credentials in Cain and Abel on the attacker machine

**Why FTP:** FTP sends usernames and passwords in **plain text** (unencrypted) — easily readable in any packet capture. This demonstrates exactly why unencrypted protocols are dangerous.

---

## 11. Practical MITM with Cain and Abel — Windows Tool

**Cain and Abel** is a Windows-based network security tool that includes ARP poisoning, password recovery, and packet sniffing capabilities. It provides a GUI interface for MITM attacks.

### Setup Steps

1. **Open Cain and Abel** on the Windows attacker machine
2. **Configure → select the network adapter**
   - Go to Configure
   - Select the correct network adapter (verify the IP address matches your machine's IP)
   - Click OK
3. **Start the sniffer**
   - Click "Stop and Start Sniffer" option
   - Accept the warning that appears
4. **Scan for hosts**
   - Go to the Sniffer tab
   - Click the **+** (add) button
   - Check "All Tests" and uncheck, then recheck to run all host discovery tests
   - Cain scans the local network → finds all active IPs and MACs
   - Results appear showing all discovered IP↔MAC pairs
5. **Set up ARP poisoning**
   - Click on "ARP" tab
   - Click the **+** button to add targets
   - Select target IPs from the discovered list (e.g., 10.10.10.13, 10.10.10.14, etc.)
   - Select which devices to poison (the router IP and the victim IP)
   - Click OK to add
6. **Start poisoning**
   - The ARP table status shows as "IDLE"
   - Click the **yellow Start** button
   - Status changes from IDLE → POISONING
   - ARP poisoning is now active

### Capturing Credentials (FTP Example)

Once MITM is active:

1. On the **victim machine**, open CMD and connect via FTP:
   ```cmd
   ftp 10.10.10.11
   ```
2. Enter a username and password when prompted
3. **Back on the attacker machine** in Cain and Abel, the credentials captured in transit will appear in the Passwords section

This demonstrates that **any unencrypted protocol** (FTP, HTTP, Telnet, POP3, SMTP) transmits credentials in plaintext that a MITM attacker can trivially capture.

---

## 12. Vulnerable Protocols — Why Some Traffic Is Easy to Sniff

Not all network traffic is equally vulnerable to sniffing. The key factor is **whether data is encrypted**.

### Protocols That Send Data in Cleartext (Vulnerable to Sniffing)

| Protocol         | Port | What's Exposed                                         | Secure Alternative       |
| ---------------- | ---- | ------------------------------------------------------ | ------------------------ |
| **HTTP**         | 80   | Web traffic, form data, cookies — all in plaintext     | HTTPS (port 443)         |
| **FTP**          | 21   | Usernames, passwords, and file contents                | SFTP (SSH-based) or FTPS |
| **Telnet**       | 23   | Remote terminal commands AND credentials in plaintext  | SSH (port 22)            |
| **SMTP**         | 25   | Email content and sometimes credentials                | SMTPS / STARTTLS         |
| **POP3**         | 110  | Email retrieval, credentials in plaintext              | POP3S (port 995)         |
| **IMAP**         | 143  | Email access and management in plaintext               | IMAPS (port 993)         |
| **SNMP** (v1/v2) | 161  | Community strings (effectively passwords) in plaintext | SNMPv3                   |
| **r-login**      | 513  | Remote login credentials in plaintext                  | SSH                      |

### Why These Are Called "Cleartext" Protocols

These protocols were designed in the early internet era when security wasn't a primary concern. They transmit data as readable ASCII text — if you capture the packet, you can read the username, password, and data directly without any decryption.

### The Secure Counterparts

All modern versions add encryption:

- **HTTP → HTTPS** (TLS/SSL encryption)
- **FTP → SFTP / FTPS** (SSH or TLS encryption)
- **Telnet → SSH** (encrypted remote access)
- **SMTP/POP3/IMAP → SMTPS/POP3S/IMAPS** (TLS encryption)

---

## 13. OSI Layer Context — Where Sniffing Operates

Sniffing attacks operate at specific layers of the OSI model:

```
Layer 7 — Application    ← Protocol-based sniffing (HTTP, FTP, Telnet vulnerabilities)
Layer 6 — Presentation
Layer 5 — Session
Layer 4 — Transport
Layer 3 — Network        ← ARP operates here (IP addresses)
Layer 2 — Data Link      ← MAC flooding, ARP spoofing, DHCP starvation (MAC addresses)
Layer 1 — Physical
```

**Sniffing primarily operates at Layer 2 (Data Link):**

- MAC addresses are Layer 2 identifiers
- ARP maps between Layer 3 (IP) and Layer 2 (MAC)
- CAM table attacks target Layer 2 switching behavior
- DHCP operates at Layer 2/3 boundary

**Protocol-based sniffing operates at Layer 7 (Application):**

- Reading cleartext HTTP, FTP, Telnet packets happens at the application layer
- Once traffic is redirected via Layer 2 attacks, Layer 7 content becomes accessible

---

## 14. Sniffing Tools Reference

| Tool              | Platform       | Primary Use                                                          |
| ----------------- | -------------- | -------------------------------------------------------------------- |
| **Wireshark**     | Cross-platform | Passive packet capture and analysis — used alongside all other tools |
| **macof**         | Linux/Kali     | MAC flooding — included in dsniff package                            |
| **Yersinia**      | Linux/Kali     | Multi-protocol Layer 2 attacks including DHCP starvation             |
| **arpspoof**      | Linux/Kali     | ARP poisoning — included in dsniff package                           |
| **Ettercap**      | Cross-platform | Comprehensive MITM framework; ARP poisoning + sniffing + filtering   |
| **Cain and Abel** | Windows        | GUI-based ARP poisoning, password recovery, MITM                     |
| **Bettercap**     | Cross-platform | Modern replacement for Ettercap; powerful MITM framework             |
| **tcpdump**       | Linux          | Command-line packet capture                                          |

---

## 15. Defenses Against Sniffing Attacks

Understanding attacks enables building better defenses:

| Attack                      | Defense                                                                                                                                                                   |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **MAC Flooding**            | Enable **Port Security** on managed switches — limit max MAC addresses per port; if exceeded, shut down the port                                                          |
| **DHCP Starvation**         | Enable **DHCP Snooping** on switches — only allows DHCP responses from trusted ports; rate-limits DHCP requests                                                           |
| **ARP Poisoning**           | Enable **Dynamic ARP Inspection (DAI)** — validates ARP packets against a trusted DHCP snooping table; rejects forged ARP replies                                         |
| **Cleartext protocols**     | **Use encrypted alternatives exclusively** — HTTPS, SSH, SFTP, SMTPS, etc.                                                                                                |
| **General sniffing**        | Use **VPNs** to encrypt all traffic; use **network segmentation** to limit blast radius; enable **802.1X port authentication** to prevent unauthorized device connections |
| **Passive sniffing (hubs)** | **Replace all hubs with managed switches** — virtually no legitimate reason to use hubs in 2024                                                                           |

---

## 16. Why This Matters for Cybersecurity

| Domain                       | Relevance                                                                                                                                    |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Penetration Testing**      | ARP poisoning and MITM are standard techniques in internal network pentests; tools like Bettercap and Ettercap are daily-use tools           |
| **SOC / Blue Team**          | Network sensors and IDS/IPS systems detect ARP anomalies (duplicate IPs, MAC changes) — SOC analysts investigate these alerts                |
| **Incident Response**        | When a breach is suspected, investigators look for evidence of ARP poisoning in network logs and ARP cache dumps                             |
| **Malware Analysis**         | Many malware strains include network sniffing capabilities — understanding how sniffing works helps analysts identify malicious behavior     |
| **Public WiFi Security**     | Public networks (cafes, airports) are prime hunting grounds for MITM attacks — all traffic on shared networks is vulnerable unless encrypted |
| **CEH / OSCP Certification** | Both certifications include hands-on MITM and sniffing scenarios; these tools and concepts are tested directly                               |

---

## 17. Quick Revision Table

| Concept                          | One-line Summary                                                                     |
| -------------------------------- | ------------------------------------------------------------------------------------ |
| **Sniffing**                     | Intercepting and reading network traffic not intended for the attacker               |
| **Passive Sniffing**             | No extra packets needed — hubs broadcast all traffic to all ports                    |
| **Active Sniffing**              | Requires crafted packets — switches must be tricked into broadcasting                |
| **Hub**                          | Broadcasts all traffic to all ports — passive sniffing is trivial                    |
| **Switch**                       | Sends traffic only to the correct port using MAC addresses — requires active attacks |
| **CAM Table**                    | Switch's internal table mapping MAC addresses to ports                               |
| **MAC Flooding**                 | Overflow CAM table with fake MACs → switch behaves like hub → broadcast              |
| **macof**                        | Tool for MAC flooding attacks                                                        |
| **DHCP Starvation**              | Exhaust DHCP IP pool with fake requests → new users can't connect (DoS)              |
| **Yersinia**                     | Tool for DHCP starvation and other Layer 2 attacks                                   |
| **ARP**                          | Address Resolution Protocol — maps IP addresses to MAC addresses                     |
| **ARP Poisoning**                | Send fake ARP replies to redirect traffic through attacker                           |
| **arpspoof**                     | Tool for ARP poisoning                                                               |
| **MITM**                         | Man-in-the-Middle — attacker positioned between two communicating parties            |
| **Cain and Abel**                | Windows GUI tool for ARP poisoning and credential capture                            |
| **Cleartext protocols**          | FTP, HTTP, Telnet, POP3, SMTP — transmit data including passwords unencrypted        |
| **Port Security**                | Switch feature that limits MAC addresses per port — defends against MAC flooding     |
| **DHCP Snooping**                | Switch feature that validates DHCP traffic — defends against DHCP starvation         |
| **Dynamic ARP Inspection (DAI)** | Switch feature that validates ARP packets — defends against ARP poisoning            |

---

## 18. Self-Test Questions

1. What is sniffing in networking? Give a real-world analogy.
2. What is the fundamental difference between passive and active sniffing, and what network hardware determines which applies?
3. Explain why a hub enables passive sniffing but a switch does not.
4. What is the CAM table, what does it store, and what happens when it is full?
5. Explain the MAC flooding attack step by step. What tool is used, and what is the command?
6. What is DHCP starvation? What service does it attack and what is the impact on network users?
7. Explain how ARP poisoning works — what false information is sent to which devices, and what MITM position does it create?
8. Write the arpspoof command to poison 10.10.10.1 (router) to think that 10.10.10.50 (victim) is located at the attacker's MAC.
9. Why does Wireshark show a "Duplicate IP detected" warning during an ARP poisoning attack?
10. List five cleartext protocols, their port numbers, and what sensitive information they expose.
11. Name three network switch features that defend against the attacks in this lecture, and explain what each one does.
12. What OSI layers do sniffing attacks primarily operate on, and why?

---

## What's Next

Sniffing establishes the attacker in a position to intercept traffic. Building on this:

- **DNS Poisoning** — poisoning DNS caches to redirect domain lookups to attacker-controlled IPs
- **SSL Stripping** — downgrading HTTPS connections to HTTP to bypass encryption
- **Session Hijacking** — stealing authenticated session tokens captured via MITM
- **Credential Harvesting** — capturing usernames and passwords from cleartext protocols
- **Ettercap / Bettercap** — comprehensive MITM frameworks with built-in filters for credential capture and injection
- **Wireless Sniffing** — applying similar techniques to WiFi networks (Evil Twin AP, Deauth attacks)

_Sniffing and MITM attacks form one of the most important pillars of internal network penetration testing. Any security professional — red team or blue team — must deeply understand how these attacks work, how to detect them, and how to prevent them._
