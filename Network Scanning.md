# Network Scanning — Complete Notes

## https://youtu.be/Hh_JdLJdsks

## What Network Scanning Is, Methodology, Scan Types, Nmap Commands, NetDiscover, and Practical Walkthroughs

> **Course:** NewVersionHacker — Ethical Hacking Series
> **Topic:** Network Scanning — Step 2 of the Ethical Hacking Methodology (follows Information Gathering)
> **Disclaimer:** All techniques described here are strictly for **educational purposes only**. Never perform network scanning against systems or networks you do not own or have explicit written authorization to test. Unauthorized scanning is illegal in most jurisdictions.

---

## Table of Contents

1. [What Is Network Scanning?](#1-what-is-network-scanning)
2. [Network Scanning Methodology — 8 Steps](#2-network-scanning-methodology--8-steps)
3. [The Simplified 5-Step Flow to Remember](#3-the-simplified-5-step-flow-to-remember)
4. [Types of Network Scans](#4-types-of-network-scans)
5. [Why SYN Scan Is Preferred — Deep Explanation](#5-why-syn-scan-is-preferred--deep-explanation)
6. [Tools Used for Network Scanning](#6-tools-used-for-network-scanning)
7. [Practical 1 — Discovering Active Devices with NetDiscover](#7-practical-1--discovering-active-devices-with-netdiscover)
8. [Practical 2 — Network Discovery with Nmap](#8-practical-2--network-discovery-with-nmap)
9. [Practical 3 — Port Scanning with Nmap](#9-practical-3--port-scanning-with-nmap)
10. [Practical 4 — Service and Version Detection](#10-practical-4--service-and-version-detection)
11. [Practical 5 — OS Detection](#11-practical-5--os-detection)
12. [Practical 6 — Fast Scanning with Timing Templates](#12-practical-6--fast-scanning-with-timing-templates)
13. [Reading Nmap Output — What Each Field Means](#13-reading-nmap-output--what-each-field-means)
14. [TTL Values and OS Fingerprinting](#14-ttl-values-and-os-fingerprinting)
15. [Complete Nmap Command Reference](#15-complete-nmap-command-reference)
16. [Network Scanning in Context — Where It Fits](#16-network-scanning-in-context--where-it-fits)
17. [Why This Matters for Cybersecurity](#17-why-this-matters-for-cybersecurity)
18. [Quick Revision Table](#18-quick-revision-table)
19. [Self-Test Questions](#19-self-test-questions)

---

## 1. What Is Network Scanning?

**Network scanning** is the process of actively probing a target network or system to systematically discover:

- Which devices are alive and reachable
- What IP addresses they hold
- Which ports are open on those devices
- What services are running on those ports
- What versions those services are running
- What operating system the device is using

It is **Step 2** of the ethical hacking methodology, immediately following Information Gathering (Step 1). Where information gathering is often passive (using external sources), network scanning is **active** — actual packets are sent to the target network to elicit responses.

### Simple analogy

Scanning is like surveying a building before a security assessment:

- Finding all the doors and windows (ports)
- Checking which ones are open or locked (open/closed ports)
- Identifying what is behind each door (services)
- Finding out the age and model of the locks (service versions with known vulnerabilities)

### What it means for hacking

- Without scanning: you know the address of the building (IP) but nothing else
- With scanning: you know every entry point, what's running behind each door, and which version has a known weakness

---

## 2. Network Scanning Methodology — 8 Steps

A structured scanning methodology ensures nothing is missed and results are actionable:

---

### Step 1 — Select the Target

Define what you are scanning:

- A single computer / server
- A company network
- A range of IP addresses

The target determines which scanning approach to use.

---

### Step 2 — Scan for IP Range

Determine whether the target is within your reachable network range.

**Networks you can scan:**

| Network Type                        | Scope                               | Reachable?                      |
| ----------------------------------- | ----------------------------------- | ------------------------------- |
| **LAN (Local Area Network)**        | Local — within your router's range  | Yes — directly reachable        |
| **WAN (Wide Area Network)**         | Internet-wide                       | Yes — if you have the public IP |
| **MAN (Metropolitan Area Network)** | Campus or city-wide private network | Only from inside the network    |

You can only scan systems that are reachable from your position — either on your local network or with a known public IP on the internet.

---

### Step 3 — Port Scanning

Once you have confirmed the target's IP address is active, scan all 65,535 ports to determine which are open, closed, or filtered.

- **Open port** → a service is actively listening and will accept connections
- **Closed port** → the port is reachable but no service is listening
- **Filtered port** → a firewall or filter is blocking the probe; no response

---

### Step 4 — Service Detection

For each open port found, determine **what service is running on it**.

Example:

- Port 80 → HTTP (web server)
- Port 22 → SSH (secure shell)
- Port 21 → FTP (file transfer)
- Port 443 → HTTPS (encrypted web)

Services are the actual programs listening on ports — they are the real attack surface.

---

### Step 5 — Version Grabbing (Service Version Detection)

For each identified service, extract the **exact version number**.

Why version matters:

- Software has a history of discovered vulnerabilities (CVEs)
- An older version may have a known, publicly available exploit
- Knowing the version tells you exactly which attacks will work

**Example:** If Apache HTTP Server version 2.4.49 is detected → this specific version has a publicly known path traversal vulnerability (CVE-2021-41773). The attacker knows exactly which exploit to use.

---

### Step 6 — OS Detection (Operating System Grabbing)

Identify which **operating system** the target is running.

Why OS matters:

- Every OS has OS-specific exploits and attack vectors
- Windows exploits do not work against Linux, and vice versa
- Knowing the OS allows selecting the correct attack tools and techniques

---

### Step 7 — Bypass IDS (Intrusion Detection System)

If the target has an IDS monitoring for scanning activity, apply techniques to evade detection — fragmented packets, timing adjustments, decoy scanning, etc.

---

### Step 8 — Select Correct Scan Type

Based on the target's security posture and the information needed, select the most appropriate scanning technique (SYN, TCP, UDP, FIN, etc. — see Section 4).

---

## 3. The Simplified 5-Step Flow to Remember

If 8 steps is too many, memorize this condensed version:

```
1. FIND the target IP → Is it online?
        ↓
2. DISCOVER open ports → Which ports are open?
        ↓
3. IDENTIFY services → What is running on each open port?
        ↓
4. GRAB versions → What version of each service is running?
        ↓
5. DETECT OS → What operating system is the target running?
```

All five lead to one outcome: **a complete picture of the target's attack surface**, enabling a precise, targeted attack rather than random guessing.

---

## 4. Types of Network Scans

Nmap supports multiple scanning techniques. Each has a specific use case:

| Scan Type            | Nmap Flag | Full Name          | Description                                                                               |
| -------------------- | --------- | ------------------ | ----------------------------------------------------------------------------------------- |
| **TCP Connect Scan** | `-sT`     | TCP Full Connect   | Completes the full 3-way handshake; easily detected; useful when SYN scan requires root   |
| **SYN Scan**         | `-sS`     | SYN / Stealth Scan | Sends only SYN; never completes handshake; harder to detect; default with root privileges |
| **UDP Scan**         | `-sU`     | UDP Scan           | Scans UDP ports; slower; important for DNS, SNMP, DHCP                                    |
| **ACK Scan**         | `-sA`     | ACK Scan           | Used for firewall rule mapping; determines if port is filtered vs. unfiltered             |
| **FIN Scan**         | `-sF`     | FIN Scan           | Sends FIN flag; evades some firewalls; works on BSD/Unix                                  |
| **NULL Scan**        | `-sN`     | NULL Scan          | Sends no flags; evades some firewalls                                                     |
| **Xmas Scan**        | `-sX`     | Christmas Scan     | Sends FIN, PSH, URG flags together (lights up like a Christmas tree)                      |
| **IDLE/IPID Scan**   | `-sI`     | IDLE Scan          | Uses a "zombie" host to hide scanner's identity; most stealthy                            |

> **Default scan:** When run with root/sudo privileges, Nmap defaults to **SYN Scan (`-sS`)**. This is why it is the most commonly encountered scan type in practice.

---

## 5. Why SYN Scan Is Preferred — Deep Explanation

Understanding _why_ SYN scan is preferred requires revisiting the TCP 3-way handshake from the protocol module.

### Normal TCP Connection (Full Handshake)

```
Client          Server
  │──── SYN ────▶│   Step 1: "I want to connect"
  │◀── SYN+ACK ──│   Step 2: "OK, connect with me too"
  │──── ACK ────▶│   Step 3: "Confirmed — connection established"
  │═══ DATA ════▶│   Data flows
```

When Nmap does a **TCP Connect Scan (`-sT`)**, it completes this entire 3-way handshake on every port. This means:

- 65,535 ports × 3 packets each = 196,605+ packets to the target
- This is **highly detectable** — firewalls and IDS will flag this as a port scan immediately and block the scanner's IP

### SYN Scan — The Half-Handshake

The SYN scan (also called **half-open scan** or **stealth scan**) exploits a clever observation:

> If a port is open, the server will respond with **SYN+ACK**. That response alone tells us the port is open. We don't need to complete the handshake.

```
Scanner         Server
  │──── SYN ────▶│   "Are you open?"
  │◀── SYN+ACK ──│   "Yes, I'm open!" ← This is all we needed
  │   (no ACK)   │   ← Scanner does NOT reply
  │               │   Connection never established
```

**Why this avoids firewall blocking:**

Most firewalls are configured to block sources that attempt to communicate with many ports simultaneously (a sign of a port scan). However, a SYN scan **never completes a TCP connection** — it only sends one SYN packet per port and waits for a response.

Because no full connection is ever established:

- Many older firewalls and logging systems don't record an incomplete connection
- The scanner is less likely to be blocked
- The scanner still learns which ports are open (SYN+ACK = open; RST = closed; no reply = filtered)

**Also called:**

- Half-open scan (only half the handshake happens)
- SYN stealth scan (harder to detect than full TCP)
- Half TCP scan

```
SYN → SYN+ACK (port is OPEN — we stop here)
SYN → RST (port is CLOSED — server immediately rejects)
SYN → (no reply) (port is FILTERED — firewall blocking)
```

---

## 6. Tools Used for Network Scanning

### Primary Tools

| Tool            | Type                    | Primary Use                                                                      |
| --------------- | ----------------------- | -------------------------------------------------------------------------------- |
| **Nmap**        | CLI — Free, open-source | Comprehensive port, service, OS, and version scanning; industry standard         |
| **NetDiscover** | CLI — Free              | Fast ARP-based LAN device discovery — finds active devices on your local network |

### Supporting Tools (Mentioned / Related)

| Tool          | Use                                                      |
| ------------- | -------------------------------------------------------- |
| **Wireshark** | Capture and analyze the packets generated by scans       |
| **Zenmap**    | Graphical front-end for Nmap (GUI version)               |
| **Masscan**   | Ultra-fast port scanner; useful for large network ranges |

---

## 7. Practical 1 — Discovering Active Devices with NetDiscover

### What NetDiscover Does

NetDiscover uses **ARP (Address Resolution Protocol)** to discover active devices on a local network. It sends ARP requests across the specified IP range and records which IPs respond — confirming they are online.

### Lab Environment (from the lecture)

| Machine  | OS                  | IP          |
| -------- | ------------------- | ----------- |
| Attacker | Parrot OS           | 10.10.10.x  |
| Target 1 | Windows 11          | 10.10.10.11 |
| Target 2 | Windows 10          | 10.10.10.10 |
| Server   | Windows Server 2019 | 10.10.10.29 |

### Step 1 — Gain Root Permissions

```bash
sudo su
# Enter your password when prompted
```

### Step 2 — Find Your Network Interface

```bash
ifconfig
```

Look for your active interface (e.g., `eth0`, `ens33`, `en3`) and note:

- Your IP address (e.g., `10.10.10.x`)
- Your network interface name (e.g., `en3`)

### Step 3 — Check NetDiscover Help

```bash
netdiscover -h
```

Key flags:

- `-i` → specify network interface
- `-r` → specify IP range to scan

### Step 4 — Run NetDiscover

```bash
netdiscover -i en3 -r 10.10.10.0/24
```

**Breaking down the command:**

| Part               | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| `netdiscover`      | Tool name                                                    |
| `-i en3`           | Use network interface `en3` (use your actual interface name) |
| `-r 10.10.10.0/24` | Scan the entire `10.10.10.0` network (all 254 usable IPs)    |
| `/24`              | Subnet mask = 255.255.255.0 — scan all IPs from .1 to .254   |

### What NetDiscover Output Shows

```
IP              At MAC Address     Count     Len  MAC Vendor / Hostname
-----------------------------------------------------------------------------
10.10.10.1      [MAC address]      1         60   [Router/Gateway]
10.10.10.10     [MAC address]      1         60   VMware (Windows 10)
10.10.10.11     [MAC address]      1         60   VMware (Windows 11)
10.10.10.29     [MAC address]      1         60   VMware (Server 2019)
```

**Reading the output:**

- **First IP (.1)** = Default gateway (router) — not a target
- **Subsequent IPs** = Active devices on the network
- **MAC address** = Hardware identifier for each device — can be cross-referenced to verify identity
- **MAC Vendor** = Sometimes identifies the device manufacturer (VMware for VMs, Intel for physical NICs, etc.)

### Verifying a Device's MAC Address

On the target Windows machine (CMD):

```cmd
ipconfig /all
```

Find the "Physical Address" — this should match what NetDiscover reported for that IP.

---

## 8. Practical 2 — Network Discovery with Nmap

Nmap can perform the same device discovery as NetDiscover, but with additional detail.

### Command

```bash
nmap 10.10.10.0/24
```

### What This Does vs. NetDiscover

| NetDiscover           | Nmap (basic)                           |
| --------------------- | -------------------------------------- |
| ARP-based — very fast | Uses multiple probes                   |
| Shows IP + MAC only   | Shows IP + MAC + open ports + OS hints |
| LAN only (ARP)        | Can scan WAN too                       |
| Very simple output    | More detailed output                   |

---

## 9. Practical 3 — Port Scanning with Nmap

### Command — Scan All Ports on a Specific Target

```bash
nmap -p- 10.10.10.11
```

| Flag          | Meaning                         |
| ------------- | ------------------------------- |
| `-p-`         | Scan ALL 65,535 ports (0–65535) |
| `10.10.10.11` | Target IP address               |

**Alternative — Scan Specific Port:**

```bash
nmap -p 21 10.10.10.11          # Only port 21
nmap -p 21,22,80,443 10.10.10.11  # Specific ports
nmap -p 1-1024 10.10.10.11      # Port range 1 to 1024
```

### What Port Scan Output Shows

```
PORT      STATE    SERVICE
21/tcp    open     ftp
22/tcp    open     ssh
80/tcp    open     http
135/tcp   open     msrpc
139/tcp   open     netbios-ssn
443/tcp   open     https
445/tcp   open     microsoft-ds
3389/tcp  open     ms-wbt-server
...
MAC Address: 00:0C:29:98:CA:B0 (VMware)
```

**Port states:**

| State            | Meaning                                        |
| ---------------- | ---------------------------------------------- |
| `open`           | Service is listening and accepting connections |
| `closed`         | Port reachable but no service listening        |
| `filtered`       | Firewall blocking — no response received       |
| `open\|filtered` | Cannot determine — likely filtered             |

---

## 10. Practical 4 — Service and Version Detection

After finding open ports, identify **what is running** on each port and **which version**.

### Command

```bash
nmap -p- -sV -v 10.10.10.11
```

| Flag  | Meaning                                                                  |
| ----- | ------------------------------------------------------------------------ |
| `-p-` | Scan all 65,535 ports                                                    |
| `-sV` | **Service Version detection** — identify service name AND version number |
| `-v`  | Verbose — show more detail in output                                     |
| `-vv` | Very verbose — even more detail                                          |

### What Service/Version Output Shows

```
PORT      STATE  SERVICE       VERSION
21/tcp    open   ftp           Microsoft ftpd
80/tcp    open   http          Microsoft IIS httpd 10.0
135/tcp   open   msrpc         Microsoft Windows RPC
139/tcp   open   netbios-ssn   Microsoft Windows netbios-ssn
443/tcp   open   https         Microsoft IIS httpd 10.0
445/tcp   open   microsoft-ds  Windows Server 2019 microsoft-ds
3389/tcp  open   ms-wbt-server Microsoft Terminal Services
```

**Why version matters:** If IIS 10.0 has a known CVE, you now have the exact version to look up exploits for. An attacker knowing the exact version can immediately look it up in vulnerability databases (NVD, Exploit-DB, etc.).

---

## 11. Practical 5 — OS Detection

### Command

```bash
nmap -sT -O -p- -sV -v 10.10.10.11
```

| Flag  | Meaning                                                        |
| ----- | -------------------------------------------------------------- |
| `-sT` | **TCP Connect Scan** (full handshake)                          |
| `-O`  | **OS Detection** — Nmap tries to identify the operating system |
| `-p-` | All ports                                                      |
| `-sV` | Service + version                                              |
| `-v`  | Verbose                                                        |

### Alternative — SYN Scan with OS Detection

```bash
nmap -sS -O -p- -sV -v 10.10.10.11
```

### What OS Detection Output Shows

```
OS details: Microsoft Windows 10 1903 - 21H1
OS CPE: cpe:/o:microsoft:windows_10
Network Distance: 1 hop

TCP/IP fingerprint:
  OS:SCAN(V=7.92...)
  ...
```

**Important nuance from the lecture:** Nmap sometimes detects Windows 11 as Windows 10 — because Windows 11 and Windows 10 share very similar TCP/IP stack fingerprints. This is normal and expected behavior.

---

## 12. Practical 6 — Fast Scanning with Timing Templates

By default, Nmap is careful and slow. Timing templates control scan speed:

### Timing Templates

| Template   | Flag  | Speed                        | Use Case                 |
| ---------- | ----- | ---------------------------- | ------------------------ |
| Paranoid   | `-T0` | Extremely slow (5min/packet) | Maximum IDS evasion      |
| Sneaky     | `-T1` | Very slow                    | IDS evasion              |
| Polite     | `-T2` | Slow                         | Reduce bandwidth usage   |
| Normal     | `-T3` | Default speed                | Standard scans           |
| Aggressive | `-T4` | Fast                         | Reliable networks        |
| Insane     | `-T5` | Very fast                    | Fast local networks only |

**Higher number = faster scan, less stealth, more network load**

### Full Fast Scan Command (from the lecture)

```bash
nmap -p- -sS -sV -vv -T5 10.10.10.11
```

| Flag  | Meaning                               |
| ----- | ------------------------------------- |
| `-p-` | All 65,535 ports                      |
| `-sS` | SYN scan (stealth, default)           |
| `-sV` | Service and version detection         |
| `-vv` | Very verbose — maximum detail output  |
| `-T5` | Insane timing — fastest possible scan |

### Result: What Changes with Timing

Without timing (default): scan may take 5–10+ minutes
With `-T5`: same scan completes in seconds

**Trade-off:** `-T5` is noisier and more detectable but dramatically faster. Use `-T4` for a balance of speed and reduced detection risk.

---

## 13. Reading Nmap Output — What Each Field Means

### Full Example Output

```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 10.10.10.11
Host is up (0.0010s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           Microsoft ftpd
80/tcp    open  http          Microsoft IIS httpd 10.0
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp   open  https         Microsoft IIS httpd 10.0
445/tcp   open  microsoft-ds  Windows Server 2019 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
MAC Address: 00:0C:29:98:CA:B0 (VMware)
OS details: Microsoft Windows 10 1903 - 21H1
Nmap done: 1 IP address (1 host up) scanned in 8.22 seconds
```

**Field-by-field breakdown:**

| Field                               | Meaning                                                     |
| ----------------------------------- | ----------------------------------------------------------- |
| `Nmap scan report for 10.10.10.11`  | Target IP being reported on                                 |
| `Host is up (0.0010s latency)`      | Device is online; 1ms response time                         |
| `Not shown: 65521 closed tcp ports` | 65,521 ports found closed — not listed to keep output clean |
| `PORT`                              | Port number and protocol (tcp/udp)                          |
| `STATE`                             | open / closed / filtered                                    |
| `SERVICE`                           | Name of the standard service on that port                   |
| `VERSION`                           | Actual service name and version detected                    |
| `MAC Address`                       | Hardware address + manufacturer (VMware = virtual machine)  |
| `OS details`                        | Detected operating system                                   |

---

## 14. TTL Values and OS Fingerprinting

**TTL (Time to Live)** is a field in every IP packet that decrements as the packet travels through network hops. It prevents packets from looping forever.

Different operating systems use different **default TTL values** — which allows rough OS identification even without dedicated OS scanning:

| Default TTL | Operating System                           |
| ----------- | ------------------------------------------ |
| **128**     | **Windows** (Windows 7, 8, 10, 11, Server) |
| **64**      | **Linux** / Unix / macOS                   |
| **255**     | Cisco network devices, some Unix variants  |
| **254**     | Solaris                                    |

**In Nmap output:** When `-O` is used, Nmap analyzes TCP/IP fingerprints (including TTL, window size, TCP options) to determine OS. The TTL is one of many signals it uses.

**Quick manual check:**

```bash
ping 10.10.10.11
```

If TTL in the ping response is close to 128 → likely Windows.
If close to 64 → likely Linux.

---

## 15. Complete Nmap Command Reference

### Device Discovery

```bash
# Discover live hosts in a network range
nmap 10.10.10.0/24

# Ping scan only (no port scan) — just find live hosts
nmap -sn 10.10.10.0/24
```

### Port Scanning

```bash
# Scan top 1000 ports (default)
nmap 10.10.10.11

# Scan ALL 65,535 ports
nmap -p- 10.10.10.11

# Scan specific port(s)
nmap -p 22 10.10.10.11
nmap -p 22,80,443 10.10.10.11
nmap -p 1-1024 10.10.10.11

# SYN scan (stealth, default with root)
nmap -sS 10.10.10.11

# TCP connect scan (full handshake, no root needed)
nmap -sT 10.10.10.11

# UDP scan
nmap -sU 10.10.10.11
```

### Service and Version Detection

```bash
# Detect services and versions
nmap -sV 10.10.10.11

# Full port + service + version
nmap -p- -sV 10.10.10.11
```

### OS Detection

```bash
# OS detection
nmap -O 10.10.10.11

# OS + service + version + verbose
nmap -O -sV -v 10.10.10.11
```

### Verbosity and Output

```bash
nmap -v 10.10.10.11     # Verbose
nmap -vv 10.10.10.11    # Very verbose
```

### Timing

```bash
nmap -T0 10.10.10.11    # Paranoid (slowest)
nmap -T1 10.10.10.11    # Sneaky
nmap -T2 10.10.10.11    # Polite
nmap -T3 10.10.10.11    # Normal (default)
nmap -T4 10.10.10.11    # Aggressive (recommended for most use)
nmap -T5 10.10.10.11    # Insane (fastest)
```

### Combined Comprehensive Scan (from lecture)

```bash
# Full scan: all ports, SYN scan, service+version, very verbose, fast
nmap -p- -sS -sV -vv -T5 10.10.10.11

# Full scan + OS detection
nmap -p- -sS -O -sV -vv -T5 10.10.10.11
```

### NetDiscover Commands

```bash
# Check help
netdiscover -h

# Find your network interface
ifconfig

# Scan a network range
netdiscover -i eth0 -r 192.168.1.0/24
netdiscover -i en3 -r 10.10.10.0/24
```

---

## 16. Network Scanning in Context — Where It Fits

```
Step 1: INFORMATION GATHERING (Footprinting & Recon)
        Find target's public info, IP range, technologies
             ↓
Step 2: NETWORK SCANNING ← WE ARE HERE
        Discover active hosts, open ports, services, versions, OS
             ↓
Step 3: ENUMERATION
        Deep-dive into specific services (SMB, FTP, HTTP, DNS enumeration)
             ↓
Step 4: VULNERABILITY ANALYSIS
        Match discovered services/versions to known CVEs
             ↓
Step 5: EXPLOITATION
        Attack using appropriate exploits for discovered vulnerabilities
             ↓
Step 6: POST-EXPLOITATION
        Maintain access, pivot to other systems
             ↓
Step 7: REPORTING
        Document everything discovered and exploited
```

Network scanning fills in the detailed picture that information gathering started — converting a known IP address into a complete profile of the target's exposed attack surface.

---

## 17. Why This Matters for Cybersecurity

| Application                   | How Network Scanning Applies                                                                      |
| ----------------------------- | ------------------------------------------------------------------------------------------------- |
| **Penetration Testing**       | Every authorized pentest begins with a scanning phase — Nmap is the first tool run                |
| **Vulnerability Management**  | Security teams scan their own networks regularly to find what's exposed                           |
| **SOC / Network Monitoring**  | Baseline scanning establishes what "normal" looks like; deviations indicate changes or intrusions |
| **Attack Surface Management** | Organizations use continuous scanning tools to track all internet-facing systems                  |
| **CTF (Capture The Flag)**    | Nmap is the first tool used in virtually every CTF challenge                                      |
| **Red Team Operations**       | Recon and scanning are the foundation of any red team engagement                                  |
| **Blue Team Defense**         | Knowing what attackers can discover about your network helps you reduce exposure                  |

### From a defense perspective

If an attacker can run `nmap -p- -sV -O` against your network and find:

- Port 445 open with Windows SMB → check for EternalBlue / MS17-010
- Port 3389 open with old Windows → check for BlueKeep / DejaBlue
- FTP on port 21 with an old version → check for known FTP exploits

The defensive response is: **close unnecessary ports, keep services updated, use firewalls to filter scanning, run IDS/IPS to detect scanning activity**.

---

## 18. Quick Revision Table

| Concept                | One-line Summary                                                                              |
| ---------------------- | --------------------------------------------------------------------------------------------- |
| **Network Scanning**   | Actively probing a target network to discover devices, open ports, services, versions, and OS |
| **NetDiscover**        | ARP-based tool for discovering active devices on a LAN                                        |
| **Nmap**               | Industry-standard port scanner; detects ports, services, versions, and OS                     |
| **`-sS`**              | SYN (stealth) scan — sends only SYN, never completes handshake; default with root             |
| **`-sT`**              | TCP connect scan — full 3-way handshake; works without root; easily detected                  |
| **`-sU`**              | UDP scan — scans UDP ports                                                                    |
| **`-p-`**              | Scan all 65,535 ports                                                                         |
| **`-sV`**              | Service and version detection                                                                 |
| **`-O`**               | OS detection                                                                                  |
| **`-v` / `-vv`**       | Verbose / very verbose output                                                                 |
| **`-T0` to `-T5`**     | Timing templates: 0=slowest/stealthiest, 5=fastest/noisiest                                   |
| **`/24`**              | CIDR notation for subnet — `/24` = 255 usable IPs in the range                                |
| **Open port**          | A service is actively listening and accepting connections                                     |
| **Filtered port**      | Firewall is blocking the probe — no response                                                  |
| **TTL 128**            | Indicates Windows OS in ping/scan response                                                    |
| **TTL 64**             | Indicates Linux/macOS OS in ping/scan response                                                |
| **SYN scan preferred** | Never completes handshake → less detectable → not blocked by basic firewalls                  |
| **Version grabbing**   | Finding the exact version of a running service to match it against known CVEs                 |

---

## 19. Self-Test Questions

1. What is network scanning and how does it differ from information gathering?
2. List the 5-step simplified scanning methodology in order.
3. What is the difference between a TCP Connect Scan (`-sT`) and a SYN Scan (`-sS`)? Why is SYN scan preferred?
4. Explain why a SYN scan is harder to detect by a firewall than a full TCP connect scan — using the 3-way handshake logic.
5. Write the Nmap command to scan all 65,535 ports on `192.168.1.10` with service and version detection, in verbose mode, using T4 timing.
6. What does the `-O` flag do in Nmap, and what TTL value indicates a Windows target vs. a Linux target?
7. What is the difference between an open, closed, and filtered port in Nmap output?
8. Why does version grabbing matter? Give a concrete example of how a specific version leads directly to an exploitable vulnerability.
9. Write the NetDiscover command to scan the `192.168.0.0/24` network using interface `eth0`.
10. What is the `/24` at the end of an IP range (e.g., `10.10.10.0/24`) and what does it mean for the scan?

---

## What's Next

Network scanning reveals the complete surface area of the target. The next steps in the methodology build on these results:

- **Enumeration** — deep dive into specific services found (SMB enumeration, FTP enumeration, DNS zone transfers, SNMP enumeration, LDAP enumeration)
- **Vulnerability Scanning** — automated tools (OpenVAS, Nessus) match discovered service versions against the CVE database
- **Exploitation** — using Metasploit and standalone exploits to attack specific vulnerable versions
- **Nmap Scripting Engine (NSE)** — Nmap's built-in scripting system that can perform enumeration, vulnerability detection, and even exploitation from within Nmap

_Network scanning with Nmap is a skill every cybersecurity professional must master — it appears in penetration testing, CTF competitions, SOC operations, and certification exams (CEH, OSCP, CompTIA Security+). The commands in these notes are the foundation of all further attack and defense work._
