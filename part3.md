# Cybersecurity Course — Part 3: Types of IP Addresses, IPv4 vs IPv6, and First Practical (Metasploitable Access via Nmap + Netcat)

> Source: Lecture transcription (Part 3 of course series) — Channel: "NewVersionHacker"
> Prerequisites: [Part 1 — Intro, Ethical Hacking, Networking Basics](./Cybersecurity-Course-Part1-Intro-Ethical-Hacking-Networking-Basics.md) | [Part 2 — Network Types, IP Address, ISP](./Cybersecurity-Course-Part2-Network-Types-IP-Address-ISP.md)
> Topics covered: Types of IP Addresses (Public/Private/Static/Dynamic), live IP-check practical, IPv4 vs IPv6 (Dotted Decimal vs Hexadecimal notation, prefix/network-vs-host bits), and a first hands-on lab using Nmap + Netcat against a Metasploitable target in a VM environment.

---

## Table of Contents

1. [Course Announcements](#1-course-announcements)
2. [Why Theory Matters Before Practical](#2-why-theory-matters-before-practical)
3. [The Four Types of IP Addresses — Overview](#3-the-four-types-of-ip-addresses--overview)
4. [Private IP Address](#4-private-ip-address)
5. [Public IP Address](#5-public-ip-address)
6. [Public vs Private — The Communication Rule](#6-public-vs-private--the-communication-rule)
7. [Why Static & Dynamic IP Exist — The IPv4 Exhaustion Problem](#7-why-static--dynamic-ip-exist--the-ipv4-exhaustion-problem)
8. [Static IP Address](#8-static-ip-address)
9. [Dynamic IP Address](#9-dynamic-ip-address)
10. [Static vs Dynamic — School/Home Analogy](#10-static-vs-dynamic--schoolhome-analogy)
11. [Practical: Checking Your Own Dynamic Public IP](#11-practical-checking-your-own-dynamic-public-ip)
12. [IPv4 vs IPv6 — Versions, Not Types](#12-ipv4-vs-ipv6--versions-not-types)
    - [12.1 IPv4 Recap — Dotted Decimal Notation](#121-ipv4-recap--dotted-decimal-notation)
    - [12.2 Prefix Notation / Network vs Host Bits (IPv4)](#122-prefix-notation--network-vs-host-bits-ipv4)
    - [12.3 IPv6 — Structure & Hexadecimal Notation](#123-ipv6--structure--hexadecimal-notation)
    - [12.4 IPv6 Zero-Compression Rule](#124-ipv6-zero-compression-rule)
    - [12.5 IPv6 Network vs Host Bits](#125-ipv6-network-vs-host-bits)
    - [12.6 IPv4 vs IPv6 Comparison Table](#126-ipv4-vs-ipv6-comparison-table)
13. [Hands-On Practical: Accessing a Target System](#13-hands-on-practical-accessing-a-target-system)
    - [13.1 Lab Setup](#131-lab-setup)
    - [13.2 Step 1 — Host Discovery with Netdiscover](#132-step-1--host-discovery-with-netdiscover)
    - [13.3 Step 2 — Service & OS Enumeration with Nmap](#133-step-2--service--os-enumeration-with-nmap)
    - [13.4 Step 3 — Gaining Access with Netcat](#134-step-3--gaining-access-with-netcat)
    - [13.5 Step 4 — Verifying Access](#135-step-4--verifying-access)
    - [13.6 Step 5 — Proving Control (Reboot Test)](#136-step-5--proving-control-reboot-test)
14. [Ethical Disclaimer (Instructor's Own Words)](#14-ethical-disclaimer-instructors-own-words)
15. [Key Takeaways / Summary](#15-key-takeaways--summary)
16. [Glossary](#16-glossary)
17. [Commands Reference (This Lesson)](#17-commands-reference-this-lesson)

---

## 1. Course Announcements

- The channel ("NewVersionHacker") was approaching **100 subscribers**, and the instructor thanked the audience for their support.
- **Major announcement:** The instructor plans to make a **complete, free cyber security and digital forensics course** available on the channel, specifically to support learners who cannot afford paid courses, so they can still build their careers in this field.
- Standard call-to-action: subscribe, comment, like, and enable notifications ("bell icon") — engagement helps the instructor gauge interest and stay motivated to continue the series.

---

## 2. Why Theory Matters Before Practical

The instructor addresses learner fatigue with theory-heavy lessons (specifically around IP addressing):

> **Instructor's framing:** Just as learning "ABCD" is a compulsory foundation before reading/writing in school, networking theory (especially around IP addresses) is the **compulsory base** for cyber security. Theory clears your conceptual foundation; practical work gives you the tools to apply that foundation in real life. Both are necessary — neither replaces the other.

This sets up today's focus: completing the **types of IP addresses** topic (building on Part 2's introduction to what an IP address is and what it's used for), before moving into the first hands-on practical of the series.

---

## 3. The Four Types of IP Addresses — Overview

The instructor states there are **four types of IP addresses total**, grouped into two conceptual pairs to avoid confusion:

| Pair       | Types                  | Classified By                                  |
| ---------- | ---------------------- | ---------------------------------------------- |
| **Pair 1** | Public IP / Private IP | **Where** the address is used (scope/location) |
| **Pair 2** | Static IP / Dynamic IP | **Whether** the address changes over time      |

> The instructor deliberately teaches **Public/Private first**, then **Static/Dynamic** separately, to avoid mixing the two classification systems and confusing learners — these are **two independent ways of categorizing IP addresses**, not four mutually exclusive types.

---

## 4. Private IP Address

**Definition:** A **Private IP address** is used within a **small, local network range** — specifically within a **LAN (Local Area Network)**.

**Example setup described:**

- A home **router** (WiFi/broadband box installed at home) assigns private IP addresses to all the devices connected to it — whether connected via **WiFi (wireless)** or **Ethernet cable (wired)**.
- All devices connected to that single router, within that room/floor, form the **LAN**, and **private IP addresses** are what those devices use to identify each other **within that local network**.

> **Key point:** Private IP addresses operate within a confined, small range (LAN) — they are **not** what's used to communicate with the broader internet directly.

---

## 5. Public IP Address

**Definition:** A **Public IP address** is used for **Wide Area Network (WAN)** communication — i.e., for accessing the **internet** at large.

**How it relates to the router setup:**

- While devices _inside_ your home network use **private IPs** to talk to each other (and to the router) on the LAN side,
- The **router itself** is also assigned a **public IP address** — this is the address visible on the **other side of the cable** connecting your home router to your ISP's broader network.
- This public IP address is what **identifies your network's connection to the internet** as a whole.

**Who provides the public IP?**

- The instructor reconfirms: your **ISP (Internet Service Provider)** — the company that installed your broadband/provides your SIM's data service — is the one that issues your **public IP address**.

---

## 6. Public vs Private — The Communication Rule

> **Critical rule emphasized by the instructor:** A **Public IP address can never directly communicate with a Private IP address**, and vice versa — **these two address spaces cannot talk to each other directly.**

- The **private IP** (used inside the LAN, behind the router) and the **public IP** (used by the router to reach the internet) exist in **separate communication scopes**.
- The instructor notes that a system (mechanism) exists to bridge/translate between these two scopes (referencing the router's role in enabling internal devices to still reach the internet despite this separation) — and promises to explain this mechanism in more depth in a future lesson (this is foreshadowing **NAT — Network Address Translation**, though not named explicitly in this transcript).

---

## 7. Why Static & Dynamic IP Exist — The IPv4 Exhaustion Problem

The instructor introduces the **second classification pair** (Static/Dynamic) by first explaining the **underlying problem** that necessitated it:

- Recall from Part 2: an IPv4 address is composed of **4 pairs (octets)**, each ranging from 0–255.
- The **total number of unique IPv4 addresses possible** from this combination is approximately **4.2–4.7 billion** (the instructor states "4.7 billion" as an approximate/rounded figure).
- However, the **global internet user base is roughly 7 billion+ people** — meaning there are **more potential users than available unique IPv4 addresses**.
- **Solution:** Rather than giving every device a permanently fixed address (which would run out quickly), addresses were divided into two behavioral categories: **Static** and **Dynamic** — allowing IP addresses to be **reused/reassigned** when not actively needed, easing the shortage.

> **Note on accuracy:** The actual total number of IPv4 addresses is **2³² ≈ 4.29 billion** — the instructor's "4.7 billion" figure is a rounded approximation used for teaching purposes.

---

## 8. Static IP Address

**Definition:** **Static** = fixed, unchanging. A Static IP address is **permanently assigned** and does not change over time.

**Where it's used:** Primarily on **servers**.

- A **server** is described as a very large, high-storage computer system — much bigger and more powerful than a typical personal computer — responsible for hosting the websites you visit and the data you download.
- **Why servers need static IPs:** If a server's IP address changed daily, users (and browsers, DNS systems, etc.) would not be able to reliably locate and access that website/service.
- **Examples given:** Large websites like **Facebook** and **Twitter** maintain **fixed (static) IP addresses** precisely because their address must remain consistent and discoverable at all times.

---

## 9. Dynamic IP Address

**Definition:** **Dynamic** = changeable. A Dynamic IP address is **not permanently fixed** — it can be reassigned/changed over time.

**Who uses dynamic IPs:** Everyday users/devices — e.g., regular people's phones and home internet connections.

**Why dynamic IPs make sense for regular users:**

- If your phone's mobile data **recharge expires**, your phone loses internet access. At that point, continuing to hold onto a unique IP address for that idle phone would be **wasteful**, since the address isn't being actively used.
- Instead, the **ISP logs the date/time** that IP address was held by a given device (e.g., "Phone A held this IP from 12:00 to 1:00 on the 30th"), then **reallocates that same IP address to a different device/user** once it's no longer in use.
- When the original device reconnects later (e.g., after recharging), it is issued a **different** IP address from the available pool.

> **Outcome:** This dynamic reassignment system is what allows billions of devices to **share a limited pool of IPv4 addresses** over time, easing the IPv4 address shortage described in Section 7.

---

## 10. Static vs Dynamic — School/Home Analogy

The instructor offers a clear analogy to distinguish why **some** things need static IPs while others are fine with dynamic ones:

> **Scenario:** Imagine your **school's address** is "Zone A," and your **home's address** is "Zone B."
>
> - If your **school's address changed every day** (sometimes Zone B, sometimes Zone C), you would constantly struggle to find your way there — leading to confusion, missed days, and frustration. **This is why a server (like a school) needs a static/fixed address.**
> - But if **your home's address changed** instead, it wouldn't affect your ability to get to school — because you only need to know the school's (fixed) address to get there; your own home address changing doesn't disrupt that journey. **This is why a regular device (like your phone) can function fine with a dynamic/changing address.**

**Applied to the internet:** When you access a website/server, **that server's IP must stay fixed (static)** for you to reliably reach it — but **your own device's IP address can change (dynamic)** without affecting your ability to browse, since you're always looking up the _server's_ fixed address, not the other way around.

---

## 11. Practical: Checking Your Own Dynamic Public IP

The instructor walks through a simple live demonstration to let learners observe their own **dynamic public IP address changing**:

### Steps:

1. Open any web browser.
2. Search: **`what is my IP`** or **`what is my IPv4`**.
3. Click one of the top results — this will display your **current public IP address** (and IP version). **Note this value down.**
4. **Important caveat:** If you're connected via your **home WiFi router**, your IP **will not change** unless the router itself is power-cycled/turned off — because the router maintains a relatively stable connection to the ISP.
5. To actually observe the **dynamic** nature of an IP, you must instead:
   - Connect your **phone to mobile data (SIM/tower network)** instead of WiFi.
   - Revisit the "what is my IP" website and **note the IP address shown**.
   - Turn on **Airplane Mode**, wait, then turn it back off (reconnecting to the mobile network).
   - Revisit the same website again — you should now observe a **different IP address**, demonstrating the dynamic reassignment in action.

> **Additional notes from the instructor:**
>
> - The IP address shown on these "what is my IP" websites is your **public IP address**.
> - Your public IP **will not be visible** in your device's local network settings — it can only be observed via an external lookup (like the browser method above), since it represents how the outside internet sees your connection.
> - Your **private IP address**, by contrast, _can_ be viewed locally — e.g., via your system's terminal (commands such as `ipconfig` on Windows or `ifconfig`/`ip a` on Linux — referenced later in the practical section).

---

## 12. IPv4 vs IPv6 — Versions, Not Types

> **Important conceptual distinction made by the instructor:** Public/Private and Static/Dynamic are **types** of IP addresses (classified by usage/behavior). **IPv4 and IPv6, by contrast, are _versions_** of the IP addressing system itself (i.e., generational upgrades to the underlying addressing scheme) — not "types" in the same sense.

### 12.1 IPv4 Recap — Dotted Decimal Notation

- As covered in Part 2: IPv4 addresses are **32-bit**, written as **4 dot-separated pairs (octets)**, each ranging from 0–255.
- This writing format — e.g., `192.168.1.1` — is formally called **Dotted Decimal Notation**: numbers, separated by dots.

### 12.2 Prefix Notation / Network vs Host Bits (IPv4)

The instructor introduces the concept of **prefix notation** — a way of indicating how many bits of an IP address are reserved for identifying the **network** versus the **specific device (host)**.

- Example used: an address with a subnet mask of `255.255.255.0`, expressed in prefix notation as **`/24`**.
- **Interpretation:**
  - The **first 3 pairs (24 bits)** of the address are reserved to identify the **network** (this is where country/state/ISP/city-level information conceptually lives, as discussed in Part 2).
  - The **last pair (8 bits)** is reserved to identify the **specific device** on that network.
- **Why `/24`?** Because 24 of the IP address's 32 total bits are allocated to the network portion (3 pairs × 8 bits each = 24 bits), leaving the remaining 8 bits (1 pair) for the host/device.

> **Instructor's reassurance:** This is a foundational/simplified introduction to subnetting concepts — learners shouldn't worry if it's not fully clear yet, as it will be revisited in more depth later in the course.

### 12.3 IPv6 — Structure & Hexadecimal Notation

- **IPv6** addresses are **128 bits** in length (compared to IPv4's 32 bits).
- IPv6 addresses are written using **Hexadecimal Notation** (as opposed to IPv4's Dotted Decimal Notation) — using **colons** (`:`) to separate sections instead of dots.
- An IPv6 address is composed of **8 total pairs/groups** (segments).

### 12.4 IPv6 Zero-Compression Rule

- When an IPv6 address is displayed with **fewer than 8 visible groups** (e.g., only 5 shown), and a section shows **two colons together (`::`)**, this represents one or more **groups of zeros that have been compressed/hidden** for brevity.
- **Rule:** This zero-compression shorthand (`::`) is only used when there are **two or more consecutive all-zero groups** — a single zero group typically isn't compressed this way.
- **Example reasoning given:** If only 5 groups are visibly shown out of the expected 8, and a double-colon appears, the missing 3 groups are understood to be all-zero groups that have been compressed into that shorthand.

### 12.5 IPv6 Network vs Host Bits

- Applying the same network/host-bit-allocation concept as IPv4, but at IPv6's much larger scale:
  - The **first 4 pairs/groups** of an IPv6 address (which, at 16 bits per group, totals **64 bits**) are reserved for the **network** portion.
  - The **last 4 pairs/groups** (also 64 bits) are reserved for the **device/host** portion.
- This results in a **`/64`** prefix being common for IPv6 network identification (64 bits network + 64 bits host = 128 bits total).

**Total possible IPv6 addresses:** An extremely large number — described by the instructor as approximately **340 undecillion** (informally repeated as "340 trillion trillion trillion trillion" to convey its sheer scale) — effectively ensuring address exhaustion will not be a concern for the foreseeable future, even as global device/user counts continue to grow.

**Character set used in IPv6:** Combines:

- **Numbers 0–9**, and
- **Letters A–F** (hexadecimal digits)

> The instructor poses a check-understanding question: would a digit/letter outside the `0–9, A–F` range be valid in an IPv6 address? **Answer: No** — anything outside that hexadecimal character range falls outside the valid format and would be incorrect.

### 12.6 IPv4 vs IPv6 Comparison Table

| Attribute                           | IPv4                                          | IPv6                                                            |
| ----------------------------------- | --------------------------------------------- | --------------------------------------------------------------- |
| **Address size**                    | 32 bits                                       | 128 bits                                                        |
| **Notation style**                  | Dotted Decimal Notation (e.g., `192.168.1.1`) | Hexadecimal Notation, colon-separated (e.g., `2001:0db8::1`)    |
| **Number of groups/pairs**          | 4 (octets)                                    | 8 (groups)                                                      |
| **Character set**                   | Numbers 0–255 per octet                       | Hexadecimal: 0–9 and A–F                                        |
| **Common network/host split**       | `/24` example: 24 bits network / 8 bits host  | `/64` example: 64 bits network / 64 bits host                   |
| **Total possible addresses**        | ~4.2–4.7 billion                              | ~340 undecillion                                                |
| **Compression shorthand**           | None                                          | `::` represents two or more consecutive all-zero groups         |
| **Current status (per instructor)** | Primary version actively used today           | Still being rolled out/tested across devices; not yet universal |

> **Instructor's closing note on versions:** The course will primarily focus on **IPv4** going forward (since it remains the dominant version in practical/real-world use at the time of this recording), while IPv6 concepts will be revisited as needed.

---

## 13. Hands-On Practical: Accessing a Target System

> ⚠️ **Context & Scope Note:** This section documents a lab demonstration performed entirely within the instructor's **own isolated virtual machine environment**, explicitly for **educational purposes**, against a **deliberately vulnerable target machine** ("Metasploitable") designed for security training. The instructor explicitly states (see [Section 14](#14-ethical-disclaimer-instructors-own-words)) that this knowledge must **never** be used against systems without proper authorization. This is recorded here strictly as **course reference notes**, consistent with standard, widely-taught penetration testing training labs.

### 13.1 Lab Setup

- **Attacker machine:** Referred to as the "black machine" — running **Kali Linux** (a penetration-testing-focused Linux distribution; "Kali" is referenced by the instructor as "Kali"/"black machine").
- **Target machine:** Referred to as the "wheat machine" — a **Metasploitable** virtual machine (a purpose-built, intentionally vulnerable Linux VM used widely in security training for legal practice).
- **Why virtual machines are used:**
  - Running both attacker and target as **VMs** isolates the lab from the instructor's actual host system.
  - This means mistakes, system corruption, or heavy resource load during practice **do not affect the underlying physical/host machine**.
  - The instructor notes they will walk through **how to set up these virtual machines** in a future lesson.
- Before touching the target, the instructor first elevates privileges on the attacker machine to the **administrator/root account** (noting that systems typically have both a "guest" and "administrator" account, and root/administrator access is needed to run the necessary tools).

### 13.2 Step 1 — Host Discovery with Netdiscover

**Goal:** Identify the target system's IP address on the local network before attempting any further action.

**Tool used:** `netdiscover`

- Described as a tool that **scans and displays all IP addresses within a given network range**.
- The instructor opens a new terminal, confirms their own attacker machine's IP address first (for reference/comparison), then runs `netdiscover` to list live hosts on the local network range.
- Among the discovered addresses, the instructor identifies the most likely target IP by elimination (excluding their own machine's IP, the default gateway/router IP, and broadcast-type addresses), landing on the target Metasploitable machine's IP: **`192.168.66.144`** (referenced/typo'd at various points in the transcript as "192.168 66.4.144" — the consistent value used throughout the rest of the lab is **`.144`** as the final/relevant octet).

> **Instructor's note:** Identifying the correct IP among several candidates can be confusing for beginners at first, but becomes intuitive with practice — submask/gateway/broadcast address identification will be covered in more depth in upcoming lessons.

### 13.3 Step 2 — Service & OS Enumeration with Nmap

**Goal:** Once the target IP is identified, gather details about what services are running on it and what operating system it uses, before attempting access.

**Tool used:** `nmap` (Network Mapper) — described by the instructor as a very well-known scanning tool.

**Command structure described (conceptually, as narrated):**

```bash
nmap -v -sV -O 192.168.66.144
```

**Flags explained by the instructor:**
| Flag | Purpose (as explained) |
|---|---|
| `-v` ("Verbose"/"Hain") | Shows detailed output of what the tool is doing in the background, rather than just a final silent result. |
| `-sV` | Detects the **version of software/services** running on each open port. |
| `-O` | Attempts to detect/fingerprint the target's **Operating System**. |

> **Why OS detection matters (instructor's reasoning):** Without knowing the target's operating system, it becomes much harder to know how to properly approach gaining access — different operating systems require different techniques/tools.

**Result of the scan:**

- Nmap returned a list of **open ports** on the target, along with the **service/software version** running on each port.
- The OS fingerprinting result indicated the target was running a **Linux**-based operating system (with a specific version identified on-screen, though not transcribed in detail here).
- Based on experience, the instructor selected **port `5124`** as the port most likely to provide the fastest path to access (this is presented as an experience-based judgment call by the instructor, not a fixed rule).

### 13.4 Step 3 — Gaining Access with Netcat

**Tool used:** `netcat` (referred to in the transcript as "Net Cut" — a phonetic transcription of **Netcat**, a common networking utility used for reading/writing data across network connections, often nicknamed the "Swiss Army knife" of networking).

**Command structure described (conceptually, as narrated):**

```bash
nc 192.168.66.144 5124
```

- The instructor enters the target's IP address (`192.168.66.144`) and the chosen port (`5124`) into the Netcat command.
- Upon running it, the tool reports: **"session opened"** — indicating a connection was successfully established to that port on the target machine.

### 13.5 Step 4 — Verifying Access

To confirm whether real access to the target system had been achieved (versus simply seeing local output), the instructor performs several verification steps:

1. **`ls`** — Lists files. Initially, the instructor notes uncertainty: are these files from their own (attacker) system, or genuinely from the target?
2. **Check current working directory** — runs **`pwd`** ("Print Working Directory") on the now-connected session, observing the result indicated a location like `/home/admin` or similar on the **target** machine (not the attacker's own filesystem).
3. **Navigate to root directory** — uses **`cd /`** to move to the filesystem root, then lists contents (revealing standard Linux root-level directories: `bin`, `boot`, `dev`, `etc`, `home`, etc.) — confirming this is indeed a genuine **Linux filesystem belonging to the target**, distinct from the attacker's own system structure.
4. **Compare IP addresses** — runs **`ifconfig`** (or equivalent) on both the original attacker terminal and the new Netcat session, confirming that:
   - The attacker machine shows one IP (referenced as ending in a different octet),
   - While the now-connected session shows the **target's IP** (ending in `.144`) — confirming that the shell session obtained genuinely belongs to the **remote target system**, not the local attacker machine.

### 13.6 Step 5 — Proving Control (Reboot Test)

As a final, definitive proof of control over the remote system:

- The instructor runs the **`reboot`** command from within the established Netcat session (targeting the remote Metasploitable machine).
- The target machine is observed to **restart**, visibly confirming that the access obtained was genuine, remote, and had administrative-level control over the target system — and not merely a local/simulated result.

> **Instructor's conclusion:** "We can access a system sitting at another place and reboot it" — demonstrating end-to-end remote access from initial discovery (`netdiscover`) through enumeration (`nmap`) to actual remote command execution (`netcat` session + `reboot`).

---

## 14. Ethical Disclaimer (Instructor's Own Words)

The instructor explicitly states, immediately following the practical demonstration:

> "All these videos are for enhancing your knowledge and... so that you can help someone — you are not to use this for any wrong work." The target machine used ("Metasploitable") is explicitly described as a machine **built specifically for practical/training purposes** — and the instructor clarifies that **this does not mean any random system can be attacked this way**; this lab works specifically because Metasploitable is intentionally left vulnerable for learning purposes.

This reinforces the **White Hat / Ethical Hacking** framing established in Part 1: these techniques are taught strictly for **authorized, legal, educational, and defensive purposes**, never for unauthorized access to real-world systems.

---

## 15. Key Takeaways / Summary

1. There are **four types of IP addresses**, grouped in two independent classification pairs: **Public/Private** (scope-based) and **Static/Dynamic** (change-frequency-based).
2. **Private IPs** are used within a LAN (local, small-range); **Public IPs** are used for WAN/internet-facing communication, issued by your ISP.
3. **Public and Private IP address spaces cannot directly communicate with each other** — a translation mechanism (to be covered later — i.e., NAT) bridges them.
4. **Static/Dynamic** classification exists to solve the **IPv4 address exhaustion problem** (~4.2–4.7 billion total addresses vs. ~7+ billion global users) by allowing dynamic reuse of unused addresses.
5. **Static IPs** are used for servers/websites needing a consistent, fixed address (e.g., Facebook, Twitter). **Dynamic IPs** are used for everyday consumer devices, reassigned as needed.
6. **IPv4 and IPv6 are _versions_ of IP addressing, not "types"** — IPv4 is 32-bit/Dotted Decimal Notation; IPv6 is 128-bit/Hexadecimal Notation, vastly larger in address space (~340 undecillion addresses).
7. Both IPv4 and IPv6 addresses are split into **network bits** and **host/device bits** (e.g., IPv4 `/24` = 24 network bits + 8 host bits; IPv6 `/64` = 64 network bits + 64 host bits).
8. The first hands-on practical demonstrated a full, basic penetration testing workflow against an intentionally vulnerable lab VM (Metasploitable): **host discovery (netdiscover) → service/OS enumeration (nmap) → access (netcat) → verification (ls/pwd/cd/ifconfig) → proof of control (reboot)**.
9. All practical techniques shown are explicitly framed as **authorized, lab-only, ethical training exercises** — never to be used without explicit authorization on real-world systems.

---

## 16. Glossary

| Term                              | Definition                                                                                                                                      |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Private IP Address**            | An IP address used within a local network (LAN); not directly reachable from the public internet.                                               |
| **Public IP Address**             | An IP address used to identify a network's connection point to the internet (WAN); assigned by an ISP.                                          |
| **Static IP Address**             | A fixed IP address that does not change over time; commonly used by servers/websites.                                                           |
| **Dynamic IP Address**            | An IP address that can be reassigned/changed over time; commonly used by everyday consumer devices.                                             |
| **Dotted Decimal Notation**       | The standard way of writing IPv4 addresses, using four decimal numbers (0–255) separated by dots.                                               |
| **Prefix Notation (e.g., `/24`)** | A shorthand indicating how many bits of an IP address are allocated to the network portion versus the host/device portion.                      |
| **Hexadecimal Notation**          | The standard way of writing IPv6 addresses, using hexadecimal groups (0–9, A–F) separated by colons.                                            |
| **IPv4**                          | The 4th version of the Internet Protocol; uses 32-bit addresses.                                                                                |
| **IPv6**                          | The 6th version of the Internet Protocol; uses 128-bit addresses, designed to solve IPv4 address exhaustion.                                    |
| **Netdiscover**                   | A network scanning tool used to discover live hosts/IP addresses on a local network.                                                            |
| **Nmap (Network Mapper)**         | A widely used tool for scanning networks/hosts, detecting open ports, running services, and operating systems.                                  |
| **Netcat (`nc`)**                 | A versatile networking utility used to read/write data across network connections, often used to establish basic remote shell sessions.         |
| **Metasploitable**                | An intentionally vulnerable Linux virtual machine, purpose-built for legal penetration testing practice and security training.                  |
| **Port**                          | A logical endpoint/channel on a system through which network services communicate; identified by a numeric value (e.g., port 5124 in this lab). |
| **Kali Linux**                    | A Linux distribution specifically designed and equipped for penetration testing and security research.                                          |

---

## 17. Commands Reference (This Lesson)

| Command                      | Purpose                                                                                                                                                          |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `netdiscover`                | Scan and discover live hosts/IP addresses within the local network range.                                                                                        |
| `nmap -v -sV -O <target_ip>` | Scan a target IP with verbose output (`-v`), detect service versions (`-sV`), and attempt OS detection (`-O`).                                                   |
| `nc <target_ip> <port>`      | Use Netcat to attempt a connection to a specific IP and port on the target.                                                                                      |
| `ls`                         | List files/directories in the current location.                                                                                                                  |
| `pwd`                        | Print the current working directory — used to confirm which system/filesystem you're operating in.                                                               |
| `cd /`                       | Navigate to the root directory of the filesystem.                                                                                                                |
| `ifconfig`                   | Display network interface configuration, including the system's IP address — used to confirm whether you're viewing the attacker's or target's network identity. |
| `reboot`                     | Restart the system — used here as a definitive proof-of-access/control test on the remote target.                                                                |

> **Note:** Several of these commands and flags will be covered in greater depth (including all available Nmap flags/scan types, port fundamentals, and proper VM lab setup) in upcoming lessons, per the instructor.

---

## Notes for Next Lesson (Per Instructor)

- Deeper explanation of **how Public, Private, Static, and Dynamic IP addresses all work together** in practice.
- Full breakdown of **ports** — what they are, how many exist, and their role in services/communication.
- Proper **virtual machine (VM) lab setup** walkthrough (attacker + target environment).

---

_End of Part 3 transcription notes._
