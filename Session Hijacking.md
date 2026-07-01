# Session Hijacking — Complete Notes

**Course:** Ethical Hacking / Cyber Security
**Type:** Theory + Practical Lab
**Instructor reference:** New Version Hacker

> **Disclaimer:** All techniques covered here are strictly for **educational and defensive purposes**. Performing session hijacking or any unauthorized access to systems/accounts is illegal under **IT Act 2000, Section 66** (up to 3 years imprisonment and/or ₹5 lakh fine). Always operate only on systems you own or have explicit written authorization to test.

---

## Table of Contents

1. [What Is a Session?](#1-what-is-a-session)
2. [What Is Session Hijacking?](#2-what-is-session-hijacking)
3. [Why Session IDs Are So Dangerous](#3-why-session-ids-are-so-dangerous)
4. [How Session IDs Look — Structure & Predictability](#4-how-session-ids-look--structure--predictability)
5. [Session Hijacking Methods — Overview](#5-session-hijacking-methods--overview)
6. [Application-Layer Session Attacks](#6-application-layer-session-attacks)
7. [Network-Layer Session Attacks](#7-network-layer-session-attacks)
8. [Practical 1: Viewing & Reusing a Session ID (Browser Demo)](#8-practical-1-viewing--reusing-a-session-id-browser-demo)
9. [Practical 2: Session Hijacking via Proxy (ZAP/OWASP Proxy)](#9-practical-2-session-hijacking-via-proxy-zapowasp-proxy)
10. [Practical 3: Man-in-the-Middle + HTTP Traffic Capture (BetterCap)](#10-practical-3-man-in-the-middle--http-traffic-capture-bettercap)
11. [Detection: Identifying a Man-in-the-Middle Attack (Wireshark)](#11-detection-identifying-a-man-in-the-middle-attack-wireshark)
12. [Prevention & Counter Measures](#12-prevention--counter-measures)
13. [Key Takeaways](#13-key-takeaways)

---

## 1. What Is a Session?

### 1.1 Definition

A **session** is a **temporary period of interaction** between a user (client) and a web server, maintained through a **session ID** (also called a session token) after the user has authenticated (logged in).

### 1.2 Why Sessions Exist

Consider what would happen without sessions on a website like social media:

- You log in (enter your username/password).
- You like a post → you'd have to re-enter your password.
- You comment on something → re-enter your password again.
- You browse to another page → re-enter your password again.

This would be an unusable experience. **Sessions solve this** — after successful login, the server issues a session ID that acts as a temporary identity badge, so the user doesn't need to re-authenticate for every action during that session.

### 1.3 Session Lifecycle

```
User enters username + password
            ↓
Server authenticates the credentials
            ↓
Server issues a SESSION ID (session token) to the user
            ↓
User holds this session ID for the duration of their visit
            ↓
Every request user makes includes this session ID
            ↓
Server recognizes the session ID and allows actions without re-authentication
            ↓
User logs out → session ID is invalidated → next login requires credentials again
```

### 1.4 Key Facts About Sessions

| Fact            | Description                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------- |
| **Issued by**   | The **web server** — the server creates and assigns the session ID                                |
| **Received by** | The **client** (the user/browser)                                                                 |
| **When given**  | After **successful authentication** (username + password verified)                                |
| **Duration**    | Valid until the user logs out, the session expires, or the server invalidates it                  |
| **Stored on**   | Typically in the browser's **cookies** (viewable via browser developer tools → Storage → Cookies) |

---

## 2. What Is Session Hijacking?

**Session hijacking** (also called **session hacking**) is the exploitation of a valid session ID to **gain unauthorized access to a user's account** — without needing their username or password.

**Core principle:** Once an attacker obtains a legitimate user's session ID, they can **impersonate that user** for all purposes — the server sees the session ID and believes it is communicating with the authorized user.

**Three defining characteristics:**

1. **Identification** — the session ID is the token/key being targeted (it's the user's "identity" for that session).
2. **Provided by server** — the session ID was legitimately issued by the web server to the real user.
3. **Post-authentication** — the session ID only exists after the user has already successfully logged in.

---

## 3. Why Session IDs Are So Dangerous

The session ID is a **single point of failure** for account security:

- Most modern logins include protections like **OTP, 2FA, captcha, or hardware tokens**.
- But all of these protections apply **at login time** — once the user is authenticated and the session ID is issued, the session ID bypasses all of them.
- If an attacker steals the session ID, they don't need to know the password, crack the OTP, or bypass 2FA — **they simply present the stolen session ID and the server lets them in**.

> **Analogy:** Your password is like the lock on a door. Your session ID is like a key that was already made after you unlocked the door. Stealing the key is easier than picking the lock.

---

## 4. How Session IDs Look — Structure & Predictability

### 4.1 Example Session ID Format

A session ID in a URL might look like:

```
https://example.com/profile?session=a7b3f182202417215022abc124def
```

Session IDs are also commonly stored in browser **cookies** — visible in:

> Browser → F12 (Developer Tools) → Application / Storage → Cookies

### 4.2 The Predictability Problem

**Some poorly implemented servers generate session IDs based on predictable patterns** — such as combining:

- Current **date** (e.g., `20240217` = 17 Feb 2024)
- Current **server time** (e.g., `214022` = 21:40:22)
- Fixed/constant prefix or suffix values

**Example of a weak, predictable session ID:**

```
[CONSTANT_PREFIX][DATE][TIME][CONSTANT_SUFFIX]
a7b3f1-20240217-214022-abc124
```

**How an attacker exploits this:**

1. Attacker logs into the website multiple times.
2. They record the session IDs they receive each time and observe the pattern.
3. They identify which parts are constant (prefix, suffix) and which parts change (date, time).
4. They estimate **when a target user likely logged in** (based on information gathering).
5. They **reconstruct the session ID** by substituting the estimated date/time into the pattern.
6. They use the predicted session ID to gain access without ever needing the real user's credentials.

### 4.3 Secure vs. Insecure Session ID Generation

| Approach                                | Security Level                       | Notes                                                                         |
| --------------------------------------- | ------------------------------------ | ----------------------------------------------------------------------------- |
| **Date + Time based**                   | ❌ Very weak                         | Predictable — attacker can reconstruct it                                     |
| **Sequential numbers**                  | ❌ Weak                              | Trivially guessable by incrementing                                           |
| **Username + timestamp hash**           | ⚠️ Moderate                          | Partially predictable if the hash algorithm is known                          |
| **Cryptographically random + long**     | ✅ Strong                            | CSPRNG-generated, sufficiently long (e.g., 128-bit+), not time/date dependent |
| **Encrypted session token (JWT, etc.)** | ✅ Strong when correctly implemented | Must use strong algorithms and proper signing                                 |

---

## 5. Session Hijacking Methods — Overview

| Method                                | Brief Description                                                                            |
| ------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Stealing**                          | Directly intercepting/capturing the session ID from network traffic                          |
| **Guessing / Predicting**             | Analyzing session ID patterns to generate a valid one                                        |
| **Brute Forcing**                     | Systematically trying many session ID values until one works                                 |
| **Sniffing**                          | Passively capturing network packets to extract session IDs                                   |
| **Man-in-the-Middle (MitM)**          | Positioning attacker between user and server to intercept all traffic                        |
| **Cross-Site Scripting (XSS)**        | Injecting a script into the victim's browser that sends their session cookie to the attacker |
| **Cross-Site Request Forgery (CSRF)** | Tricking the authenticated user into making an unintended request (one-click attack)         |
| **Session Replay**                    | Capturing a valid session ID and reusing it later                                            |

---

## 6. Application-Layer Session Attacks

These attacks occur at **Layer 7 (Application Layer)** of the OSI model — the layer where user data is not encrypted in transit (unless HTTPS is properly configured). At this layer, data is fully decrypted and readable.

| Attack                                | Description                                                                                                                                                                                                                  |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sniffing**                          | Passively monitoring network traffic to capture session IDs in plaintext HTTP traffic                                                                                                                                        |
| **Predictable Session ID Attack**     | Analyzing the session generation pattern and reconstructing valid tokens (as described in Section 4)                                                                                                                         |
| **Man-in-the-Middle (MitM) Attack**   | Attacker inserts themselves between the user and server, receiving all traffic in both directions — intercepts session IDs as they are transmitted                                                                           |
| **Man-in-the-Browser Attack**         | Malware installed in the victim's browser intercepts session data locally before it is encrypted and sent                                                                                                                    |
| **Cross-Site Scripting (XSS)**        | Injecting malicious JavaScript into a web page; when victim visits the page, the script reads their session cookie and sends it to the attacker                                                                              |
| **CSRF (Cross-Site Request Forgery)** | Attacker sends victim a malicious link; when victim (already logged in) clicks it, the browser automatically sends the victim's session cookie with the forged request — the server processes it as a legitimate user action |
| **Session Replay Attack**             | Attacker captures a session ID and replays it later to regain access                                                                                                                                                         |
| **CRIME Attack**                      | **C**ompression **R**atio **I**nfo-leak **M**ade **E**asy — exploits HTTPS compression to leak session tokens by observing response size changes                                                                             |
| **Session Donation Attack**           | Attacker logs in themselves, then tricks the victim into using the attacker's session ID                                                                                                                                     |
| **Padding Oracle Attack**             | Exploits the server's padding error responses to decrypt encrypted session tokens                                                                                                                                            |

> **Layer 7 significance:** All these attacks happen at the application layer where HTTP (not HTTPS) traffic is readable as plaintext, and even HTTPS can be targeted if SSL stripping or protocol-level vulnerabilities are exploited.

---

## 7. Network-Layer Session Attacks

These attacks occur at **lower OSI layers** (primarily Layer 3 — Network, and Layer 4 — Transport), targeting the underlying TCP/IP communication rather than the web application itself.

| Attack                                | Description                                                                                                                                                                             |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Blind Hijacking**                   | Attacker cannot see the actual packets but sends forged responses blindly, hoping to inject commands into the session                                                                   |
| **UDP Hijacking**                     | Sends forged/fake UDP packets to manipulate a session (UDP has no authentication built-in, making it susceptible to forgery)                                                            |
| **TCP/IP Hijacking**                  | Exploits the TCP three-way handshake by predicting TCP sequence numbers; attacker takes over an existing TCP session by sending packets with correct sequence numbers                   |
| **RST (Reset) Attack**                | Repeatedly sends TCP RST (Reset) packets to break the connection, forcing the session to re-establish and attempting to intercept the new session ID in the process                     |
| **IP Spoofing**                       | Attacker sends packets with a forged source IP address (the victim's IP); server's reply goes to the attacker                                                                           |
| **Man-in-the-Middle (Network Level)** | Using **ARP spoofing** and **ICMP manipulation** to position attacker as the intermediary between user and router/server at the network layer — all traffic passes through the attacker |

### 7.1 ARP and ICMP in Network-Level MitM

- **ARP (Address Resolution Protocol):** Resolves IP addresses to MAC addresses. By sending fake ARP replies, an attacker can tell all devices on the local network "my MAC address is the router's IP" — causing all traffic intended for the router to be sent to the attacker's machine instead.
- **ICMP (Internet Control Message Protocol):** Used for ping commands and network diagnostics. Can be used to redirect traffic or cause disruption as part of MitM setup.

---

## 8. Practical 1: Viewing & Reusing a Session ID (Browser Demo)

### 8.1 Goal

Demonstrate what a session ID looks like in the browser and show how a stolen (unencrypted) session ID can be used to gain access to an account without a password.

### 8.2 Target Website

- **Demo site:** `http://hub.pw` (a deliberately insecure practice/demo website with **unencrypted** session IDs — suitable for learning)
- **Contrast site:** `https://newversionhacker.in` (properly secured — session ID is encrypted, demonstration of what "secure" looks like)

### 8.3 Step 1 — View the Session ID in Browser Developer Tools

1. Open the target website (`hub.pw`) in a browser.
2. Log in with test credentials (e.g., `test` / `test`).
3. Press **F12** to open Developer Tools.
4. Navigate to: **Application** (Chrome) or **Storage** (Firefox) tab.
5. In the left panel, expand **Cookies** → select the website's domain.
6. You will see a table with cookie **Name** and **Value** columns:
   - The **Name** field contains something like `login` or `session`.
   - The **Value** field contains the actual **session ID token**.

**Example output:**

```
Name: login
Value: a7b3f182202417215022abc124def456
```

> On a properly secured website (`newversionhacker.in`), the same process shows cookies where both names and values appear as encrypted/hashed strings — meaningless to read directly — demonstrating the security contrast.

### 8.4 Step 2 — Steal and Reuse the Session ID on Another Machine

**On the source machine (Kali Linux — "attacker"):**

1. Log into `hub.pw` with test credentials.
2. Open DevTools → Storage → Cookies.
3. **Copy** the session ID value from the `login` cookie.

**On the target machine (Windows 10 — "victim simulation"):**

1. Open a browser and navigate to `hub.pw` without logging in.
2. Open DevTools → Storage → Cookies for the `hub.pw` domain.
3. **Add** a new cookie entry manually:
   - **Name:** `login` (matching the name from the source)
   - **Value:** Paste the copied session ID
4. **Refresh the page.**

**Result:** The page shows the user as logged in (displaying `test` account profile and details) — **without ever entering a username or password** on this machine. This directly demonstrates that possessing the session ID is equivalent to being authenticated.

> **Why this works on `hub.pw` but not on secured sites:** On `hub.pw`, the session token is plaintext and has no additional binding (no IP binding, no browser fingerprinting, no HttpOnly flag). On properly secured websites, session cookies are typically: (1) encrypted, (2) marked `HttpOnly` (not accessible via JavaScript), (3) marked `Secure` (only transmitted over HTTPS), and/or (4) bound to specific client characteristics.

---

## 9. Practical 2: Session Hijacking via Proxy (ZAP/OWASP Proxy)

### 9.1 Tool Used

**ZAP (Zed Attack Proxy)** — an open-source web proxy that intercepts, reads, and modifies HTTP/HTTPS traffic between a browser and web server.

### 9.2 Lab Setup

- **Attacker machine:** Windows Server 2019
- **Victim machine:** Windows 11 (`ws1`)

### 9.3 How a Proxy-Based Session Hijack Works (Concept)

```
Normal flow:    Victim browser → Web Server
Proxy-inserted: Victim browser → Proxy (ZAP on attacker's machine) → Web Server
```

Once the proxy is in the path, **all traffic passes through it** — the attacker can see, intercept, and modify any request or response before it reaches its destination.

**The key insight:** The proxy intercepts the session token in transit and can also **redirect the victim to a different (malicious) website** by modifying the destination in the HTTP headers.

### 9.4 Step-by-Step

**Step 1 — Configure ZAP:**

1. Launch ZAP on the attacker machine.
2. Click the **"+"** icon to add a new session scope.
3. Click **"Break"** to set up request/response interception.
4. ZAP splits the view: request visible on one side, response on the other.

**Step 2 — Set browser proxy settings on the victim machine:**

1. In Firefox (victim machine): **Preferences → Network Settings → Manual Proxy Configuration**.
2. Set:
   - **HTTP Proxy:** IP address of the attacker machine (where ZAP is running)
   - **Port:** matching ZAP's listener port (default: `8080`)
3. Click **OK**.

> Once the proxy is set, **all browser traffic from the victim passes through ZAP on the attacker's machine**.

**Step 3 — Start ZAP interception:**

1. On ZAP, click **"Start"** to begin intercepting.
2. On the victim machine, browse to a website (e.g., `www.gomovie.com`).
3. The browser may show a security warning (proxy certificate) — click **Advanced → Accept Risk and Continue**.

**Step 4 — Intercept and modify the request:**

1. ZAP shows the intercepted HTTP request in its interface.
2. To redirect the victim to a different website:
   - Find the destination domain in the request headers (e.g., `Host: gomovie.com`).
   - **Replace** all instances of the original domain with the target redirect domain (e.g., `goshopping.com`).
3. Click **"Forward"** / **"Next Step"** to release the modified request.
4. Repeat the modification for subsequent requests (may need to do this several times until the redirection takes hold).

**Result:** The victim's browser loads a **completely different website** (`goshopping.com`) while the address bar may still show the original domain — this is the core mechanism of session manipulation and redirect attacks via proxy.

### 9.5 What This Demonstrates

This practical shows three key concepts:

1. **Traffic interception** — all HTTP traffic between victim and server is visible to the proxy.
2. **Content modification** — requests and responses can be altered in transit (injecting a redirect).
3. **De-synchronization** — the proxy breaks the direct connection between victim and server into separate segments it controls.

---

## 10. Practical 3: Man-in-the-Middle + HTTP Traffic Capture (BetterCap)

### 10.1 Tool Used

**BetterCap** — a powerful network attack and monitoring framework, capable of performing Man-in-the-Middle attacks, ARP spoofing, SSL stripping, and packet sniffing — all in one tool.

### 10.2 Lab Setup

- **Attacker machine:** Kali Linux (network interface: `eth0`)
- **Victim machine:** Windows 11 (`10.10.10.11`)
- **Both machines on same local network**

### 10.3 Step-by-Step Commands

**Step 1 — Launch BetterCap:**

```bash
sudo bettercap -iface eth0
```

_(Replace `eth0` with your actual network interface — check with `ifconfig` or `ip a`)_

**Step 2 — Enable Network Probing (discover live hosts):**

```bash
net.probe on
```

This sends probe packets across the network to discover all active devices.

**Step 3 — Enable Network Reconnaissance:**

```bash
net.recon on
```

Detects and lists all IP addresses active on the local network by reading ARP tables.

**Step 4 — Configure SSL Stripping:**

```bash
set https.proxy.sslstrip true
```

SSL stripping downgrades HTTPS connections to HTTP — so encrypted traffic becomes readable plaintext. This is key to capturing credentials and session tokens from "secure" sites.

**Step 5 — Set ARP Spoofing Target:**

```bash
set arp.spoof.targets 10.10.10.11
```

_(Replace with your victim's IP address — found via Nmap or net.recon)_

Configures ARP spoofing to impersonate the router for the victim device — causing all victim traffic to flow through the attacker's machine.

**Step 6 — Enable HTTPS Proxy:**

```bash
https.proxy on
```

Activates the SSL-stripping proxy to intercept HTTPS traffic.

**Step 7 — Enable ARP Spoofing:**

```bash
arp.spoof on
```

Begins sending fake ARP replies, telling the victim that the attacker's MAC address belongs to the router's IP. All victim traffic is now redirected through the attacker.

**Step 8 — Enable Packet Sniffing:**

```bash
net.sniff on
```

Starts capturing all network packets passing through the attacker's machine.

**Step 9 — Filter for Password Fields (Optional — targeted capture):**

```bash
set net.sniff.regexp '.*password=.+'
```

Applies a regex filter to show only packets containing password fields — reduces noise from irrelevant traffic.

_(Note: You can replace `password` with any field name you want to capture, e.g., `username`, `token`, `session`, etc.)_

**Step 10 — Victim logs into a website:**
On the victim machine (Windows 11), open Firefox and navigate to `www.gomovie.com` → enter credentials → log in.

**Step 11 — View captured credentials on attacker machine:**

- BetterCap's terminal shows incoming packet data.
- Scroll through output to find captured form submissions.
- **Captured data visible includes:**
  - `username` field value (plaintext)
  - `password` field value (plaintext)
  - Session cookies and other request parameters

> **Why this works:** The victim's traffic was downgraded from HTTPS to HTTP by SSL stripping, so credentials were transmitted in plaintext and captured by BetterCap's sniffer.

> **Why it doesn't always work:** If a website uses **HSTS (HTTP Strict Transport Security)**, the browser refuses to downgrade to HTTP regardless of what the proxy instructs, making SSL stripping ineffective.

---

## 11. Detection: Identifying a Man-in-the-Middle Attack (Wireshark)

### 11.1 How to Detect a MitM Attack on Your Network

A network-level MitM (using ARP spoofing, as done by BetterCap) leaves a **detectable signature in ARP traffic**.

### 11.2 Detection Process Using Wireshark

**On the victim machine (Windows 11):**

1. Open **Wireshark** and start capturing on the network interface.
2. Filter for ARP packets in the filter bar:
   ```
   arp
   ```
3. **Observe the ARP packets:**

**Normal ARP behavior (no attack):**

- The router (`10.10.10.1`) asks: "Who has IP `10.10.10.11`?" → victim replies with its MAC address.
- Only the router should be asking who owns which IP addresses.

**ARP Spoofing indicator (MitM active):**

- A **third machine** (`10.10.10.18` — the attacker) is sending **ARP replies claiming to be the router**.
- All devices' ARP tables are being told: "MAC address XX:XX:XX:XX = `10.10.10.1`" — but the real router has a different MAC.
- In Wireshark, you'll see the attacker's IP (`10.10.10.18`) sending many ARP packets that should only come from the router.
- Indicator: **`Who has 10.10.10.x? Tell 10.10.10.18`** — any machine other than the router sending these is suspicious.

**Confirmation — Closing BetterCap:**

- When BetterCap is stopped on the attacker machine (`Ctrl+C` → `exit`), the anomalous ARP traffic stops immediately.
- Wireshark now shows only legitimate router ARP traffic: "Who has `10.10.10.x`? Tell `10.10.10.2`" (the actual router IP).
- This before/after comparison **confirms the attack was happening** and the anomalous IP was the attacker.

---

## 12. Prevention & Counter Measures

### 12.1 Session-Level Protections

| Measure                                    | Description                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Use HTTPS everywhere**                   | Encrypts all traffic, making session IDs unreadable in transit — the single most important protection                                                             |
| **HSTS (HTTP Strict Transport Security)**  | Forces browsers to always use HTTPS for a domain — prevents SSL stripping                                                                                         |
| **Secure cookie flag**                     | Marks session cookies so they are **only transmitted over HTTPS**, never HTTP                                                                                     |
| **HttpOnly cookie flag**                   | Prevents JavaScript from accessing the session cookie — blocks XSS-based session theft                                                                            |
| **SameSite cookie attribute**              | Restricts session cookies from being sent in cross-site requests — mitigates CSRF                                                                                 |
| **Short session timeouts**                 | Automatically invalidate session IDs after a period of inactivity — limits the window of opportunity if a session ID is stolen                                    |
| **Session ID regeneration**                | Issue a new session ID after login, privilege escalation, or sensitive actions — prevents session fixation                                                        |
| **Cryptographically random session IDs**   | Use CSPRNG (Cryptographically Secure Pseudo-Random Number Generator) to generate session IDs — makes them unpredictable and immune to guessing/prediction attacks |
| **Bind session to client characteristics** | Tie session ID validity to specific attributes like IP address or browser fingerprint — a stolen session ID from a different IP/browser becomes invalid           |

### 12.2 Network-Level Protections

| Measure                                | Description                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Dynamic ARP Inspection (DAI)**       | Network switch feature that validates ARP packets against a trusted binding table — blocks ARP spoofing                   |
| **SSL/TLS encryption**                 | Encrypts data in transit — even if traffic is intercepted, it cannot be read without the decryption keys                  |
| **VPN**                                | Encrypts all traffic between the device and VPN server — protects against local network sniffing                          |
| **Network segmentation**               | Divides the network into segments so attackers on one segment cannot see or manipulate traffic on others                  |
| **Network Monitoring (Wireshark/IDS)** | Continuously monitor ARP traffic for anomalies — tools like Wireshark or IDS systems can detect ARP spoofing in real time |

### 12.3 For Developers (Secure Implementation)

| Best Practice                                   | Description                                                                                                               |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Never embed session IDs in URLs**             | URL-based session IDs are logged in server logs, browser history, and referrer headers — use cookies instead              |
| **Invalidate sessions on logout**               | Ensure the server destroys/invalidates the session server-side on logout — don't just delete the client cookie            |
| **Implement proper CSRF tokens**                | Add unpredictable per-session tokens to all state-changing forms/requests                                                 |
| **Log and alert on anomalous session activity** | Multiple concurrent sessions from different IPs, rapid geographic movement, unusual request patterns — all warrant alerts |

---

## 13. Key Takeaways

1. **Sessions exist to avoid repeated authentication** — the session ID is a temporary identity token that replaces the need to enter username/password for every action during an active session.

2. **Stealing a session ID is more powerful than stealing a password** — a session ID bypasses all login-time protections including OTP, 2FA, and CAPTCHA, since authentication has already been completed.

3. **Predictable session IDs are critically weak** — if session IDs are generated based on timestamps or observable patterns, attackers can reconstruct them without ever seeing the victim's network traffic.

4. **Session hijacking spans multiple OSI layers** — application-layer attacks (XSS, CSRF, MitM, sniffing) target the web session directly; network-layer attacks (ARP spoofing, TCP hijacking, RST attacks) target the underlying communication protocol.

5. **ARP spoofing is the foundation of most local-network MitM attacks** — by sending fake ARP replies, an attacker can redirect all victim traffic through their machine; this is detectable via Wireshark by observing which machine is sending ARP packets that should only come from the router.

6. **SSL stripping can downgrade HTTPS to HTTP** — making "secure" traffic readable — unless HSTS is in place, in which case the browser refuses to downgrade.

7. **The `HttpOnly` and `Secure` cookie flags are essential minimum protections** for session cookies — they prevent JavaScript access (blocking XSS theft) and ensure cookies only travel over HTTPS (blocking plaintext interception).

8. **Detection is possible in real time** — anomalous ARP traffic (a non-router device claiming to be the router) is a reliable indicator of ARP-based MitM attacks, detectable with any packet analyzer like Wireshark.

9. **BetterCap is a comprehensive MitM framework** — combining ARP spoofing, SSL stripping, proxy interception, and packet sniffing in a single tool, demonstrating how multi-stage network attacks are chained together.

10. **Practical testing on authorized systems only** — all these techniques should be practiced in isolated lab environments (personal VMs, dedicated test servers) — never on real networks or systems without explicit written authorization.

---

_These notes complement the Web Application Security module (which covers XSS and CSRF in depth) and the Firewalls/IDS/IPS module (which covers network-level detection and prevention). Session hijacking is the bridge between application-layer and network-layer attacks._
