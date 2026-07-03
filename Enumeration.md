# Cybersecurity Course : Enumeration — Concept, Types & Practical (NetBIOS & SNMP)

> Source: "NewVersionHacker"
> Topics covered: What is Enumeration, where it fits in the ethical hacking methodology, what is extracted during enumeration, enumeration types/services (NetBIOS, SNMP, NTP, NFS, LDAP), practical NetBIOS enumeration with Nmap, practical SNMP enumeration with Nmap.

> ⚠️ **Ethical Disclaimer (from instructor):** This content is strictly for educational purposes and security awareness. Do not misuse any of the techniques shown. The channel does not promote illegal or malicious activities. Always perform these activities only on systems you own or have explicit authorization to test.

---

## Table of Contents

1. [What is Enumeration?](#1-what-is-enumeration)
2. [Where Enumeration Fits in the Ethical Hacking Process](#2-where-enumeration-fits-in-the-ethical-hacking-process)
3. [What is Extracted During Enumeration](#3-what-is-extracted-during-enumeration)
4. [Types of Enumeration (By Service/Protocol)](#4-types-of-enumeration-by-serviceprotocol)
   - [4.1 NetBIOS Enumeration](#41-netbios-enumeration)
   - [4.2 SNMP Enumeration](#42-snmp-enumeration)
   - [4.3 NFS Enumeration](#43-nfs-enumeration)
   - [4.4 NTP Enumeration](#44-ntp-enumeration)
   - [4.5 LDAP Enumeration](#45-ldap-enumeration)
5. [Practical 1 — NetBIOS Enumeration with Nmap](#5-practical-1--netbios-enumeration-with-nmap)
6. [Practical 2 — SNMP Enumeration with Nmap](#6-practical-2--snmp-enumeration-with-nmap)
7. [Key Takeaways / Summary](#7-key-takeaways--summary)
8. [Glossary](#8-glossary)
9. [Commands Reference](#9-commands-reference)

---

## 1. What is Enumeration?

**Definition:** Enumeration (also written as "Namre" in the transcript — a phonetic rendering by the instructor) is an **advanced-level information-gathering process** that extracts detailed, structured information from a target system or network — going deeper than standard network scanning.

**Simple distinction between Network Scanning and Enumeration:**

| Stage                | What It Does                                                                                                                                                                     |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Network Scanning** | Gets a broad idea of what's running on the target — which ports are open, what OS is running, what services are present. It's an **overview**.                                   |
| **Enumeration**      | Goes deeper — extracts **specific, structured, actionable data** from the target: user account names, machine names, shared resources, running services, network configurations. |

> **Instructor's analogy:** In network scanning you're "putting an idea" — getting a general picture. In enumeration you're extracting detailed technical intelligence from that picture, preparing for a direct attack.

---

## 2. Where Enumeration Fits in the Ethical Hacking Process

Enumeration is the **third major step** in the ethical hacking methodology:

```
Step 1 → Information Gathering (Reconnaissance)
           Passive/active research about the target (OSINT, WHOIS, DNS lookups, etc.)

Step 2 → Network Scanning
           Port scanning, service detection, OS fingerprinting (covered in previous parts)

Step 3 → Enumeration ← (This lesson)
           Extract deep system/network details: usernames, machine names, shares, services

Step 4 → Direct Attack / Exploitation
           Use the gathered intelligence to gain unauthorized access (next lesson)
```

> **Important note from instructor:** Enumeration is considered to be **very close to the attack phase** — sometimes while performing enumeration on a target system, the act of probing can itself trigger a compromise or reveal exploitable vulnerabilities. For this reason, it is treated almost as a **post-attack procedure** in some models — meaning by the time enumeration is complete, the system may effectively already be compromised.

---

## 3. What is Extracted During Enumeration

Enumeration targets specific categories of information:

| Information Extracted         | Description                                                                                                                                    |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Usernames / User Accounts** | All user accounts that exist on the target system or server — who can log in and with what names.                                              |
| **Machine Name**              | The network name of the target machine (similar to OS grabbing for the operating system — this identifies the device by its network hostname). |
| **Network Resources**         | What network resources the target system is connected to or using — how it connects to the internet, what services it relies on.               |
| **Shares**                    | What files, folders, or resources are being shared by the target system across the network — active file-sharing information.                  |
| **Running Services**          | What services are actively running on the target — gives insight into exploitable attack surfaces and points of weakness.                      |

**Why all this matters:** By combining this extracted data, an attacker or penetration tester can:

- Identify specific user accounts to target (for credential attacks).
- Understand what services are vulnerable.
- Find network shares that may be misconfigured or accessible without authentication.
- Plan the most effective exploitation approach for the specific system.

---

## 4. Types of Enumeration (By Service/Protocol)

Enumeration is organized by the specific **network service or protocol** being probed. Different services expose different categories of information.

### 4.1 NetBIOS Enumeration

**Full form:** NetBIOS = Network Basic Input/Output System

**Port:** 137 (UDP), 138 (UDP), 139 (TCP)

**What it does:**

- NetBIOS is a Windows networking protocol used for **file and printer sharing** across local networks.
- NetBIOS enumeration extracts:
  - The **NetBIOS name** of the target machine (its network identity).
  - All **user accounts** registered on the system.
  - Active **flags** (indicators of what network roles the system is serving — e.g., workstation, domain controller, file server).
  - **Group memberships** of the machine on the network.
  - Shared resources available over NetBIOS.

**Why it's important:** NetBIOS is commonly enabled on Windows machines by default. A system with NetBIOS active and improperly secured can expose all user account names and shared resources without requiring authentication — giving an attacker a detailed map of the system's users and file structure.

---

### 4.2 SNMP Enumeration

**Full form:** SNMP = Simple Network Management Protocol

**Port:** 161 (UDP)

**What it does:**

- SNMP is used to **manage and monitor network devices** (routers, switches, servers, printers) across a network from a central point.
- In a large network (e.g., a corporate office with many connected systems), SNMP allows an administrator to manage all devices centrally — setting permissions, monitoring usage, checking status — without having to inspect each device individually.
- SNMP enumeration can extract:
  - A complete list of **all running processes** on the network/system.
  - **Network interface details** (IP addresses, MAC addresses).
  - **Installed software** and version information.
  - **System uptime, hostname, and contact details**.
  - **Routing tables** and connected devices.
  - **User accounts** accessible via SNMP.

**Why it's dangerous:** SNMP v1 and v2c use a simple "community string" (essentially a password) for authentication — often left at the default value of `public` or `private`. If SNMP is accessible with default credentials, an attacker can extract the **entire directory of all processes and configurations** running across the network.

---

### 4.3 NFS Enumeration

**Full form:** NFS = Network File System (instructor refers to it as "New Technology File System" — the widely used term is Network File System)

**Port:** 2049 (TCP/UDP)

**What it does:**

- NFS allows users to **remotely access files** on another system over a network — as if the remote files were stored locally.
- This is the underlying technology behind many cloud storage and remote work file access systems.
- NFS enumeration extracts:
  - What **file systems and directories** are exported/shared by the target.
  - Who has **read/write permissions** on those shares.
  - Available mount points.

**Why it's dangerous:** If NFS exports are misconfigured (e.g., accessible to everyone on the network, or to any IP), an attacker can:

- **Read** all files in the exported directories (including sensitive configs, credentials).
- **Write** to the filesystem if write permission is granted — potentially planting backdoors or modifying system files.
- Access the file system at a **very deep level** — the instructor notes NFS can provide "deep level access."

> **Note from instructor:** NFS enumeration practical could not be included in this video due to platform/community guidelines. It follows the same Nmap-based approach as NetBIOS and SNMP.

---

### 4.4 NTP Enumeration

**Full form:** NTP = Network Time Protocol

**Port:** 123 (UDP)

**What it does:**

- NTP manages and synchronizes **time** across all devices on a network.
- It's responsible for the automatic time updates you see on your devices — you rarely need to set the time manually because NTP updates it automatically.
- NTP operates at very high precision — synchronizing time to within milliseconds across the internet.
- NTP enumeration can extract:
  - A list of **hosts connected to the NTP server**.
  - **System time and timezone** information.
  - Network structure details (what devices are synchronized together).

**Why it's sensitive:** Time synchronization is fundamental to many security mechanisms (authentication tokens, SSL certificates, Kerberos authentication, log timestamps). Access to NTP data can reveal network topology and connected devices. Manipulation of NTP can potentially undermine time-based security controls.

> **Note from instructor:** NTP enumeration practical was not included in this video due to the sensitivity of the protocol. It is covered in the advanced/paid course.

---

### 4.5 LDAP Enumeration

**Full form:** LDAP = Lightweight Directory Access Protocol

**Port:** 389 (TCP/UDP), 636 (LDAPS — secure)

**What it does:**

- LDAP is used to **manage directory information** — structured repositories of information about users, computers, and resources in a network.
- In corporate environments, LDAP (often implemented as Microsoft Active Directory) is used to manage:
  - All user accounts and their attributes.
  - Group memberships.
  - Organizational units (departments, teams).
  - Computer accounts.
  - Access control policies.
- LDAP enumeration can extract:
  - All **user account names** in the directory.
  - **Group memberships** and organizational structure.
  - **Email addresses, phone numbers, job titles** if stored.
  - **Computer accounts** in the domain.

**Why it's powerful:** LDAP access essentially gives you a **complete organizational map** of the target — every user, every computer, every group, and the relationships between them. The instructor calls it "very vulnerable" and "very powerful" if access is obtained — stating that gaining LDAP access is considered an extremely significant compromise.

> **Note from instructor:** LDAP enumeration was not demonstrated in this video due to copyright/community guideline concerns. Covered in the advanced course.

---

## 5. Practical 1 — NetBIOS Enumeration with Nmap

**Tool:** Nmap (pre-installed on Kali Linux and Parrot OS)
**Target:** Lab Windows machine (instructor's virtual lab environment)

### Command Used

```bash
nmap -sV -sC --script nbstat.nse <target_ip>
```

### Flag Breakdown

| Flag / Option         | Meaning                                                                                                                                               |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap`                | The network scanning tool.                                                                                                                            |
| `-sV`                 | Service Version detection — identifies what software/version is running on each open port.                                                            |
| `-sC`                 | Script scan — runs Nmap's default scripts against the target.                                                                                         |
| `--script nbstat.nse` | Runs the **NetBIOS Status script** specifically — extracts NetBIOS-specific information (name, flags, MAC address, user accounts, group memberships). |
| `<target_ip>`         | The IP address of the target system.                                                                                                                  |

### What the Script Does

The `nbstat.nse` script (NSE = Nmap Scripting Engine) sends NetBIOS queries to the target and parses the responses to extract structured information.

### Output / Results Explained

A successful NetBIOS enumeration scan returns:

```
Host script results:
| nbstat:
|   NetBIOS name: MACHINENAME, NetBIOS user: <unknown>, NetBIOS MAC: xx:xx:xx:xx:xx:xx
|   Names:
|     MACHINENAME      <00>  Flags: <unique><active>
|     WORKGROUP        <00>  Flags: <group><active>
|     MACHINENAME      <20>  Flags: <unique><active>
|_    WORKGROUP        <1e>  Flags: <group><active>
```

**Reading the output:**

| Field                 | Meaning                                                                               |
| --------------------- | ------------------------------------------------------------------------------------- |
| **NetBIOS name**      | The network hostname of the target machine.                                           |
| **NetBIOS MAC**       | The MAC address of the target's network adapter (blurred by instructor for security). |
| **Names section**     | Lists all NetBIOS names registered by the target on the network.                      |
| **`<00>`**            | Workstation service (computer itself on the network).                                 |
| **`<20>`**            | File Server service (target is sharing files).                                        |
| **`<1e>`**            | Browser service (network browsing).                                                   |
| **Flags: `<active>`** | The service/role is currently running and active on the target.                       |
| **Flags: `<unique>`** | Only one device on the network has this name.                                         |
| **Flags: `<group>`**  | This name is shared by all devices in the workgroup/domain.                           |

**What this tells us:**

- The target machine's exact **network name** (useful for further targeting).
- Whether the target is a **file server** (flag `<20>` active = file sharing enabled).
- Whether it belongs to a **workgroup or domain** (group flag shows network membership).
- The **MAC address** — which can reveal the device manufacturer and aid in identification.
- **Active services** — every active flag is a potential entry point for further exploitation.

> **Instructor's note:** These active flags are significant — they show which network roles the target is actively serving, and each active service is a surface that can be probed for vulnerabilities.

---

## 6. Practical 2 — SNMP Enumeration with Nmap

**Tool:** Nmap with SNMP script
**Target:** Lab network machine
**Port:** 161 (UDP — important: SNMP runs on UDP, not TCP)

### Command Used

```bash
nmap -sU -p 161 --script snmp-processes <target_ip>
```

### Flag Breakdown

| Flag / Option             | Meaning                                                                                                                                 |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `nmap`                    | The network scanning tool.                                                                                                              |
| `-sU`                     | **UDP scan** — must be specified because SNMP runs on UDP port 161. Without this, Nmap defaults to TCP and will not find the SNMP port. |
| `-p 161`                  | Target port 161 (SNMP's standard UDP port).                                                                                             |
| `--script snmp-processes` | Runs the SNMP processes enumeration script — retrieves the full list of running processes on the target via SNMP.                       |
| `<target_ip>`             | IP address of the target.                                                                                                               |

> **Common mistake flagged by instructor:** A command error occurred in the live demo (likely a typo in the script name or missing flag). The instructor notes that even small command errors will prevent the scan from running — spelling, spacing, and flag syntax must be exactly correct.

### Additional Useful SNMP Nmap Scripts

```bash
--script snmp-info          # System information (hostname, OS, uptime, contact)
--script snmp-interfaces    # Network interface details (IP, MAC, speed)
--script snmp-netstat       # Active network connections
--script snmp-sysdescr      # System description string
--script snmp-brute         # Brute-force SNMP community strings
```

### Output / Results

A successful SNMP enumeration returns a **complete process list** from the target — every program running on the system at the time of the scan, including:

- System processes (OS-level services).
- Application processes (web servers, database servers, etc.).
- Background services.
- PID (Process ID) and process name for each.

**What this tells us:**

- **Every running service** on the target — identifying potential vulnerabilities in outdated or misconfigured applications.
- **System software** — the specific applications and their versions.
- **Network management information** — how the target is managing its network resources.
- This data directly feeds into the exploitation phase — knowing what's running tells you what to attack and with which exploits.

> **Instructor's observation:** The SNMP scan returned "the entire directory of all processes running on the network" — making it an extremely information-rich enumeration technique when SNMP is accessible.

---

## 7. Key Takeaways / Summary

1. **Enumeration** is the third step in the ethical hacking process — it goes **deeper than network scanning**, extracting specific, structured intelligence: usernames, machine names, shares, services, and network resources.
2. Enumeration is positioned between **network scanning** and **direct exploitation** — and is so invasive that it sometimes triggers a compromise even during the enumeration process itself.
3. The five main enumeration services covered are: **NetBIOS** (file/printer sharing, user accounts), **SNMP** (network management, full process list), **NFS** (remote file system access), **NTP** (time protocol, network topology), and **LDAP** (directory services, full organizational map).
4. **NetBIOS enumeration** reveals the target's network name, active service flags, MAC address, workgroup membership, and file-sharing status — using `nmap --script nbstat.nse`.
5. **SNMP enumeration** must be done with a **UDP scan** (`-sU`) on port **161** — it can return the complete list of all processes and network configurations on the target.
6. **SNMP is particularly dangerous** when left at default community strings (`public`/`private`) — it can expose an entire network's management information without authentication.
7. **LDAP**, when accessible, is considered an extremely high-value target — it reveals the complete organizational structure, all user accounts, and all computer accounts in a network directory.
8. Multiple tools exist for each enumeration type — Nmap with its NSE (Nmap Scripting Engine) scripts is demonstrated here as a reliable, widely available option for beginners.

---

## 8. Glossary

| Term                            | Definition                                                                                                                                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Enumeration**                 | An advanced information-gathering phase in ethical hacking that extracts specific, structured data from target systems — usernames, machine names, shares, services, and network resources. |
| **NetBIOS**                     | Network Basic Input/Output System — a Windows networking protocol for file and printer sharing; enumeration reveals machine names, user accounts, and active services.                      |
| **SNMP**                        | Simple Network Management Protocol — used to manage and monitor network devices centrally; enumeration can extract the entire list of running processes and network configurations.         |
| **NFS**                         | Network File System — allows remote access to files over a network; enumeration reveals exported directories and their access permissions.                                                  |
| **NTP**                         | Network Time Protocol — synchronizes time across all networked devices; enumeration reveals connected hosts and network topology.                                                           |
| **LDAP**                        | Lightweight Directory Access Protocol — manages directory information (users, computers, groups) in corporate networks; enumeration reveals the complete organizational structure.          |
| **NSE (Nmap Scripting Engine)** | A scripting framework built into Nmap that allows running specialized scripts against targets for advanced information gathering, vulnerability detection, and exploitation.                |
| **NBStat**                      | NetBIOS Statistics — a command/protocol that returns the NetBIOS name table of a remote machine; the `nbstat.nse` Nmap script automates this.                                               |
| **Community String (SNMP)**     | A simple password used to authenticate SNMP requests — commonly left at the insecure defaults of `public` (read) or `private` (write).                                                      |
| **Active Flag (NetBIOS)**       | An indicator in NetBIOS enumeration output showing that a particular network service/role is currently running on the target system.                                                        |
| **File Server Flag `<20>`**     | A specific NetBIOS flag indicating the target machine has file-sharing services enabled and active.                                                                                         |
| **UDP Scan (`-sU`)**            | An Nmap scan type specifically for probing UDP ports — required for services like SNMP (port 161) and NTP (port 123) that use UDP instead of TCP.                                           |
| **Process List**                | The complete inventory of all programs/services currently running on a system — extractable via SNMP enumeration.                                                                           |

---

## 9. Commands Reference

```bash
# ─── NETBIOS ENUMERATION ──────────────────────────────────────────────────────

# Basic NetBIOS enumeration with service version detection and NSE script
nmap -sV -sC --script nbstat.nse <target_ip>

# ─── SNMP ENUMERATION ────────────────────────────────────────────────────────

# SNMP process enumeration (UDP scan on port 161)
nmap -sU -p 161 --script snmp-processes <target_ip>

# SNMP system information
nmap -sU -p 161 --script snmp-info <target_ip>

# SNMP network interface details
nmap -sU -p 161 --script snmp-interfaces <target_ip>

# SNMP active network connections
nmap -sU -p 161 --script snmp-netstat <target_ip>

# Multiple SNMP scripts combined
nmap -sU -p 161 --script snmp-info,snmp-processes,snmp-interfaces <target_ip>

# ─── GENERAL NOTES ───────────────────────────────────────────────────────────

# SNMP MUST use -sU (UDP scan) — default TCP scan will not find port 161
# NetBIOS runs on:
#   Port 137 UDP (name service)
#   Port 138 UDP (datagram service)
#   Port 139 TCP (session service)
# SNMP runs on:
#   Port 161 UDP (queries)
#   Port 162 UDP (traps/notifications)
# NTP runs on:
#   Port 123 UDP
# NFS runs on:
#   Port 2049 TCP/UDP
# LDAP runs on:
#   Port 389 TCP/UDP
#   Port 636 TCP/UDP (LDAPS — secure)
```

---

## Notes for Next Lesson (Per Instructor)

- **Direct Exploitation** — using the information gathered through network scanning and enumeration to perform actual attacks on the target system.
- Advanced enumeration techniques (NTP, LDAP, additional tools) — covered in the paid/advanced course due to platform guidelines.

---

_End of Part 11 transcription notes._
