# Firewalls, IDS, IPS & Honeypots — Complete Notes

## https://youtu.be/uwD5FMOGgps

**Course:** Ethical Hacking / Cyber Security
**Type:** Theory + Practical Lab
**Instructor reference:** New Version Hacker Channel

> **Disclaimer:** All techniques shown here are strictly for **educational and defensive purposes**. Bypassing or attacking security controls on any system without explicit written permission is illegal under **IT Act 2000, Section 66**.

---

## Table of Contents

1. [Firewall — Theory](#1-firewall--theory)
2. [IDS (Intrusion Detection System)](#2-ids-intrusion-detection-system)
3. [IPS (Intrusion Prevention System)](#3-ips-intrusion-prevention-system)
4. [IDS vs. IPS — Comparison](#4-ids-vs-ips--comparison)
5. [Honeypot — Theory](#5-honeypot--theory)
6. [Practical 1: Windows Built-in Firewall — Inbound & Outbound Rules](#6-practical-1-windows-built-in-firewall--inbound--outbound-rules)
7. [Practical 2: Third-Party Firewall (ZoneAlarm) — Blocking a Website](#7-practical-2-third-party-firewall-zonealarm--blocking-a-website)
8. [Practical 3: Honeypot Simulation (HoneyBOT)](#8-practical-3-honeypot-simulation-honeybot)
9. [Practical 4: Firewall Bypass Techniques](#9-practical-4-firewall-bypass-techniques)
10. [Key Takeaways](#10-key-takeaways)

---

## 1. Firewall — Theory

### 1.1 What Is a Firewall?

A firewall acts like a **physical wall** — just as a wall separates two spaces and protects against external elements, a firewall **separates your internal network from the outside world** and protects it from unauthorized access.

**Core function:** A firewall contains **filters and rules** that:

- Filter network traffic coming from outside.
- Filter IP addresses and block unauthorized access attempts.
- Allow only approved communication based on defined rules.

### 1.2 Types of Firewalls

| Type                  | Description                                                          | Used By                        |
| --------------------- | -------------------------------------------------------------------- | ------------------------------ |
| **Software Firewall** | Installed as a program on a device (e.g., Windows Defender Firewall) | Individual users, home systems |
| **Hardware Firewall** | A dedicated physical device placed at the network perimeter          | Organizations, enterprises     |

> Both software and hardware firewalls apply the same underlying principle — filtering traffic based on rules — but hardware firewalls handle much larger volumes of traffic and protect entire networks rather than single devices.

### 1.3 Inbound vs. Outbound Traffic

Understanding **inbound** and **outbound** is essential for configuring firewall rules correctly:

**Scenario:** A PC is connected to a router.

| Direction    | Definition                                     | Example                                                        |
| ------------ | ---------------------------------------------- | -------------------------------------------------------------- |
| **Outbound** | Traffic _sent_ from your device to another     | PC sends a packet to the router → **outbound** for the PC      |
| **Inbound**  | Traffic _received_ by your device from another | Router sends a reply packet to the PC → **inbound** for the PC |

> **Same packet, two perspectives:** A packet sent by PC A is **outbound** from PC A's perspective and **inbound** from the router's perspective. When the router replies, that reply is **outbound** from the router and **inbound** for PC A.

### 1.4 Inbound vs. Outbound Rules

Firewalls maintain **two separate rule sets**:

| Rule Type          | What It Controls                                                                               |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| **Inbound Rules**  | Traffic _entering_ your system — block unwanted incoming connections/packets                   |
| **Outbound Rules** | Traffic _leaving_ your system — block your system from communicating with certain destinations |

**Example rules you can set:**

- Block all **inbound** traffic from IP `192.168.1.90` → that IP cannot reach your system.
- Block all **outbound** traffic on **port 80** → your system cannot send HTTP requests to any destination.
- Allow only specific IPs to communicate with a specific port → granular access control.

### 1.5 How Firewall Rules Work (Overview)

1. Firewall maintains an **ordered list of rules**.
2. Every packet of traffic is **compared against each rule in order**.
3. The first matching rule is applied (allow or deny).
4. If no rule matches, a default policy applies (usually deny all).

---

## 2. IDS (Intrusion Detection System)

**Full form:** Intrusion Detection System

**What it does:** Monitors network traffic and system activity for **suspicious or dangerous patterns** and **alerts** the administrator when a threat is detected.

**What it does NOT do:** It does not take any action to stop or neutralize the threat — it only detects and reports.

**Analogy:** A **security guard at your door** who spots a fire in your house and shouts "Fire!" to warn you — but does not have a fire extinguisher and will not put it out himself.

**In practice:** IDS generates **alerts/notifications** when it detects anomalies, intrusion attempts, known attack signatures, or policy violations — leaving the response action to the human administrator or another system.

---

## 3. IPS (Intrusion Prevention System)

**Full form:** Intrusion Prevention System

**What it does:** Does everything IDS does (detects threats), AND **actively takes action to stop them** — blocking, dropping, or quarantining the malicious traffic or activity automatically without requiring human intervention.

**Analogy:** A **firefighter** assigned to your house — when they detect a fire, they don't just warn you; they immediately take out the extinguisher and put it out.

**In practice:** IPS can automatically:

- Block traffic from a suspicious IP address.
- Drop malicious packets.
- Reset a connection identified as an attack.
- Quarantine an affected endpoint.

---

## 4. IDS vs. IPS — Comparison

| Aspect                          | IDS                                      | IPS                                                             |
| ------------------------------- | ---------------------------------------- | --------------------------------------------------------------- |
| **Full form**                   | Intrusion Detection System               | Intrusion Prevention System                                     |
| **Primary function**            | Detect and alert                         | Detect, alert, AND prevent/block                                |
| **Action taken**                | Sends alert only — no automatic blocking | Actively blocks/drops/neutralizes threats                       |
| **Human intervention required** | Yes — admin must respond to alerts       | Minimal — system responds automatically                         |
| **Deployment**                  | Passive (monitoring mode)                | Inline (in the traffic path, can intercept)                     |
| **Analogy**                     | Security guard who warns of danger       | Firefighter who both warns and extinguishes                     |
| **False positive risk**         | Lower impact (only an unnecessary alert) | Higher impact (legitimate traffic could be incorrectly blocked) |

> **Combined use:** In practice, organizations often deploy IDS and IPS together — or use a combined **IDPS** (Intrusion Detection and Prevention System) — with IDS providing detailed logging/alerting and IPS providing automated blocking.

---

## 5. Honeypot — Theory

### 5.1 What Is a Honeypot?

A **honeypot** is a **decoy system or server** intentionally set up to **attract and mislead attackers** away from the real/production systems.

**Core principle:** Make the honeypot look exactly like the real target so that attackers engage with the decoy, believing it to be the actual server — while the real infrastructure remains untouched and secure.

### 5.2 How a Honeypot Works

```
Real Server (protected, critical)
         ↑
Hacker → tries to attack → is redirected/lured to Honeypot Server (decoy)
         ↓
Honeypot mimics the real server's appearance
Hacker believes they've reached the real target
Hacker's activity is monitored and logged on the honeypot
Real server remains untouched
```

### 5.3 Additional Purpose of Honeypots

Beyond simply diverting attackers, honeypots serve a **secondary intelligence-gathering function**:

- Every action an attacker takes on the honeypot is **logged and monitored**.
- This provides valuable intelligence about **attacker techniques, tools, and behavior**.
- Security teams can study these logs to improve defenses on the real infrastructure.
- The honeypot **alerts administrators** about unauthorized access attempts in real time.

> **Key characteristic:** A honeypot never reveals that it is a trap — the attacker is presented with a convincing login interface and all the apparent features of a real server, but is never actually given valid access or allowed to reach real data.

### 5.4 Types of Honeypots (Conceptual)

| Type                          | Description                                                                                           |
| ----------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Low-interaction honeypot**  | Simulates only a few services/responses; minimal risk; easier to set up                               |
| **High-interaction honeypot** | Fully functional system that allows deeper attacker engagement; riskier but gathers more intelligence |
| **Production honeypot**       | Deployed within an organization's real network to catch internal/external threats                     |
| **Research honeypot**         | Deployed to study attacker behavior and threat intelligence                                           |

---

## 6. Practical 1: Windows Built-in Firewall — Inbound & Outbound Rules

### 6.1 Accessing Windows Firewall

1. Press the **Windows key** and search for **"Firewall"**.
2. Open **"Windows Defender Firewall with Advanced Security"** (or "Firewall & Network Protection").
3. In the advanced interface, you will see:
   - **Inbound Rules** — rules controlling traffic entering your system.
   - **Outbound Rules** — rules controlling traffic leaving your system.

### 6.2 Creating a Custom Outbound Rule (Step-by-Step)

**Goal in demo:** Block outbound traffic on **port 80** (HTTP).

1. In the left panel, click **"Outbound Rules"**.
2. In the right panel, click **"New Rule..."**
3. **Rule Type:** Select **"Port"** → Next.
4. **Protocol and Ports:**
   - Protocol: **TCP**
   - Specific local ports: `80`
   - → Next
5. **Action:** Select **"Block the connection"** → Next.
6. **Profile:** Select which network profiles this rule applies to:
   - **Domain** (corporate networks)
   - **Private** (home/trusted networks)
   - **Public** (untrusted networks)
   - → Leave all checked or select as needed → Next.
7. **Name:** Give the rule a descriptive name (e.g., `Block_Port80_Outbound`).
   - Optionally add a description.
   - → Click **"Finish"**.

**Result:** The new rule appears in the Outbound Rules list. Any application/process on this system attempting to communicate over port 80 will now be blocked.

**Deleting a rule:**

- Right-click the rule in the list → **"Delete"** → Confirm.

### 6.3 Creating an Inbound Rule Blocking a Specific IP

**Goal:** Block all inbound connections from a specific IP address (e.g., an attacker's Kali Linux machine at `10.10.10.18`).

1. Go to **"Inbound Rules"** → **"New Rule..."**
2. **Rule Type:** Select **"Custom"** → Next.
3. **Program:** Leave as **"All Programs"** → Next.
4. **Protocol and Ports:** Leave as **"Any"** → Next.
5. **Scope:**
   - Under "Which remote IP addresses does this rule apply to?" → select **"These IP addresses"**
   - Click **"Add"** → enter the attacker's IP (e.g., `10.10.10.18`) → OK
   - → Next
6. **Action:** Select **"Block the connection"** → Next.
7. **Profile:** Leave all checked → Next.
8. **Name:** Give it a name (e.g., `Block_Kali`) → Finish.

**Result:** All inbound traffic from `10.10.10.18` is now blocked — any scan or connection attempt from that machine will be filtered out.

---

## 7. Practical 2: Third-Party Firewall (ZoneAlarm) — Blocking a Website

### 7.1 Tool Used

**ZoneAlarm** — a free third-party software firewall (open-source/downloadable).

### 7.2 Installation Steps

1. Download ZoneAlarm from its official website.
2. Run installer → Accept privacy/license agreement → Click **"Quick Install"**.
3. Optionally skip/add browser extension.
4. Complete installation → **Restart system if prompted** (required after uninstall/reinstall cycles).

### 7.3 Blocking a Website Using ZoneAlarm

**Goal in demo:** Block access to `www.govmovie.com` (treated as a malicious domain for demonstration).

1. Open ZoneAlarm interface.
2. Navigate to **Firewall** section.
3. Click **"View Zones"**.
4. Click **"Add"** → Select **"Host/Site"** (to block a domain name).
5. In the dialog that appears:
   - **Zone:** Change from "Trusted" to **"Blocked"** (or "Public" — essentially non-trusted).
   - **Host/Site name:** Enter `www.govmovie.com`
   - **Description:** e.g., `Block_website` (for your own reference)
6. Click **"Lookup"** → ZoneAlarm automatically detects and fills in the IP address of the domain.
7. Click **"OK"**.

**Result:** The domain now appears in the ZoneAlarm zone list as blocked.

**Verification:**

- Open a browser and attempt to visit `www.govmovie.com`.
- The browser displays: **"Internet access blocked"** — confirming the firewall rule is active.

### 7.4 Uninstalling ZoneAlarm

- Control Panel → Programs → Uninstall a Program → Select ZoneAlarm → Uninstall.
- A system restart may be required after uninstallation before reinstalling.

---

## 8. Practical 3: Honeypot Simulation (HoneyBOT)

### 8.1 Tool Used

**HoneyBOT** — a free Windows-based honeypot simulation tool that emulates open ports/services to attract and log attacker activity.

**Installed on:** Windows Server 2022 (the "protected" server in the demo).

### 8.2 HoneyBOT Configuration

After installation:

1. Launch HoneyBOT.
2. **Email Alerts tab:** Configure email notifications so that HoneyBOT sends you an alert whenever someone interacts with the honeypot.
3. **Export settings:** Configure whether to upload/store logs on a remote server.
4. **Log format:** Configure preferred log format.
5. **Updates:** Check for software updates.
6. Click **"Apply"** → **"OK"**.

> The system running HoneyBOT now acts as a honeypot — presenting fake open services (like Telnet on port 23, FTP on port 21, etc.) to any connecting attacker, while logging all their activity.

### 8.3 Demonstrating HoneyBOT Detection (Attack Simulation)

**From the attacker machine (Kali Linux), attempt to connect to the honeypot server:**

**Test 1 — Telnet connection attempt:**

```bash
telnet [honeypot_server_IP]
```

- HoneyBOT presents a fake login prompt.
- Even if the attacker enters correct-looking credentials, login always fails.
- The attacker cannot tell this is a honeypot — it feels like a real (but malfunctioning) server.

**Test 2 — FTP connection attempt:**

```bash
ftp [honeypot_server_IP]
```

- HoneyBOT presents a fake FTP login interface.
- Same behavior — credentials are rejected, attacker gets no access.

**On the HoneyBOT interface (victim server side), after each attempt:**

- An **alert appears immediately** showing:
  - The **port number** that was targeted (e.g., port 23 for Telnet, port 21 for FTP).
  - The **source IP address** of the attacker.
  - A **full conversation log** (right-click → "View Details") showing:
    - Each packet sent and received.
    - Login attempt details.
    - Connection sequence from initiation to failure.

### 8.4 HoneyBOT Key Behavior Summary

| Feature                 | Description                                                                              |
| ----------------------- | ---------------------------------------------------------------------------------------- |
| **Service emulation**   | Mimics Telnet, FTP, and other services — appears as a real server to attackers           |
| **Login deception**     | Always presents a login prompt but never grants access                                   |
| **Stealth**             | Attacker has no way to know it's a honeypot — it behaves like a real (but broken) server |
| **Logging**             | Logs every packet, every attempt, every conversation with full timestamps                |
| **Alerting**            | Generates real-time alerts for every connection attempt                                  |
| **Email notifications** | Can send email alerts to administrators when attack activity is detected                 |

---

## 9. Practical 4: Firewall Bypass Techniques

> **Purpose:** Understanding how firewalls can be bypassed helps security professionals design more robust defenses and helps ethical hackers test whether client firewalls are truly effective.

### 9.1 Setup

**Firewall rule created (as in Practical 1):**

- An inbound rule on Windows Server named `Kali` blocks **all traffic** from IP `10.10.10.18` (the Kali Linux attacker machine).

**Goal:** Demonstrate how standard scanning tools fail against this firewall, then show a bypass technique.

### 9.2 Standard Nmap Scans (Blocked by Firewall)

All of the following standard scans fail — results show "filtered" or no useful information:

```bash
# Standard SYN scan
nmap 10.10.10.11

# Explicit SYN scan
nmap -sS 10.10.10.11

# Intense/Aggressive scan
nmap -T4 -A 10.10.10.11

# Ping sweep
nmap -sP 10.10.10.0/24
```

**Results observed:**

- Ports shown as **"filtered"** — the firewall is dropping or not responding to the packets from the attacker's IP.
- Only **MAC addresses** were returned in the ping sweep — no port or service details.
- No OS or service version information was obtained.

**What "filtered" means:** The firewall is actively dropping these packets — the target doesn't respond, so Nmap can't determine if the port is open or closed.

### 9.3 Firewall Bypass — Decoy/Spoofed Source IP Scan (Idle/Zombie Scan Concept)

**Concept:** Instead of sending scan packets directly from the attacker's IP (which is blocked), the attacker **uses a third machine's IP address as the apparent source** of the scan packets. The firewall only blocks traffic from `10.10.10.18` — but if traffic appears to come from `10.10.10.22` (a Windows Server 2022 machine on the same network), it may be allowed through.

**Command used:**

```bash
nmap -sI 10.10.10.22 10.10.10.11
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-sI` | **Idle scan** (also called Zombie scan) — uses a third machine (`10.10.10.22`) as a **"zombie"** to send/receive probe packets on the attacker's behalf |
| `10.10.10.22` | The **zombie/intermediate machine** whose IP is used as the apparent packet source |
| `10.10.10.11` | The **actual target** being scanned |

**How the idle/zombie scan works:**

1. The attacker probes the zombie machine's IP ID sequence to use it as a traffic channel.
2. Scan packets appear to originate from the zombie machine's IP (`10.10.10.22`), not the attacker's IP (`10.10.10.18`).
3. The firewall allows traffic from `10.10.10.22` (not blocked) — so the scan packets reach the target.
4. Target responses go back to the zombie, and the attacker infers which ports are open based on changes in the zombie's IP ID counter.

**Result in demo:** After switching on the intermediate machine (Windows Server 2022), the scan returned **open ports** — including **port 80 (HTTP)** — confirming the firewall bypass was successful.

> **Key learning:** The firewall blocked `10.10.10.18` but not `10.10.10.22`. By routing the scan through `10.10.10.22`, the attacker effectively circumvented the firewall rule.

### 9.4 Other Firewall Bypass Techniques (Mentioned, Not Demonstrated)

The instructor notes there are many more bypass techniques covered in advanced modules, including:

- **Fragmentation attacks** — breaking packets into small fragments that individually don't match firewall signatures.
- **Tunneling** — encapsulating blocked protocols inside allowed ones (e.g., DNS tunneling, ICMP tunneling).
- **Using allowed ports** — communicating on ports that the firewall permits (e.g., port 443/HTTPS).
- **Source port manipulation** — setting source ports to common trusted values (e.g., 53/DNS, 80/HTTP).
- **VPN/proxy usage** — routing traffic through an allowed intermediary.

### 9.5 Defensive Implications

| Attack Technique                  | Defensive Counter                                                                                                                                                                                                   |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Zombie/Idle scan (spoofed source) | Block traffic from **unexpected sources**; use **stateful firewall inspection** (tracks connection state, not just IP); implement **egress filtering** to prevent your internal machines from being used as zombies |
| Fragmentation attacks             | Enable **packet reassembly** in the firewall before inspection                                                                                                                                                      |
| Port-based bypass                 | Use **deep packet inspection (DPI)** — inspect packet content, not just port numbers                                                                                                                                |
| Tunneling                         | Deploy **application-layer inspection** tools; monitor for abnormal protocol behavior                                                                                                                               |

---

## 10. Key Takeaways

1. **A firewall is the first line of defense** — it filters traffic based on defined rules (inbound and outbound), controlling who can communicate with your system and on which ports/protocols.

2. **Inbound vs. outbound rules are distinct** — always understand the traffic _direction_ before creating a rule. Blocking inbound from an IP prevents that IP from reaching you; blocking outbound on a port prevents your system from initiating connections on that port.

3. **IDS detects, IPS prevents** — IDS is the security guard who warns; IPS is the firefighter who acts. IDS generates alerts; IPS automatically blocks/drops threats. In practice, modern IDPS systems combine both functions.

4. **Honeypots deceive, monitor, and gather intelligence** — by luring attackers to a decoy system, honeypots protect real infrastructure AND provide detailed logs of attacker techniques — invaluable for improving defenses and for forensic analysis.

5. **Standard firewall rules can be bypassed** — a firewall that blocks traffic from a specific IP can be circumvented if the attacker routes their traffic through a third machine whose IP is not blocked (idle/zombie scan). This demonstrates why **stateful inspection** and **network-level monitoring** are more robust than simple IP-based blocking alone.

6. **Layered security is essential** — no single control is sufficient. Effective security combines: Firewall + IDS/IPS + Honeypots + WAF + strong authentication + regular audits — this is called **Defense in Depth**.

7. **Third-party firewalls add flexibility** — tools like ZoneAlarm extend firewall capabilities with user-friendly interfaces for blocking specific websites/domains by name, complementing the built-in OS firewall which operates primarily at the network/port level.

8. **Honeypots are both a trap and a forensic tool** — the detailed logs HoneyBOT generates (source IP, port, full packet conversation, timestamps) are directly usable as digital forensic evidence of unauthorized access attempts, linking the honeypot security concept directly to digital forensics investigation.

---

_These notes complement the Web Server Security and Web Application Security modules. Firewalls, IDS/IPS, and honeypots represent the active defensive layer that security teams deploy on and around the servers and applications covered in those modules._
