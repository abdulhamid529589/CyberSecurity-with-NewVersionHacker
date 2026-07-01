# IP Addressing & Networking — Part 4 (In-Depth Notes)

## How IP Addresses Actually Work on the Internet: ISP, Routers, Public/Private IP, NAT, MAC Addresses, ARP, and DHCP

> **Course:** NewVersionHacker — Networking Fundamentals Series (Part 4)
> **Prerequisite:** Parts 1–3 cover what an IP address is and basic addressing concepts. This part explains the _working mechanism_ — how a packet actually travels from your laptop to a server on the internet and back, and which protocols make that possible.

---

## Table of Contents

1. [Big Picture: What This Lecture Is Really About](#1-big-picture-what-this-lecture-is-really-about)
2. [ISPs and the Public IP Pool](#2-isps-and-the-public-ip-pool)
3. [Public IP vs Private IP — The Core Distinction](#3-public-ip-vs-private-ip--the-core-distinction)
4. [The Router's Private IP Range and the Default Gateway](#4-the-routers-private-ip-range-and-the-default-gateway)
5. [NAT: How a Private IP "Becomes" a Public IP](#5-nat-how-a-private-ip-becomes-a-public-ip)
6. [IP Assignment: Manual Configuration vs DHCP](#6-ip-assignment-manual-configuration-vs-dhcp)
7. [MAC Addresses: Structure, Origin, and Purpose](#7-mac-addresses-structure-origin-and-purpose)
8. [ARP: How a Router Maps IP Addresses to MAC Addresses](#8-arp-how-a-router-maps-ip-addresses-to-mac-addresses)
9. [Lease Expiry and IP Reuse When a Device Disconnects](#9-lease-expiry-and-ip-reuse-when-a-device-disconnects)
10. [Localhost (127.0.0.1) — The Loopback Address](#10-localhost-127001--the-loopback-address)
11. [Source IP and Destination IP](#11-source-ip-and-destination-ip)
12. [The Router's Three Identities](#12-the-routers-three-identities)
13. [Every Device's Three Identities](#13-every-devices-three-identities)
14. [Full Packet Journey — Step-by-Step Walkthrough](#14-full-packet-journey--step-by-step-walkthrough)
15. [Hands-On Practical: Windows CMD Networking Commands](#15-hands-on-practical-windows-cmd-networking-commands)
16. [Corrections & Clarifications to the Original Transcript](#16-corrections--clarifications-to-the-original-transcript)
17. [Why This Matters for Cybersecurity](#17-why-this-matters-for-cybersecurity)
18. [Quick Revision Table](#18-quick-revision-table)
19. [Self-Test Questions](#19-self-test-questions)

---

## 1. Big Picture: What This Lecture Is Really About

Up to this point, the series explained _what_ an IP address is. This part answers a more practical question: **when a device sends a request to a website, what is the exact sequence of events, addresses, and protocols involved before a reply comes back?**

At a high level, four layers are involved:

```
Your Device  →  Your Router (LAN boundary)  →  Your ISP  →  The Internet (destination server)
```

Each of these layers has its own addressing scheme, and the transition between layers is where most of the "magic" (and the confusion) happens. This lecture walks through that transition mechanism in detail.

---

## 2. ISPs and the Public IP Pool

An **ISP (Internet Service Provider)** is a company that owns a large block (range) of public IP addresses, licensed/allocated to them by regional internet registries.

- When a customer installs broadband, WiFi, or buys a mobile SIM, the ISP hands out **one public IP address** from its pool to that customer's connection.
- This assigned address is typically a **dynamic IP** — it is not permanently fixed to the customer; the ISP can change it periodically (e.g., on router reboot, lease renewal, or reconnection). ISPs do offer **static IPs** as a paid add-on for customers who need a fixed address (e.g., for hosting servers).
- Think of the ISP as the "wholesaler" of public IP addresses, and your router as the single retail outlet for your home — it takes one public IP and figures out how to share internet access across many devices behind it.

---

## 3. Public IP vs Private IP — The Core Distinction

| Aspect                                     | Public IP                                  | Private IP                                                                    |
| ------------------------------------------ | ------------------------------------------ | ----------------------------------------------------------------------------- |
| Issued by                                  | ISP                                        | Router (from its internal pool)                                               |
| Uniqueness scope                           | Globally unique across the entire internet | Unique only within the local network (LAN)                                    |
| Typical ranges                             | Assigned by IANA/ISPs (varies)             | Reserved private ranges, e.g. `192.168.0.0/16`, `10.0.0.0/8`, `172.16.0.0/12` |
| Can be reached directly from the internet? | Yes                                        | No — never directly routable on the public internet                           |
| Changes how often?                         | Usually dynamic (can change)               | Usually stable while the device stays connected to the same router            |
| Visible to                                 | Servers/websites you connect to            | Only devices on the same LAN                                                  |

### Why can't every device just get a public IP?

There simply aren't enough public IPv4 addresses in the world for every single device (phones, laptops, smart TVs, IoT devices) to have one each — this is a well-known problem called **IPv4 exhaustion**. Private IP ranges + NAT (explained in Section 5) are the practical workaround that lets millions of devices share a much smaller pool of public addresses.

A router solves this by:

1. Receiving exactly **one public IP** from the ISP.
2. Maintaining its **own private IP range** internally (e.g. `192.168.0.1` to `192.168.0.254`), and handing out one private IP to each connected device.

---

## 4. The Router's Private IP Range and the Default Gateway

When you buy a router, it ships with a **default private IP range** pre-configured by the manufacturer (commonly starting at `192.168.0.1` or `192.168.1.1`).

### Critical rule: the first address in the range is reserved

The very **first IP address** in that range (e.g. `192.168.0.1`) is **never** assigned to any connected device. The router keeps this address for itself. This reserved address is called the **Default Gateway**.

**What is a Default Gateway, functionally?**

- It is the router's own private-side IP address.
- Every device on the LAN is configured (automatically, via DHCP) to know this address as "the door to everything outside my local network."
- Any traffic destined for somewhere outside the LAN (i.e., the internet) is first sent to this gateway address, and the router takes it from there.

### Accessing the router's admin panel

Because the default gateway is just an IP address reachable from any device on the LAN, you can type it into a web browser (e.g., `http://192.168.0.1`) to reach the router's **admin configuration panel** — this is where you change the WiFi password, router login password, port forwarding rules, DHCP settings, etc.

- The exact gateway address differs by router brand/model (`192.168.0.1`, `192.168.1.1`, `192.168.1.254`, etc.).
- You must be connected to that specific router's network to access its panel — it isn't reachable from outside the LAN unless remote management is explicitly enabled (a security risk if left on).

---

## 5. NAT: How a Private IP "Becomes" a Public IP

> The original lecture describes this mechanism conceptually without naming it, but what's being described is exactly **NAT — Network Address Translation**. This is one of the most important concepts in practical networking, so it deserves a precise breakdown.

### The fundamental rule

**A private IP can never communicate directly with a public IP, and vice versa.** They exist in two separate addressing "worlds." The router is the only component allowed to bridge between them.

### Step-by-step: what happens when you search something on Google

1. Your device (private IP, e.g. `192.168.0.5`) generates a **packet** — a small unit of data containing, among other things, a _source IP_ (your private IP) and a _destination IP_ (Google's public IP).
2. The packet cannot leave the LAN with a private source IP attached — the wider internet has no idea how to route a reply back to a private address. So the packet first travels to the **default gateway** (the router's private IP).
3. The router receives the packet and performs **address translation**: it rewrites the packet's source IP from your private IP to the router's own **public IP** (the one given by the ISP). It also keeps an internal table tracking which private IP/port made this specific request, so it knows where to send the reply later.
4. The translated packet, now carrying the router's public IP as its source, is forwarded to the ISP, and from there out onto the wider internet, ultimately reaching the destination server.
5. When the **reply** comes back from the server, it is addressed to the router's public IP (since that's all the server ever saw).
6. The router looks up its internal translation table, figures out which internal device originally made the request, rewrites the destination IP back to that device's **private IP**, and forwards the reply inward.

```
 OUTGOING:
 Device (Private IP) → Router (translates Private→Public) → ISP → Internet → Server

 INCOMING (reply):
 Server → Internet → ISP → Router (translates Public→Private) → Device
```

This translation process is what allows **dozens of devices behind a single router to all share one public IP** without conflicts — each outgoing connection is tracked individually (technically via source port numbers in real NAT implementations, though the lecture simplifies this to IP-level switching for conceptual clarity).

---

## 6. IP Assignment: Manual Configuration vs DHCP

A router can assign private IPs to devices in two ways:

### 6.1 Manual (Static) Assignment

- You, the user, manually type an IP address into a device's network settings.
- **Risk:** if you manually pick an IP address that's already in use by another device on the same network, you get an **IP conflict**, and one or both devices may lose network access until it's resolved.
- Manual assignment is mostly used in controlled environments (e.g., assigning a fixed IP to a server, printer, or security camera so it's always reachable at the same address).

### 6.2 Automatic Assignment via DHCP

- The router automatically hands out a free IP address to each device as it joins the network — no manual entry needed, and no conflict risk (because the router tracks which addresses are already in use).
- This is the default and far more practical method for everyday networks.

> **Correction:** The original video transcript refers to this protocol as **"DSCP."** The correct name is **DHCP — Dynamic Host Configuration Protocol**. (DSCP — Differentiated Services Code Point — is an unrelated concept used for QoS/traffic prioritization, not for IP assignment. This is explained further in Section 16.)

**How DHCP works in practice (standard 4-step process — DORA):**

| Step | Name            | What Happens                                                           |
| ---- | --------------- | ---------------------------------------------------------------------- |
| 1    | **Discover**    | Device broadcasts: "I'm new here, is there a DHCP server (router)?"    |
| 2    | **Offer**       | Router replies: "Yes, here's an available IP address you can use."     |
| 3    | **Request**     | Device replies: "OK, I'll take that IP address."                       |
| 4    | **Acknowledge** | Router confirms: "Confirmed, that IP is now yours for a lease period." |

This handshake (DORA) is the standard underlying mechanism, even though the lecture describes it at a simplified, conceptual level.

---

## 7. MAC Addresses: Structure, Origin, and Purpose

A **MAC Address (Media Access Control Address)** is a **hardware-level physical address** burned into every network-capable device — WiFi adapters, ethernet cards, Bluetooth modules, etc.

### Structure

- Total length: **48 bits**
- Represented as **6 pairs (octets)** of hexadecimal digits (each pair = 8 bits)
- Hexadecimal digits use `0–9` and `A–F`

```
Example format:   AA : BB : CC : DD : EE : FF
                  └────┬────┘   └────┬────┘
              Vendor/OUI section   Device-specific section
              (first 3 pairs)      (last 3 pairs)
```

### What the two halves represent

1. **First 3 pairs — Vendor/OUI (Organizationally Unique Identifier):** identifies the manufacturer of the hardware (and indirectly, sometimes, the country/region of registration).
2. **Last 3 pairs — Device-specific serial:** a unique identifier assigned by the manufacturer to that specific unit, ensuring no two devices in the world (in theory) share the same MAC address.

### Hardware vs Software — why this distinction matters

- **Hardware** = anything physically touchable: mouse, keyboard, motherboard, SSD, WiFi adapter, etc.
- **Software** = applications/platforms you use, but cannot physically touch.
- Since a MAC address is tied to _hardware_, it stays fixed to that physical network interface (barring spoofing, which is a separate, deliberate technique used in security contexts).

### MAC vs IP — key conceptual difference

|             | MAC Address               | IP Address                                 |
| ----------- | ------------------------- | ------------------------------------------ |
| Layer       | Data Link Layer (Layer 2) | Network Layer (Layer 3)                    |
| Assigned by | Manufacturer (burned-in)  | Router/DHCP server (or ISP, for public IP) |
| Changes?    | Fixed (unless spoofed)    | Can change (especially dynamic IPs)        |
| Scope       | Local network segment     | Can be local (private) or global (public)  |

---

## 8. ARP: How a Router Maps IP Addresses to MAC Addresses

For the router to correctly deliver a reply to the right device, it needs to know **which MAC address corresponds to which IP address** on its LAN. This mapping is built and maintained by **ARP (Address Resolution Protocol)**.

### Step-by-step ARP process

1. The router sends an **ARP broadcast** to every device on the LAN simultaneously: _"Who has IP address 192.168.0.2?"_
2. Every device receives this broadcast, but only the device that actually owns that IP address replies.
3. That device responds directly to the router: _"I have that IP, and my MAC address is AA:BB:CC:DD:EE:FF."_
4. The router records this **IP ↔ MAC mapping** in a local table called the **ARP cache/table**.
5. From then on, whenever a packet comes from or needs to go to that IP, the router knows exactly which physical device (MAC address) to talk to.

```
Router  → (broadcast to all)  →  "Who has 192.168.0.2?"
Device (owns 192.168.0.2) → (unicast reply) → "I do. MAC: AA:BB:CC:DD:EE:FF"
Router → stores in ARP table → 192.168.0.2 = AA:BB:CC:DD:EE:FF
```

This is exactly why a reply meant for your laptop never accidentally lands on your roommate's phone — DHCP ensures each device has a unique IP, and ARP ensures the router can map that IP back to the exact physical hardware (MAC) it needs to deliver to.

> **Security relevance:** Because ARP broadcasts are trusted by default and unauthenticated, this is the exact mechanism abused in **ARP Spoofing/ARP Poisoning attacks**, where an attacker sends forged ARP replies to trick a router (or other devices) into associating the attacker's MAC address with someone else's IP — enabling Man-in-the-Middle (MITM) attacks. Understanding the legitimate ARP flow above is the foundation for understanding why that attack works.

---

## 9. Lease Expiry and IP Reuse When a Device Disconnects

A natural question: if a router doesn't constantly "watch" each device, how does it know when a device has disconnected so it can reuse that IP address?

- The router periodically re-broadcasts ARP requests asking "who has this IP?"
- If a device has disconnected, it simply **does not reply**.
- After a period of no reply (and/or when a DHCP lease time expires), the router marks that IP address as **vacant/available** again.
- The next new device that joins the network can then be assigned that now-free IP address.

This is conceptually similar to (though simplified from) how real DHCP lease expiration and ARP cache timeout/aging work in production networking equipment.

---

## 10. Localhost (127.0.0.1) — The Loopback Address

Every device, regardless of its network connection status, has a third identity beyond its private/public IPs: **localhost**.

- **IP Address:** `127.0.0.1`
- **Domain name equivalent:** `localhost`
- This address **never leaves the device** — it always points back to the device itself. This is technically called the **loopback address**.

### What it's used for

- Many applications (Word, Excel, local development servers, databases like MySQL running locally, etc.) run a small internal server on the machine itself. Other parts of the same system access that internal server through `127.0.0.1`, without needing internet access at all.
- It's also used to test whether a machine's own networking stack (TCP/IP implementation) is functioning correctly — pinging `127.0.0.1` checks the local stack independent of any actual network hardware or connection.

> **Good to know:** The entire `127.0.0.0/8` range (not just `127.0.0.1`) is technically reserved for loopback use, though `127.0.0.1` is the conventional one almost everyone uses.

---

## 11. Source IP and Destination IP

### Analogy

Think of a trip from Nagpur to Mumbai:

- Going there: **Source = Nagpur**, **Destination = Mumbai**
- Coming back: **Source = Mumbai**, **Destination = Nagpur**

The same logic applies to network packets — source and destination swap depending on the direction of travel.

### Applied to a real request

| Direction                 | Source IP                                                     | Destination IP                                                   |
| ------------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------- |
| Device → Server (request) | Device's private IP (translated to router's public IP by NAT) | Server's public IP                                               |
| Server → Device (reply)   | Server's public IP                                            | Router's public IP (then translated back to device's private IP) |

```
Request:  Device (private) → Router (NAT: private→public) → ISP → Server
Reply:    Server → ISP → Router (public IP received) → Router (NAT: public→private) → Device
```

Every packet carries both addresses in its header at all times — this is what lets routers and servers know exactly where data came from and where a reply should go.

---

## 12. The Router's Three Identities

| #   | Identity               | Description                                                                                    |
| --- | ---------------------- | ---------------------------------------------------------------------------------------------- |
| 1   | **Public IP Address**  | Assigned by the ISP; this is the router's identity as seen from the internet                   |
| 2   | **Private IP Address** | The router's own address within its LAN; functions as the Default Gateway                      |
| 3   | **MAC Address**        | Since the router is itself a physical hardware device, it has its own hardware MAC address too |

---

## 13. Every Device's Three Identities

| #   | Identity                          | Description                                                             |
| --- | --------------------------------- | ----------------------------------------------------------------------- |
| 1   | **Private IP Address**            | Assigned by the router via DHCP (or manually)                           |
| 2   | **MAC Address**                   | Hardware-bound identity, fixed to the network adapter                   |
| 3   | **Localhost Address (127.0.0.1)** | Used purely for internal/self-testing purposes; never leaves the device |

---

## 14. Full Packet Journey — Step-by-Step Walkthrough

Putting everything together, here is the complete journey of a single web request:

```
┌─────────────┐      ┌────────────────────────┐      ┌──────┐      ┌─────────────┐
│   Device     │      │         Router          │      │ ISP  │      │   Server     │
│ Private IP   │─────▶│ Private IP ⇄ Public IP  │─────▶│      │─────▶│ Public IP    │
│ MAC Address  │      │ (NAT translation here)  │      │      │      │ (e.g. Google)│
│ 127.0.0.1    │      │ MAC Address              │      │      │      │              │
└─────────────┘      └────────────────────────┘      └──────┘      └─────────────┘
       ▲                                                                    │
       │                      Reply travels the reverse path                │
       └────────────────────────────────────────────────────────────────────┘
```

1. **Packet generation:** The device creates a packet (e.g., a request for google.com), tagging its own private IP as the source and Google's public IP as the destination.
2. **Local delivery to gateway:** Since the device cannot talk to a public IP directly, the packet is first sent to the **default gateway** (router's private IP) — this hop uses the **MAC address** (resolved via ARP) for actual local delivery.
3. **NAT translation:** The router rewrites the source IP from private → public, logging the translation in its internal NAT table.
4. **Transit through ISP:** The router forwards the now-public-sourced packet to the ISP, which routes it across the internet toward the destination server.
5. **Server processes the request** and sends a reply addressed to the router's public IP.
6. **Reverse NAT translation:** The router receives the reply, checks its NAT table, rewrites the destination IP from public → the original device's private IP.
7. **Final local delivery:** The router uses its **ARP table** to find the correct MAC address for that private IP and delivers the reply to the exact physical device that made the original request.

Throughout this entire flow:

- **DHCP** ensured the device had a valid, conflict-free private IP in the first place.
- **ARP** ensured the router always knew which physical device (MAC) corresponds to which private IP.
- **NAT** ensured the private IP world and the public IP world could communicate without ever talking to each other directly.

---

## 15. Hands-On Practical: Windows CMD Networking Commands

### 15.1 — View IP Configuration

```cmd
ipconfig
```

This reveals:

- Your device's **private IP address** (labeled "IPv4 Address")
- Your **Default Gateway** address
- Subnet mask

For more detail (including MAC address, DHCP status, lease times), use:

```cmd
ipconfig /all
```

> **Note:** Your **public IP** is not shown by `ipconfig` — that's only known to your router and ISP. To check it, search "what is my IP" in a browser, or visit a dedicated IP-checking website.

### 15.2 — View Active Connections / Localhost Usage

```cmd
netstat
```

This shows:

- Which processes/applications are using `localhost` (127.0.0.1) internally without needing the internet
- Active source and destination addresses — i.e., what external addresses your system is currently connected to
- A good first step to verify your network stack is functioning correctly

For more detail with process names and listening ports:

```cmd
netstat -ano
```

### 15.3 — View MAC Address

```cmd
getmac
```

This lists the MAC addresses of all network-capable hardware on your system (WiFi adapter, ethernet card, virtual adapters, etc.).

For more detail:

```cmd
getmac /v /fo list
```

### 15.4 — MAC Address Vendor Lookup

1. Copy a MAC address obtained from `getmac`.
2. Search "**MAC address lookup**" in a browser — several free vendor-lookup tools exist.
3. Paste the MAC address; the tool reads the first 3 octets (the OUI) and returns the manufacturer name, since vendor information is embedded in that portion of the address.

### 15.5 — Public IP / WHOIS Lookup

- To find out who owns/registered a given public IP address, various **IP lookup / WHOIS** websites can be used.
- Not all information is publicly available — some details are kept private by the registrant for privacy reasons.

> **Ethical/security note:** MAC and IP lookup tools should only be used on your own devices/network, or in authorized, legal, educational contexts (e.g., CTFs, authorized penetration tests). Looking up or probing networks/devices you don't own or have explicit permission to test can be illegal depending on jurisdiction.

---

## 16. Corrections & Clarifications to the Original Transcript

Since this is study material, it's worth flagging a couple of places where the spoken transcript used informal or slightly inaccurate terminology, so your notes stay technically correct:

| Transcript said                                                            | Correct term                                                  | Why it matters                                                                                                                                                                       |
| -------------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| "DSCP" (for automatic IP assignment)                                       | **DHCP** (Dynamic Host Configuration Protocol)                | DSCP is an unrelated field used in QoS/packet prioritization at the IP header level, not for address assignment. Confusing the two would be a factual error in an exam or interview. |
| "ARP sends address resolution protocol message to all" (described loosely) | ARP **broadcasts** a request and receives a **unicast reply** | The request is broadcast (sent to everyone), but the actual reply from the owning device is sent directly back (unicast), not also broadcast.                                        |
| NAT process described without naming it                                    | **NAT (Network Address Translation)**                         | Knowing the formal name lets you connect this lecture to standard textbooks, certifications (CompTIA Network+, CCNA), and further reading.                                           |

---

## 17. Why This Matters for Cybersecurity

Since this is part of a cybersecurity curriculum, it's worth explicitly connecting these basics to security topics you'll encounter later:

- **ARP Spoofing / ARP Poisoning:** Exploits the unauthenticated nature of ARP broadcasts to redirect traffic — directly built on the ARP mechanism explained in Section 8.
- **MITM (Man-in-the-Middle) Attacks:** Often achieved by sitting between a device and its default gateway, intercepting the private↔public translation point described in Section 5.
- **Port Scanning / Network Reconnaissance (Nmap, etc.):** Relies on understanding which IPs are active on a network — directly uses the IP/MAC/ARP concepts above.
- **NAT Traversal issues in pentesting:** Understanding why a private IP can't be reached directly from outside explains why port forwarding, VPNs, or reverse shells are needed when targeting machines behind a NAT/router.
- **MAC Spoofing:** A common technique to bypass MAC-based access controls (MAC filtering) — only meaningful once you understand that MAC addresses are _supposed_ to be fixed hardware identifiers.

---

## 18. Quick Revision Table

| Concept                   | One-line Summary                                                                    |
| ------------------------- | ----------------------------------------------------------------------------------- |
| **ISP**                   | Owns a pool of public IPs; assigns one (usually dynamic) to each customer           |
| **Public IP**             | Globally unique, internet-facing address                                            |
| **Private IP**            | LAN-only unique address, assigned by the router                                     |
| **Default Gateway**       | The router's reserved private IP; the "exit door" for all LAN traffic               |
| **NAT**                   | Translates private IP ↔ public IP so internal devices can share one public IP       |
| **DHCP**                  | Automatically assigns IP addresses to devices (corrected from "DSCP" in transcript) |
| **MAC Address**           | 48-bit hardware identity; first 3 octets = vendor (OUI), last 3 = device-specific   |
| **ARP**                   | Maps IP addresses to MAC addresses via broadcast request + unicast reply            |
| **ARP Table/Cache**       | Router's internal record of IP↔MAC mappings                                         |
| **Localhost (127.0.0.1)** | Loopback address; always points back to the same device, never leaves it            |
| **Source/Destination IP** | The "from" and "to" addresses carried in every packet header                        |
| **ipconfig**              | CMD command to view private IP and default gateway                                  |
| **netstat**               | CMD command to view active connections and localhost usage                          |
| **getmac**                | CMD command to view MAC addresses                                                   |

---

## 19. Self-Test Questions

Use these to check your understanding before moving to Part 5:

1. Why can a private IP address never communicate directly with a public IP address?
2. What is the difference between the router's default gateway address and its public IP address?
3. Walk through, in order, what happens to a packet from the moment you hit "search" in a browser until the reply appears on your screen.
4. What is the correct name of the protocol used for automatic IP assignment, and why might "DSCP" be a misleading term to use instead?
5. What two pieces of information does a MAC address encode, and how many bits/pairs make up each part?
6. Explain, step by step, how ARP allows a router to know which physical device a given private IP belongs to.
7. What happens to an IP address when the device using it disconnects from the network?
8. Why does `127.0.0.1` never appear in `netstat` output as traffic going out to the internet?
9. Why is understanding ARP important for understanding ARP spoofing/MITM attacks?
10. Which CMD command would you use to find your device's MAC address, and which to find your private IP and default gateway?

---

## What's Next

This lecture established the full mechanical flow: **ISP → Router (NAT + DHCP) → ARP-based local delivery → Internet → Server → Reverse path back.** Future parts in this series are likely to build on this with:

- Subnetting and CIDR notation (how IP ranges are mathematically divided)
- Routing protocols (how routers decide _which path_ to send packets across the internet)
- Firewalls and port forwarding (critical for both defense and offensive security work)
- Hands-on packet capture and analysis using tools like **Wireshark** or **tcpdump**, where you can literally watch ARP requests, DHCP handshakes, and NAT translations happen in real time

_A solid grasp of this lecture's concepts is the foundation for almost every offensive networking topic you'll study later — including ARP spoofing, MITM attacks, network reconnaissance, and NAT traversal during exploitation._
