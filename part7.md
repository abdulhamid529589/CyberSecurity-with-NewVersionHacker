# Wireshark Practical — Observing TCP Flags Live — Part 7 (In-Depth Notes)

## Capturing and Analyzing Real Network Traffic: ARP, SYN, ACK, FIN, RST, and PSH Flags in Wireshark

> **Course:** NewVersionHacker — Networking Fundamentals Series (Part 7)
> **Prerequisite:** Part 6 is essential — this lecture is the live, hands-on demonstration of every flag and handshake concept taught in Part 6. You will not understand what you're looking at in Wireshark without first understanding TCP flags, the Three-Way Handshake, and connection termination.

---

## Table of Contents

1. [What This Practical Covers](#1-what-this-practical-covers)
2. [What Is Wireshark?](#2-what-is-wireshark)
3. [Installing Wireshark](#3-installing-wireshark)
4. [Wireshark Interface Overview](#4-wireshark-interface-overview)
5. [Selecting a Network Interface to Capture](#5-selecting-a-network-interface-to-capture)
6. [Identifying Your Own IP Address (ifconfig)](#6-identifying-your-own-ip-address-ifconfig)
7. [Reading the Packet List — What Each Column Means](#7-reading-the-packet-list--what-each-column-means)
8. [Observing ARP Live in Wireshark](#8-observing-arp-live-in-wireshark)
9. [Observing the Three-Way Handshake (SYN → SYN+ACK → ACK)](#9-observing-the-three-way-handshake-syn--synack--ack)
10. [Observing the FIN + PSH Flag Combination](#10-observing-the-fin--psh-flag-combination)
11. [Observing the RST (Reset) Flag](#11-observing-the-rst-reset-flag)
12. [Observing ACK Packets During Data Transfer](#12-observing-ack-packets-during-data-transfer)
13. [The URG Flag — Why You Won't See It Normally](#13-the-urg-flag--why-you-wont-see-it-normally)
14. [Closing a Browser and Watching Connections Terminate](#14-closing-a-browser-and-watching-connections-terminate)
15. [Source and Destination Swap — Confirmed Live](#15-source-and-destination-swap--confirmed-live)
16. [Color Coding in Wireshark](#16-color-coding-in-wireshark)
17. [Wireshark Filters — How to Focus on What Matters](#17-wireshark-filters--how-to-focus-on-what-matters)
18. [Corrections & Clarifications to the Original Transcript](#18-corrections--clarifications-to-the-original-transcript)
19. [Why This Matters for Cybersecurity](#19-why-this-matters-for-cybersecurity)
20. [Quick Revision Table](#20-quick-revision-table)
21. [Self-Test Questions](#21-self-test-questions)

---

## 1. What This Practical Covers

This is the first fully hands-on, practical session in the series. Everything taught conceptually in Part 6 — TCP flags, the Three-Way Handshake, connection termination, ARP requests — is now observed _live_ inside a real packet capture using **Wireshark**.

The core purpose of this practical:

- Prove that the theoretical concepts from Part 6 are not abstract — they are real, observable events happening on your network right now, in the background of every single connection.
- Build comfort with Wireshark's interface so you can use it independently in future sessions.
- See exactly how packets flow between your system and servers, including which flags appear at which stage.

---

## 2. What Is Wireshark?

**Wireshark** is a free, open-source **network packet analyzer** (also called a packet sniffer or traffic analyzer).

In plain language, Wireshark listens on a network interface and **captures every packet** that passes through it — whether sent by your system or received from the network. It then displays those packets in a readable, organized format, broken down by protocol, flags, source/destination, timing, and much more.

### Why Wireshark is important

- It allows you to see networking in action at the raw packet level — not just a high-level "connection succeeded" message, but the actual SYN, ACK, FIN flags and their exact sequence.
- It is an industry-standard tool used by network engineers, security analysts, and penetration testers worldwide.
- It is equally valuable for **defense** (network troubleshooting, intrusion analysis) and **offense** (intercepting unencrypted traffic, understanding protocols before attacking them).

---

## 3. Installing Wireshark

| Platform          | How to Get Wireshark                                                                                  |
| ----------------- | ----------------------------------------------------------------------------------------------------- |
| **Kali Linux**    | Pre-installed — no action needed. Open from the applications menu.                                    |
| **Parrot OS**     | Usually pre-installed as well. If not: `sudo apt install wireshark`                                   |
| **Windows**       | Download the installer from [https://www.wireshark.org](https://www.wireshark.org) and run it.        |
| **Ubuntu/Debian** | `sudo apt install wireshark` — during setup, choose "Yes" to allow non-root users to capture packets. |

> **Kali-specific note from the lecture:** In Kali Linux, Wireshark can be found by clicking the Kali icon/menu on the sidebar and searching for "Wireshark." Clicking it will open the tool directly. Kali Linux is the preferred OS for this course's practical sessions; upcoming videos will cover Kali installation for those who haven't set it up yet.

---

## 4. Wireshark Interface Overview

When Wireshark opens, you are presented with three main areas:

### 4.1 — Network Interface Selection Screen (startup)

Before capturing, Wireshark lists all available network interfaces on your system and shows a small live graph of traffic activity beside each one. You must **select the correct interface** to capture (covered in Section 5).

### 4.2 — Packet List Pane (top panel)

Once capturing starts, this is the scrolling list of all packets captured in real time — one row per packet. This is what the lecture focuses on throughout the practical.

### 4.3 — Packet Details Pane (middle panel)

Clicking any packet in the list expands the full breakdown of its contents — headers, flags, sequence numbers, acknowledgment numbers, payload length, and more.

### 4.4 — Packet Bytes Pane (bottom panel)

Shows the raw hexadecimal and ASCII representation of the selected packet's bytes. Used for deep inspection — less relevant for beginner sessions.

---

## 5. Selecting a Network Interface to Capture

When Wireshark starts, it asks: **which network interface do you want to monitor?**

This matters because your system may have several interfaces:

- `eth0` / `ens33` — wired Ethernet
- `wlan0` — wireless WiFi
- `lo` — loopback (localhost traffic only)
- `docker0`, `virbr0` — virtual/bridged interfaces created by VMs or containers

**You need to select the interface that is actually carrying your active network traffic.** In the lecture, the relevant interface was `eth0` (labeled `et0` in the transcript), which was confirmed using `ifconfig` (see Section 6).

To identify the correct interface before opening Wireshark:

```bash
ifconfig
```

or (on systems without ifconfig):

```bash
ip addr
```

Look for the interface that has your current IP address assigned — that's the one to capture on.

Once you double-click the correct interface in Wireshark, it begins live capture immediately.

---

## 6. Identifying Your Own IP Address (ifconfig)

The lecture uses `ifconfig` inside the Kali terminal to identify which IP address belongs to the current system. This is important in Wireshark because, with potentially hundreds of packets flying by, you need to know **which packets are yours** (i.e., which packets have your IP as source or destination).

```bash
ifconfig
```

In the lecture example:

- The interface was `eth0` (or `et0`)
- The IP shown was something like `192.168.33.166` (the last octet `.166` was used to visually identify the system's packets in the Wireshark capture)

> **Note on VM networking (covered in the lecture):** When running Kali inside a virtual machine (VirtualBox, VMware), the router's IP seen by Wireshark may differ from what you'd see on a physical home network (e.g., the router shows `.2` instead of `.1`). This is because the VM uses a virtual router/NAT managed by the hypervisor software — functionally the same concept, just a different IP within the VM's virtual LAN. The real-world principles are identical.

---

## 7. Reading the Packet List — What Each Column Means

When packets start flowing in Wireshark, the packet list shows these columns:

| Column          | What It Shows                                                                                                                                      |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **No.**         | Packet number — sequential count from the start of capture                                                                                         |
| **Time**        | Time elapsed since capture started (in seconds); shows how long each packet took relative to the start                                             |
| **Source**      | The IP address the packet was sent **from**                                                                                                        |
| **Destination** | The IP address the packet is being sent **to**                                                                                                     |
| **Protocol**    | The protocol being used (e.g., TCP, UDP, ARP, TLSv1.3, HTTP, HTTPS, DNS)                                                                           |
| **Length**      | Size of the packet in bytes                                                                                                                        |
| **Info**        | A human-readable summary of what this packet contains (e.g., `SYN`, `SYN, ACK`, `ACK`, `FIN, ACK`, `RST`, specific port numbers and sequence info) |

### How to tell which packets are yours

Look at the **Source** column for your IP address (confirmed via `ifconfig`) — any packet sourced from your IP is data _you sent_. Any packet with your IP in the **Destination** column is data _coming back to you_.

---

## 8. Observing ARP Live in Wireshark

The very first thing the lecture points out upon opening Wireshark is **ARP packets** appearing in the capture.

This is significant because ARP was taught theoretically in Part 4 — and here it is, happening in the background without any deliberate action, proving the concept is real and active.

### What you see in Wireshark for ARP:

```
Protocol: ARP
Info:     Who has 192.168.33.2? Tell 192.168.33.166
```

This is the router (or VM virtual router) broadcasting an ARP request — asking the network _"which device has this IP address?"_ — exactly as described in Part 4's explanation of ARP.

Shortly after, a reply packet appears:

```
Protocol: ARP
Info:     192.168.33.2 is at [MAC address]
```

This confirms:

- ARP broadcast (request) — _"Who has this IP?"_
- ARP unicast reply — _"I do, here's my MAC address."_

**The ARP mechanism from Part 4's theory is visibly operating in real time.** ARP requests will continue appearing periodically in the background as long as Wireshark is capturing — the router regularly re-verifies its ARP table.

---

## 9. Observing the Three-Way Handshake (SYN → SYN+ACK → ACK)

When the browser is opened or a website is visited, Wireshark captures the full TCP Three-Way Handshake. Here is what you'll see in the **Info** column, exactly in this order:

```
Step 1:  [Source: Your IP]        →  [Destination: Server IP]   Info: SYN
Step 2:  [Source: Server IP]      →  [Destination: Your IP]     Info: SYN, ACK
Step 3:  [Source: Your IP]        →  [Destination: Server IP]   Info: ACK
```

### Mapping this to theory (Part 6):

| Wireshark Info Column | What it means                                                             |
| --------------------- | ------------------------------------------------------------------------- |
| `SYN`                 | Your system initiates the connection — _"I want to connect"_              |
| `SYN, ACK`            | The server acknowledges your request AND signals it also wants to connect |
| `ACK`                 | Your system confirms — connection is now established, data can flow       |

### Key observation about source/destination:

- During the **SYN** packet: **Your IP = Source**, **Server IP = Destination**
- During the **SYN+ACK** reply: **Server IP = Source**, **Your IP = Destination** ← these have swapped
- During the final **ACK**: **Your IP = Source** again, **Server IP = Destination**

This live swap of source and destination directly confirms the concept from Part 4 — _when a reply comes back, source and destination reverse._

> In the lecture, as soon as the browser was opened (even before navigating anywhere), multiple handshakes appeared immediately — because modern browsers pre-establish connections to several servers in the background (CDNs, analytics, update services, etc.) the moment they launch.

---

## 10. Observing the FIN + PSH Flag Combination

When a connection closes gracefully, you will often see:

```
Info: FIN, PSH, ACK
```

This combination of three flags may initially seem confusing. Here is why each flag appears together:

| Flag    | Why it appears at connection closure                                                                                                                                                                             |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **FIN** | Signals the end of data — the sender is done and requesting connection termination                                                                                                                               |
| **PSH** | Ensures any data still sitting in the network buffer is immediately flushed and delivered before the connection closes — preventing data loss from buffered data being silently dropped when the connection ends |
| **ACK** | Acknowledges the last piece of data or the last flag received from the other side                                                                                                                                |

The FIN+PSH combination is therefore a **safety measure** — FIN says "we're done," and PSH says "but first, make absolutely sure nothing is stuck in the buffer." After this, the standard FIN→ACK→FIN→ACK termination sequence (from Part 6) completes.

---

## 11. Observing the RST (Reset) Flag

In Wireshark, RST packets appear **highlighted in red** — this is Wireshark's default color coding to draw attention to connection resets, which often indicate something went wrong.

```
Info: RST
  or
Info: RST, ACK
```

### When RST appears in practice:

- A connection attempt reached a port that is **closed or not listening** — the server sends RST to say _"nothing here, stop trying."_
- A **network error** caused the connection to reach an unrecoverable state — RST clears it so both sides can start fresh if needed.
- One side forcefully closes the connection mid-session (as opposed to the graceful FIN sequence).
- **Abruptly closing an application** (as shown at the end of the lecture when the browser was force-closed) can trigger RST rather than FIN, because there was no time to go through the formal 4-step FIN sequence.

> **Security relevance:** RST packets are used offensively in **RST injection / TCP reset attacks** — an attacker sends a forged RST packet (spoofing the source IP of one legitimate party) to force-terminate an ongoing connection between two systems that had nothing wrong.

---

## 12. Observing ACK Packets During Data Transfer

Once a connection is established (post-handshake), the bulk of ongoing traffic is a mix of:

- **Data packets** carrying actual content (web page HTML, images, API responses, etc.)
- **ACK packets** confirming receipt of the data packets

```
Info: ACK
Info: ACK (seq=..., ack=..., len=0)
```

ACK packets themselves carry **no data** (length = 0) — they are pure confirmations. Their high frequency is why ACK is by far the most common flag visible in any live Wireshark capture.

---

## 13. The URG Flag — Why You Won't See It Normally

The **URG (Urgent)** flag was explained in Part 6 as a way to mark certain data as high-priority. In practice:

- URG is **extremely rarely used** in modern TCP implementations.
- Most modern applications and protocols have their own higher-level prioritization mechanisms and don't rely on TCP's URG flag.
- You may capture thousands of packets in a normal Wireshark session and never see a single URG flag.
- The lecture explicitly confirms this: URG is not visible in the captured traffic, and that is entirely normal.

> **Security note:** URG flags have historically appeared in **OS fingerprinting** and **evasion techniques** in security tools, because unusual flag combinations (URG set alone, or combined with FIN/SYN in ways normal traffic doesn't produce) are used to probe how different operating systems respond.

---

## 14. Closing a Browser and Watching Connections Terminate

The lecture demonstrates that **closing a browser triggers visible termination packets** in Wireshark in real time.

### What happens when you close a browser:

1. The browser initiates graceful closure of all active connections → **FIN, PSH, ACK** packets appear.
2. Servers acknowledge → **ACK** packets arrive.
3. Servers send their own FIN → more **FIN, ACK** packets.
4. Final acknowledgments close everything out → **ACK** packets.

**If the browser is force-closed** (killed without graceful shutdown), the OS may send **RST** packets instead of FIN — because there's no time for the orderly 4-step FIN sequence when a process is terminated abruptly.

This live behavior directly confirms the connection termination theory from Part 6:

- Graceful close = FIN sequence
- Abrupt close = RST

---

## 15. Source and Destination Swap — Confirmed Live

A key theoretical point from Part 4 (IP addresses) and Part 6 (TCP communication) is confirmed live in this practical:

> **When data goes from A to B, A is source and B is destination. When the reply comes back, B becomes source and A becomes destination.**

In Wireshark, you can see this clearly — when the SYN goes out from your IP, your IP is in the Source column. When the SYN+ACK reply arrives from the server, the server's IP is in the Source column and your IP is in the Destination column. This flip happens for every single request-reply pair in the entire capture.

---

## 16. Color Coding in Wireshark

Wireshark uses color coding by default to help quickly identify different types of traffic:

| Color                   | Typical meaning                                                |
| ----------------------- | -------------------------------------------------------------- |
| **Light green**         | TCP traffic (standard connections)                             |
| **Light blue**          | UDP traffic                                                    |
| **Black (red text)**    | TCP errors, bad checksums, retransmissions                     |
| **Red background**      | RST (reset) packets — connection errors or forced terminations |
| **Yellow/light yellow** | ARP or routing protocol traffic                                |
| **Dark green**          | HTTP traffic                                                   |
| **Light gray**          | TCP ACK-only packets (no data)                                 |

> Colors can be customized under **View → Coloring Rules** in Wireshark. The defaults are designed so that errors and anomalies stand out immediately.

---

## 17. Wireshark Filters — How to Focus on What Matters

A real network capture generates hundreds or thousands of packets per second. Wireshark's **display filter bar** (at the top of the packet list) lets you narrow down what you're seeing to only relevant packets. This is essential for practical work.

### Most useful basic filters for this lecture's topics:

```
# Show only TCP traffic
tcp

# Show only SYN packets (connection initiations)
tcp.flags.syn == 1 && tcp.flags.ack == 0

# Show only SYN+ACK packets (server responses to connection requests)
tcp.flags.syn == 1 && tcp.flags.ack == 1

# Show only FIN packets (connection terminations)
tcp.flags.fin == 1

# Show only RST packets (resets — errors or forced closes)
tcp.flags.reset == 1

# Show only ACK packets (acknowledgments)
tcp.flags.ack == 1

# Show only ARP packets
arp

# Show only traffic to/from a specific IP
ip.addr == 192.168.33.166

# Show only HTTP traffic (port 80)
tcp.port == 80

# Show only HTTPS traffic (port 443)
tcp.port == 443

# Filter by protocol name
http
dns
```

Type any of these into the filter bar and press Enter — the packet list will immediately narrow to show only matching packets. This is how security analysts isolate specific connections or flag types from a busy capture.

---

## 18. Corrections & Clarifications to the Original Transcript

| Transcript said                                        | Correct / Clarified version                                                                                                                                                               | Why it matters                                                                                                                                                       |
| ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "Wire Shark" (two words)                               | **Wireshark** (one word)                                                                                                                                                                  | The correct spelling is one word — important when searching for the tool or its documentation.                                                                       |
| "IF CONFIN fig"                                        | **`ifconfig`** (one word, lowercase)                                                                                                                                                      | The correct Linux command is `ifconfig`. On newer systems, `ip addr` is the modern equivalent.                                                                       |
| "three way hand check"                                 | **Three-Way Handshake**                                                                                                                                                                   | The standard technical term; "handcheck" is informal.                                                                                                                |
| Router shows `.2` instead of `.1` in VM environment    | This is normal — **virtual machines use a virtual NAT router** managed by the hypervisor (VirtualBox/VMware), which assigns itself a different IP (often `.2`) in the VM's private subnet | Students running Kali in VirtualBox or VMware will see this and should not be confused — it's the VM's virtual gateway, functionally identical to a real home router |
| Transcript says "ET zero" for the interface            | The Linux interface name is **`eth0`** (ethernet zero)                                                                                                                                    | Exact spelling matters when typing commands; `et0` would not be recognized                                                                                           |
| UDP full name shown as "User Data Protocol" in passing | **User Datagram Protocol**                                                                                                                                                                | Covered in Part 6 notes — repeated here for emphasis since the transcript carries the error again                                                                    |

---

## 19. Why This Matters for Cybersecurity

This practical session introduces the foundational skill of **packet analysis** — one of the most critical capabilities in the entire cybersecurity field:

- **Network Traffic Analysis / Blue Team:** Security analysts at a SOC (Security Operations Center) use Wireshark (or its commercial equivalents like Network Miner, Zeek, and Suricata) to investigate suspicious traffic, identify intrusions, and reconstruct attack chains from packet captures.

- **Penetration Testing / Red Team:** Pentesters use Wireshark to understand how target applications communicate, identify unencrypted credentials passing over HTTP, or confirm that an attack (like ARP spoofing) is working as intended.

- **Understanding TLS/SSL Encryption:** The lecture briefly notes that before the actual SYN→SYN+ACK→ACK handshake, modern HTTPS connections go through a TLS/SSL key exchange. This additional layer is why you can't see the actual content of most web traffic in Wireshark (it's encrypted) — but you CAN still see the TCP flags, handshakes, and connection metadata. This will be explored in future sessions.

- **ARP Spoofing Attacks (live in Wireshark):** Having seen ARP requests live in Wireshark, it becomes immediately clear how an attacker could poison those ARP responses — sending a forged reply saying _"I have that IP"_ with the attacker's MAC address, causing the router to route victim traffic through the attacker's machine.

- **Detecting SYN Scans:** In a SYN scan (Nmap's default scan type), you see many SYN packets going out to different ports with **no corresponding ACK to complete the handshake** — a distinctive pattern that is highly visible in Wireshark and one of the primary signatures that IDS systems look for.

- **Wireshark in CTF (Capture The Flag) competitions:** Analyzing .pcap (packet capture) files is one of the most common challenge types in cybersecurity CTF competitions — the skills practiced in this session are directly applied there.

---

## 20. Quick Revision Table

| Concept                     | What You See in Wireshark                                                                 |
| --------------------------- | ----------------------------------------------------------------------------------------- |
| **ARP Request**             | Protocol = ARP; Info = _"Who has [IP]? Tell [your IP]"_                                   |
| **ARP Reply**               | Protocol = ARP; Info = _"[IP] is at [MAC address]"_                                       |
| **TCP Three-Way Handshake** | Three consecutive packets: SYN → SYN,ACK → ACK                                            |
| **SYN**                     | Your IP as source, server IP as destination; Info = `SYN`                                 |
| **SYN, ACK**                | Server IP as source, your IP as destination; Info = `SYN, ACK`                            |
| **ACK**                     | Seen constantly during data transfer; Info = `ACK`; Length typically 0 (no data payload)  |
| **FIN, PSH, ACK**           | Appears when a connection closes gracefully; PSH ensures buffered data isn't lost         |
| **RST**                     | Highlighted in red; abrupt connection termination due to error or force-close             |
| **URG**                     | Almost never seen in normal traffic captures                                              |
| **Source/Destination swap** | Every time a reply comes, the source and destination IPs in the columns are reversed      |
| **ifconfig**                | Linux command to find your own IP address before reading Wireshark output                 |
| **Display filters**         | Typed into the filter bar to isolate specific traffic (`tcp.flags.syn == 1`, `arp`, etc.) |

---

## 21. Self-Test Questions

1. What does Wireshark do, in plain language? How is it different from just checking your browser's network tab?
2. Why do you need to know your own system's IP address before reading a Wireshark capture?
3. In a Wireshark capture, you see three packets in sequence: `SYN`, then `SYN, ACK`, then `ACK`. What is happening, and which system is sending each one?
4. Why does source and destination appear to "swap" between Step 1 and Step 2 of the handshake?
5. Why does ARP traffic appear in Wireshark even before you do anything — before even opening a browser?
6. You see a packet with `FIN, PSH, ACK` in the Info column. What is each flag doing, and why are they combined?
7. What does an RST packet look like in Wireshark (including color), and what does it indicate?
8. Why is the URG flag almost never seen in a normal capture?
9. Write the Wireshark display filter to show only FIN packets.
10. What is the difference between what you observe when a browser is closed gracefully versus when it is force-killed?

---

## What's Next

This practical session established Wireshark as a key tool and visually confirmed all TCP flag and handshake theory from Part 6. Upcoming sessions are likely to cover:

- **Kali Linux installation** — setting up the full security-focused lab environment for future practicals
- **TLS/SSL handshake** — the encryption layer that wraps HTTPS connections, visible in Wireshark as a series of `Client Hello`, `Server Hello`, and `Certificate` packets _before_ the data phase begins
- **Deeper Wireshark usage** — applying display filters to hunt for specific patterns, reconstructing TCP streams, exporting captured data
- **Active attack demonstrations** — tools like Nmap performing port scans, where the resulting SYN packets (without completed handshakes) are directly observable in Wireshark

_Being comfortable with Wireshark at this stage will make every future practical session significantly easier — the ability to see what's happening at the packet level is one of the sharpest diagnostic and analytical tools available to any network security professional._
