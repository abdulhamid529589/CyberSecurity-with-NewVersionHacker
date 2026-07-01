# Web Application Hacking & Security — Masterclass Notes

## https://youtu.be/peEEg00coLU

**Course:** Ethical Hacking / Cyber Security
**Type:** Theory + Practical Lab
**Instructor reference:** New Version Hacker

> **Important Disclaimer (from instructor):** All techniques covered here are strictly for **educational purposes** — to build defensive awareness and pursue an ethical career in cyber security. Performing any of these techniques on systems/websites **without explicit written permission** is illegal under **IT Act 2000, Section 66** (up to 3 years imprisonment and/or ₹5 lakh fine). Always work ethically with authorized targets only, or participate in official **Bug Bounty Programs**.

---

## Table of Contents

1. [Introduction to Web Applications](#1-introduction-to-web-applications)
2. [Web Application vs. Website vs. Web Server](#2-web-application-vs-website-vs-web-server)
3. [Components of a Web Application](#3-components-of-a-web-application)
4. [Static vs. Dynamic Web Applications](#4-static-vs-dynamic-web-applications)
5. [How a Web Application Works (Request-Response Flow)](#5-how-a-web-application-works-request-response-flow)
6. [Common Web Application Attacks](#6-common-web-application-attacks)
7. [Web Application Security Testing — 4-Step Process](#7-web-application-security-testing--4-step-process)
8. [Counter Measures (Defense Best Practices)](#8-counter-measures-defense-best-practices)
9. [Practical Lab: Step-by-Step Demonstrations](#9-practical-lab-step-by-step-demonstrations)
10. [Tools Reference Summary](#10-tools-reference-summary)
11. [Key Takeaways](#11-key-takeaways)

---

## 1. Introduction to Web Applications

**Definition:** A web application is a type of **software hosted on a web server** and accessed through a **web browser**. Unlike traditional desktop applications that are installed locally, web applications run on a remote server and deliver an interactive, application-like experience through the browser.

**Why security matters for web applications:**

- Web applications are used everywhere — shopping websites, banking portals, social media platforms, and business tools.
- All of these carry **security vulnerabilities** that attackers can exploit to gain unauthorized access to sensitive information.

**Common examples of web applications:** Facebook, Instagram, YouTube, Gmail, Amazon, Online Banking platforms, Zoom.

---

## 2. Web Application vs. Website vs. Web Server

These three terms are often confused. Here is a clear distinction:

### Web Server

- A **large computer (server)** that stores and delivers data — web pages, files, and software.
- It accepts incoming requests from users and responds with the appropriate content.
- A web server is the **hardware/software infrastructure** on which both websites and web applications are hosted.
- **Examples of web server software:** Apache, Nginx (NGNX), IIS (Microsoft — used primarily on Windows).

### Website

- A **collection of web pages** (static or dynamic content) linked together.
- Analogy: like a **book with pages** — each page contains different content; you navigate from page to page.
- You can only access a website **through a browser** — you cannot install it as an app on your phone.
- Websites deliver information (news, blogs, services) but offer limited interactivity.
- **Example:** Wikipedia, news websites.

### Web Application

- A **software application** hosted on a server that provides a **rich, interactive experience** — equivalent to a native app but delivered via a web browser.
- Analogy: like a **phone** — compact, everything is bundled, highly interactive, and can be installed as both a phone app and accessed from a desktop browser.
- Can be **installed as an app on a phone** AND accessed as a website on a desktop — the same core functionality is available on both platforms.
- Allows users to **upload content, submit forms, make transactions, and receive real-time updates**.
- **Examples:** YouTube, Facebook, Gmail, Amazon, Zoom.

### Comparison Table

| Aspect                    | Website                        | Web Application                                               |
| ------------------------- | ------------------------------ | ------------------------------------------------------------- |
| **Nature**                | Collection of linked web pages | Software application hosted on a server                       |
| **Content**               | Mostly static (read/view only) | Dynamic — real-time updates, user uploads, transactions       |
| **User Interface**        | Page-based navigation          | App-like, highly interactive                                  |
| **Installable on phone?** | No — browser only              | Yes — downloadable app + browser access                       |
| **User interaction**      | Limited (reading, viewing)     | High — form submission, transactions, real-time communication |
| **Examples**              | Wikipedia, news sites, blogs   | Facebook, Gmail, Amazon, YouTube, Zoom                        |

---

## 3. Components of a Web Application

### 3.1 Web Server (Storage/Request Handler)

Stores the application and handles incoming user requests. **Same servers are used for both websites and web applications** — e.g., Apache, Nginx, IIS.

### 3.2 Database Server

Stores and manages user data (login credentials, profiles, transaction history, etc.).

| Database Server | Notes                                       |
| --------------- | ------------------------------------------- |
| **MySQL**       | Very common open-source relational database |
| **PostgreSQL**  | Advanced open-source relational database    |
| **MongoDB**     | NoSQL (document-based) database             |
| **Oracle DB**   | Enterprise-grade database                   |

### 3.3 Client Side (Front End)

The **interface visible to the user** in the browser. Built using:

- **HTML** — structure/content
- **CSS** — styling/appearance
- **JavaScript** — interactivity/dynamic behavior

> You (the user) are the **client**; the server is the service provider. The page visible in your browser is the client side.

### 3.4 Back End (Server-Side Logic)

All **processing that happens invisibly on the server** — authentication checks, database queries, request validation, and response generation.

| Back-End Technology | Notes                                      |
| ------------------- | ------------------------------------------ |
| **PHP**             | Widely used server-side scripting language |
| **Python**          | Used with frameworks like Django, Flask    |
| **Node.js (NJS)**   | JavaScript-based back-end runtime          |
| **ASP.NET**         | Microsoft's back-end framework             |

---

## 4. Static vs. Dynamic Web Applications

| Type        | Description                                                                | Interaction                                      | Technologies                                                |
| ----------- | -------------------------------------------------------------------------- | ------------------------------------------------ | ----------------------------------------------------------- |
| **Static**  | Fixed content — looks the same to every user; no database; simple/graphic  | Read-only — users cannot upload or submit        | HTML, CSS                                                   |
| **Dynamic** | Content changes per user/input; has a back-end database; updated regularly | Highly interactive — upload, submit, personalize | HTML + Database + Server-side scripting (PHP, Python, etc.) |

> **Examples of dynamic web apps:** YouTube (you can upload videos), Facebook (you can post, comment), Instagram (you can share content). Static web apps are far more limited — like viewing a digital brochure.

---

## 5. How a Web Application Works (Request-Response Flow)

Using **Amazon login** as an example:

```
User (Browser / Client Side)
        ↓  [Sends login request with username + password]
Web Server
        ↓  [Forwards credentials for verification]
Database Server
        ↓  [Checks if credentials match stored records]
Web Server
        ↓  [Responds: "Valid user — access granted" / "Invalid credentials"]
User (Browser)
        ↓  [Login page loads OR error message displayed]
```

**Key principle:** The verification process (checking your username/password against the database) happens **entirely in the back end** — you only see the end result (logged in, or error message) in the front end (browser).

---

## 6. Common Web Application Attacks

### 6.1 SQL Injection (SQLi)

**What it is:** A technique where an attacker **injects malicious SQL code** into an input field of a web application (e.g., a login form, search box) to **manipulate the underlying database**.

**Impact:** Can give attackers unauthorized access to the database, allowing them to:

- Log in without knowing the actual password.
- Steal, modify, or delete database data.
- Access other users' records.

**How it works:**

- Web applications use SQL queries to check credentials (e.g., `SELECT * FROM users WHERE username='input' AND password='input'`).
- If user input is not properly validated, an attacker can inject a query such as `' OR '1'='1` which always evaluates to true, bypassing the password check entirely.

**Protection:**

- **Use parameterized queries / prepared statements** — separates SQL code from user input so injected code cannot be executed.
- **Sanitize user input** — filter/validate what users are allowed to enter.
- **Use a Web Application Firewall (WAF)** — e.g., ModSecurity, Cloudflare.

---

### 6.2 Cross-Site Scripting (XSS)

**What it is:** An attack where a hacker **injects malicious JavaScript** into a web application's user-facing content (e.g., a comment box, search field). The script then **executes in another user's browser** when they visit that page.

**Important distinction:** XSS is a **client-side vulnerability** — the script is injected and runs in the _victim's browser_, though the impact ultimately reaches the server.

**Impact:**

- Stealing session **cookies** (used to hijack the victim's session/account).
- Capturing login credentials.
- Used as a **phishing vector** — redirecting users to fake pages.

**How it works:**

- Attacker posts a malicious script in a comment field on a vulnerable website.
- When another user visits that page, the script executes in _their_ browser.
- The attacker receives the victim's session token and can impersonate them.

**Protection:**

- **Input validation and sanitization** — reject or encode special characters (e.g., `<`, `>`, `"`, `'`) in user-supplied input to prevent script tags from rendering.
- **Content Security Policy (CSP)** — a browser security header that restricts which scripts are allowed to run.
- **Enable XSS filters** — built-in browser/server-side filtering mechanisms.
- **Encode output** — special characters like `<` and `>` should be converted to their HTML entity equivalents (`&lt;`, `&gt;`) so they display as text, not as executable code.

---

### 6.3 Cross-Site Request Forgery (CSRF)

**What it is:** An attack where an attacker **tricks a logged-in user** into unknowingly performing an action on a web application they are already authenticated to (e.g., changing a password, making a bank transfer), by sending them a malicious link or script.

**Key distinction from XSS:**

| Aspect                       | XSS                                                                              | CSRF                                                                                             |
| ---------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Attack origin**            | Attacker injects a script **into the website** that runs in the victim's browser | Attacker **sends a malicious request** that exploits the victim's existing authenticated session |
| **Who is directly targeted** | A visiting user's browser                                                        | An authenticated user's session on a trusted site                                                |
| **Mechanism**                | Script injection on the target site                                              | Forged request sent from attacker to victim, executed via victim's credentials                   |

**How it works:**

- User is logged in to their bank (or Facebook, etc.).
- Attacker crafts a malicious script that sends a forged request (e.g., a password-change or fund-transfer request) in the user's name.
- Attacker sends this script as a link to the victim.
- When the victim clicks the link, their browser **automatically sends the forged request** with their valid session credentials — the bank/site processes it as if the user initiated it.

**Protection:**

- **CSRF Tokens** — unique, unpredictable tokens tied to each user session, included in every request; the server validates the token before processing the request.
- **SameSite Cookie Attribute** — configure cookies so they are not sent with cross-site requests.
- **Multi-Factor Authentication (MFA)** — requiring additional authentication for sensitive actions (e.g., entering a transaction PIN before a transfer) significantly reduces impact.

---

### 6.4 Broken Authentication & Session Management

**What it is:** When a web application's authentication or session management is **poorly implemented**, allowing attackers to compromise user accounts.

**Common causes:**

- Weak or easily guessable passwords (no enforced password strength policy).
- Sessions that don't expire (no session timeout).
- Tokens that never expire (no token expiry).
- Credentials transmitted or stored in plain text.

**Impact:** Attackers can brute-force passwords, hijack sessions, or take over accounts.

**Protection:**

- **Strong password policies** — minimum length, complexity requirements.
- **Session timeout** — automatically log out inactive sessions (as banks typically do).
- **Token expiry** — authentication tokens should have a limited valid lifespan.
- **Multi-Factor Authentication (MFA)** — adds an additional verification layer beyond just username/password.

---

### 6.5 Security Misconfiguration

**What it is:** Insecure configurations in the web server, application, or database — often because default settings were never changed.

**Common examples:**

- Leaving **default credentials** unchanged (e.g., `admin`/`admin`, Kali Linux's default `kali`/`kali`).
- Displaying **detailed error messages** to users that reveal server paths, technology stack details, or database information — giving attackers a roadmap to vulnerabilities.
- Leaving **debug mode enabled** on a production server.
- Exposing **unnecessary features/ports** that aren't needed.

**Protection:**

- **Audit security settings** — regularly review all configurations.
- **Change default credentials immediately** upon first login to any service.
- **Use custom error messages** — display user-friendly generic messages (e.g., "Site under maintenance") rather than technical error details.
- **Disable unnecessary features** — reduce attack surface by turning off anything not in active use.
- **Remove debug mode** on all production environments.

---

## 7. Web Application Security Testing — 4-Step Process

### Step 1: Information Gathering (Reconnaissance / Footprinting)

**Goal:** Collect as much information as possible about the target web application — technology stack, server type, database, hidden directories, subdomains, and developer comments.

**What to find:**

- Which back-end technology is used (PHP, ASP.NET, Python, etc.)?
- What database is used (MySQL, Oracle, etc.)?
- What domains and subdomains exist?
- Are there any hidden pages or directories?
- What developer comments or configuration files are accessible?

**Tools used:**

| Tool             | Purpose                                                                                                                                                                           |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nmap**         | Network and port scanning; OS/service fingerprinting                                                                                                                              |
| **Netcraft**     | Website fingerprinting — technology/hosting info                                                                                                                                  |
| **Whois Lookup** | Domain registration and ownership information                                                                                                                                     |
| **Shodan**       | Scanning internet-connected devices live on the internet                                                                                                                          |
| **WhatWeb**      | Identifies web technology, CMS, server software, JavaScript libraries, email addresses, account IDs, SQL errors, and more — a comprehensive all-in-one information-gathering tool |
| **Telnet**       | Active banner grabbing — directly connects to the server to extract OS/application version info                                                                                   |
| **Gobuster**     | Brute-forces website directories using a word list to discover hidden folders/paths                                                                                               |
| **Dirsearch**    | Directory scanning — discovers web application directories (built-in word list, colorful interface)                                                                               |

> **Banner Grabbing:** A technique to identify the OS and application version running on a server. Two types:
>
> - **Active** — directly connects to the target (Nmap, Telnet) to retrieve information.
> - **Passive** — collects data without direct connection, via packet sniffing or third-party services (Wireshark, Shodan).

> **Load Balancer Detection:** Large organizations use load balancers to distribute incoming traffic across multiple servers. Tools to detect them: **DIG** (DNS-based LB detection) and **LBD** (Load Balancing Detector).

---

### Step 2: Web Application Scanning (Vulnerability Analysis)

**Goal:** Automatically detect security vulnerabilities in the target web application using automated scanning tools.

**What automated scanners check for:**

- SQL injection, XSS, CSRF vulnerabilities.
- Open/misconfigured ports.
- Directory traversal issues.
- Server/CMS-specific known vulnerabilities.
- Outdated plugins, themes, or software versions.

**Tools used:**

| Tool                                       | Purpose                                                                                                                                                  |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ZAP (OWASP Zed Attack Proxy) / Zaproxy** | GUI-based open-source automated web application scanner; performs spider crawling (mapping all site pages/directories) and active vulnerability scanning |
| **OWASP (framework)**                      | Open Web Application Security Project — the standard reference for classifying vulnerabilities (Top 10 list)                                             |
| **Nikto**                                  | Command-line web server vulnerability scanner                                                                                                            |
| **Acunetix (Aquanetics)**                  | Automated commercial website security scanner                                                                                                            |
| **Burp Suite (Verbshoot)**                 | GUI-based web application proxy for both manual and automated scanning and interception                                                                  |
| **WPScan**                                 | WordPress-specific security scanner (requires free API key registration); identifies plugins, themes, user enumeration, and known CVEs                   |

> **OWASP Top 10:** The gold standard reference list of the most critical web application security vulnerabilities. When reporting findings, vulnerabilities should be categorized according to which OWASP Top 10 category they match.

---

### Step 3: Exploitation

**Goal:** Ethically exploit discovered vulnerabilities to **validate their existence and assess their real-world impact**, demonstrating how an attacker could use them — always with **prior written permission from the target**.

**What happens:**

- If an SQL injection vulnerability is found → attempt to interact with the database.
- If an XSS vulnerability is found → test session cookie capture.
- Parameter tampering → test unauthorized access to other users' data.

> ⚠️ **Legal reminder:** Exploitation without permission is a criminal offense. Always operate within scope defined by the client/organization and obtain written authorization before proceeding.

**Tools used:**

| Tool                                      | Purpose                                                               |
| ----------------------------------------- | --------------------------------------------------------------------- |
| **SQLMap**                                | Automated SQL injection exploitation tool                             |
| **Metasploit (Metaexploitable)**          | Industry-standard exploitation framework for penetration testing      |
| **BeEF (Browser Exploitation Framework)** | Browser-based exploitation for XSS and client-side attacks            |
| **Burp Suite Intruder**                   | Automated brute force of authentication forms using custom word lists |

---

### Step 4: Reporting & Fixing

**Goal:** Produce a **clear, professional report** documenting all discovered vulnerabilities and providing actionable remediation recommendations.

**A good report includes:**

- **Screenshots** of all vulnerabilities found (visual evidence).
- **Vulnerability categorization** — mapping each finding to the OWASP Top 10.
- **Impact assessment** — explaining the real-world damage each vulnerability could cause if exploited.
- **Fixing recommendations** — specific, actionable steps to remediate each issue (e.g., "Apply parameterized queries to the login endpoint" or "Update WordPress plugin X to version Y").
- **Severity rating** — typically: Critical / High / Medium / Low.

**Post-report actions (by the development team):**

- **Patch** identified vulnerabilities by updating configurations or modifying code.
- **Retest** to confirm patches are effective.
- **Update security policies** as needed.

---

## 8. Counter Measures (Defense Best Practices)

| Defense Measure                           | Description                                                                                                                    |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Secure Coding Practices**               | Implement input validation, use parameterized queries, avoid hard-coded credentials                                            |
| **Strong Authentication & Authorization** | Enforce strong password policies, implement session timeouts and token expiry                                                  |
| **Multi-Factor Authentication (MFA)**     | Require additional verification (OTP, biometrics) for login and sensitive actions                                              |
| **Security Headers**                      | Implement HTTP security headers (e.g., `Content-Security-Policy`, `X-Frame-Options`, `HTTPS Strict Transport Security / HSTS`) |
| **Web Application Firewall (WAF)**        | Deploy a WAF (e.g., ModSecurity, Cloudflare) on the server to filter malicious requests                                        |
| **CSRF Tokens**                           | Embed unique per-session tokens in all state-changing requests                                                                 |
| **SameSite Cookie Attribute**             | Configure cookies to prevent them from being sent in cross-site requests                                                       |
| **Custom Error Messages**                 | Show user-friendly generic error pages — never reveal server details, file paths, or stack traces in errors                    |
| **Regular Security Audits**               | Audit configurations, access controls, and code regularly on a scheduled basis                                                 |
| **Disable Unnecessary Features**          | Turn off unused services, ports, and debug modes — minimize attack surface                                                     |
| **Change Default Credentials**            | Immediately change all default usernames and passwords upon first deployment                                                   |
| **Input Sanitization / Encoding**         | Encode special characters (`<`, `>`, `'`, etc.) in all user-supplied input before rendering in HTML                            |

---

## 9. Practical Lab: Step-by-Step Demonstrations

> **Lab Environment Used:**
>
> - **Attacker machine:** ParrotOS / Kali Linux
> - **Target machines:** Windows Server 2022/2019, Windows 10/11 (in a controlled virtual network)
> - **Target website:** `www.govmovie.com` (a personal test domain created specifically for demonstration)
> - **Additional test site:** `http://testphp.vulnweb.com` (a public testing website designed specifically for security testing practice)
> - **WordPress demo site:** Set up on a local server for CSRF/brute-force demonstrations
>
> You need: one Windows machine, one server (2019/2022), and one attacker machine (Kali or Parrot).

---

### Practical 1: Port Scanning & Service Discovery with Nmap

**Goal:** Identify open ports, running services, and OS information of the target.

**Command:**

```bash
nmap -T4 -A -v www.govmovie.com
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `-T4` | Timing template — controls scan speed (0=slowest, 5=fastest); `-T4` is fast but not aggressive enough to trigger most IDS |
| `-A` | Aggressive scan — combines OS detection, version detection, script scanning, and traceroute |
| `-v` | Verbosity — shows more detailed output; `-vv` gives even more detail |

**What the output reveals:**

- All **open ports** and their states.
- Running **service names and versions** per port.
- **NetBIOS name** (computer name on the network).
- **DNS domain name**.
- **MAC address** (and associated vendor).
- **OS detection** (e.g., Windows version).
- **SSL certificate details** — encryption algorithm (e.g., RSA), key bit-length, signature algorithm (e.g., SHA-256), and the certificate's MD5 hash value.
- **Public key type** and encryption standards in use.

---

### Practical 2: Banner Grabbing with Telnet (Active)

**Goal:** Identify the web server software and back-end technology by directly querying the server via Telnet.

**Command sequence:**

```bash
telnet www.govmovie.com 80
```

After connecting, send an HTTP GET request:

```
GET / HTTP/1.0

```

_(Press Enter twice after the second line)_

**What it reveals:**

- **Back-end technology** (e.g., `X-Powered-By: ASP.NET` confirms ASP.NET is the back-end framework).
- **Server software version** (e.g., `Server: Microsoft-IIS/10.0`).
- **Content type** (e.g., `Content-Type: text/html` confirms HTML is used for the client side).

> **Why port 80?** Port 80 is the standard HTTP port — most web applications communicate on port 80 (plain HTTP) or port 443 (HTTPS). Telnet connects to port 80 to retrieve raw HTTP headers.

---

### Practical 3: Technology Identification with WhatWeb

**Goal:** Extract comprehensive fingerprint information about the target website in one tool.

**Basic usage:**

```bash
whatweb www.govmovie.com -v
```

**Save output to a report file:**

```bash
whatweb --log-verbose=movie_report www.govmovie.com
```

View the saved report using a text editor:

```bash
nano movie_report
# OR (better for formatted viewing)
pluma movie_report
```

**What WhatWeb extracts:**

- Technology used (e.g., ASP.NET, CMS type).
- HTTP server version.
- HTTP request routing and destination.
- Author/organization metadata.
- Password field location in the HTML.
- Script sources, HTML headers, and much more — all consolidated in a single structured summary.

---

### Practical 4: Automated Vulnerability Scanning with ZAP (Zaproxy)

**Launch ZAP:**

```bash
zaproxy
```

**Steps:**

1. Launch ZAP → Click **Automated Scan**.
2. Enter the target URL (e.g., `www.govmovie.com`) → Click **Attack**.
3. ZAP will perform a spider crawl (mapping all accessible pages, directories, and links) and then automatically test for vulnerabilities.

**Key tabs to review in ZAP:**

| Tab          | What it shows                                                                                                                                            |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sites**    | All URLs/locations discovered on the target site                                                                                                         |
| **Spider**   | A full map of all crawled pages, directories, and file types found (including `sitemap.xml`, `robots.txt`, different HTTP methods used such as GET/POST) |
| **Alerts**   | Vulnerabilities found, color-coded by severity: **Red = High/Critical**, **Orange = Medium**, **Yellow = Low**                                           |
| **Messages** | Raw HTTP request/response pairs for each discovered URL                                                                                                  |

**Notable alert types from the demo:**

- SQL Injection vulnerability detected (marked **red**).
- ViewState without MAC signature (potential CSRF/integrity issue).
- Website location disclosure.
- Missing `X-Frame-Options` header.

---

### Practical 5: Directory Discovery with Gobuster

**Goal:** Discover hidden directories/folders within a web application using a word list (brute force enumeration).

**Command:**

```bash
gobuster dir -u http://www.govmovie.com -w /home/attacker/Desktop/common.txt
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `dir` | Directory brute-force mode |
| `-u` | URL of the target |
| `-w` | Path to the word list file |

**What it finds:** All accessible directories/folders within the web application (e.g., `/database/`, `/css/`, `/images/`, `/admin/`, etc.) — including ones not linked anywhere on the visible site.

---

### Practical 6: Directory Discovery with Dirsearch

**Goal:** Same as Gobuster but with a more polished interface and a built-in word list (no external word list required).

**Basic command:**

```bash
dirsearch -u www.govmovie.com
```

**Extension-specific scan:**

```bash
dirsearch -u http://www.govmovie.com -e aspx
```

_(Replace `aspx` with any file extension you want to focus on — `.php`, `.html`, `.js`, etc.)_

**Exclude specific status codes:**

```bash
dirsearch -u http://www.govmovie.com -e aspx -x 403
```

_(The `-x 403` flag hides all "403 Forbidden" results, showing only accessible pages)_

**What it shows:**

- Discovered directories/files, their HTTP status codes, and response sizes — all color-coded for easy reading.
- Extensions present within each directory (e.g., `.aspx`, `.css`, `.js`, `.php`).

**Status code reference:**
| Code | Meaning |
|---|---|
| `200` | OK — page exists and is accessible |
| `302` | Found — redirected (often indicates successful authentication in brute-force context) |
| `403` | Forbidden — page exists but access is denied |
| `404` | Not Found — page does not exist |

---

### Practical 7: Nmap Script-Based Enumeration

**Goal:** Use Nmap's built-in HTTP scripting engine to enumerate web application directories and services.

**Command:**

```bash
nmap --script http-enum -sV www.govmovie.com
```

**What it reveals:** Specific directories accessible on the target web server, including potentially sensitive locations like `/admin/`.

---

### Practical 8: Brute Force Attack with Burp Suite (Intruder)

**Goal:** Attempt to discover valid username/password combinations for a web application login form using automated password testing.

**Pre-requisite:** Configure the browser to use Burp Suite as its proxy:

- **Proxy IP:** `127.0.0.1`
- **Port:** `8080` (matching Burp Suite's listener port)

**Step-by-Step:**

1. **Launch Burp Suite** → Ensure **Intercept is ON** in the Proxy tab.
2. **Enter any credentials** in the target login form (e.g., username: `dev`, password: `pass`) and click Login.
3. **Burp Suite captures the request** — the raw HTTP POST request is visible, showing the username and password parameters.
4. **Right-click the request → Send to Intruder.**
5. **In Intruder → Positions tab:**
   - Set attack type to **Cluster Bomb** (tests all combinations of two payload sets).
   - **Clear** all default selections.
   - **Highlight the username value** → Click **Add §** to mark it as position 1.
   - **Highlight the password value** → Click **Add §** to mark it as position 2.
6. **In Intruder → Payloads tab:**
   - Payload Set 1 (username): **Load** a word list of possible usernames.
   - Payload Set 2 (password): **Load** a word list of possible passwords.
7. **Click Start Attack.**
8. **Analyze results:** Look for responses with status code **302 (Found)** — this indicates a successful login redirect (valid credentials found). All failed attempts typically return **200 OK** (the login page simply reloads).

**In the demo:** The attack identified valid credentials: username `admin`, password `qwerty@123`.

**After the attack:**

- Return to the browser → **Preferences → Proxy Settings → No Proxy** (restore normal browsing by removing the proxy configuration — otherwise, internet access will be affected).

---

### Practical 9: Parameter Tampering with Burp Suite

**Goal:** Test whether the web application improperly allows users to access other users' data by modifying URL parameters.

**Concept:** Web applications often use URL parameters to identify which resource to load (e.g., `?id=1` loads user profile #1). If the application doesn't validate that the current user is authorized to see the requested resource, changing `id=1` to `id=2` can expose another user's data.

**Step-by-Step:**

1. **Log in** to the web application as a legitimate user (e.g., username: `sam`).
2. **Navigate to your profile page.** Burp Suite intercepts the HTTP request.
3. **Forward requests** until the request containing the profile ID parameter appears (e.g., `GET /profile?id=1`).
4. **In Burp Suite → Params tab** (or Inspector panel in newer versions): Identify the `id` parameter and its current value (`1`).
5. **Change the value** of `id` from `1` to `2` (or `3`, etc.).
6. **Forward the modified request** to the server.
7. **Observe the response:** If the application is vulnerable, the profile page of a **different user** (user #2) loads — even though you are logged in as user #1.

**In the demo:** Logged in as `sam` (ID=1), changing the parameter to `id=2` revealed `John Smith`'s profile; changing to `id=3` revealed `Kitty`'s profile — demonstrating full unauthorized data access via parameter tampering.

**Why this is serious:** Every page in a web application typically runs on parameters. If these aren't encrypted or access-controlled, any user can access any other user's data — or navigate to any internal page — simply by modifying parameter values in the URL or intercepted request.

---

### Practical 10: XSS Detection with PWN_XSS Tool

**Goal:** Automatically detect XSS vulnerabilities across a web application.

**Command:**

```bash
python3 pwn_xss.py -u http://testphp.vulnweb.com
```

**How it works:** The tool systematically injects test XSS payloads (scripts) into all input points on the target site and checks whether any are executed by the browser. If a script runs (evidenced by a pop-up alert appearing), an XSS vulnerability exists at that location.

**Results interpretation:**

- **Pop-up alert appears** = XSS vulnerability confirmed at that input point.
- **No alert / clean result** = no XSS found (as seen on the protected test domains).

**In the demo:** The public testing site `testphp.vulnweb.com` yielded multiple XSS detections (marked "Critical"), with pop-up alerts appearing in many sections — confirming injected scripts were executing. The instructor's own secured site showed zero XSS alerts.

---

### Practical 11: CSRF Testing with WPScan (WordPress)

**Goal:** Scan a WordPress installation for vulnerabilities including CSRF, authentication issues, weak hashing, and outdated plugins.

**Pre-requisite:** Register for a free API token at the WPScan website.

**Command:**

```bash
wpscan --api-token [YOUR_API_TOKEN] -u http://[TARGET_IP]:8080/nva/ --plugins-detection aggressive --enumerate vp
```

**Flag breakdown:**
| Flag | Meaning |
|---|---|
| `--api-token` | Your registered WPScan API token (blurred in demo for security) |
| `-u` | Target URL |
| `--plugins-detection aggressive` | Aggressively scan for installed plugins |
| `--enumerate vp` | Enumerate vulnerable plugins specifically |

**What WPScan found in the demo:**

- **79 vulnerabilities** associated with the WordPress installation.
- **Bulk disclosure** issues.
- **Improper authentication** implementation.
- **Weak hashing** for stored passwords.
- **Cross-Site Scripting (XSS)** vulnerabilities.

**CSRF demonstration (using the WordPress vulnerability):**

1. A malicious script was crafted that would change a WordPress post's content when clicked.
2. The script link was sent to a logged-in user (simulated).
3. When the user clicked the link, the content of their post was **changed without their knowledge or action** — demonstrating a CSRF exploit in action.
4. The attacker did not need the victim's credentials — they simply needed the victim to click the malicious link while logged in.

---

### Practical 12: Load Balancer Detection

**Goal:** Identify whether a target uses load balancers (multiple servers distributing traffic) and how many.

**Tool 1 — DIG (DNS Load Balancer):**

```bash
dig [target-domain]
```

- If multiple different IP addresses are returned in the **ANSWER SECTION** for the same domain, these are load balancer IPs.
- In the demo: **6 different IP addresses** were returned for the same domain, indicating 6 load balancers.

**Tool 2 — LBD (Load Balancing Detector):**

```bash
lbd [target-domain]
```

- Checks both **DNS-based load balancing** and **HTTP-based load balancing**.
- Reports whether each type is detected/not detected.

> **Why this matters forensically/for security testing:** Knowing the number and type of load balancers helps map the full attack surface and understand how the target infrastructure distributes traffic.

---

## 10. Tools Reference Summary

| Tool              | Category               | Key Use                                                                       |
| ----------------- | ---------------------- | ----------------------------------------------------------------------------- |
| **Nmap**          | Recon / Scanning       | Port scanning, OS detection, service fingerprinting, script-based enumeration |
| **WhatWeb**       | Recon                  | Technology fingerprinting — server, CMS, frameworks, JS libraries             |
| **Telnet**        | Recon                  | Active banner grabbing — server/technology version identification             |
| **Gobuster**      | Recon                  | Directory brute-forcing with custom word lists                                |
| **Dirsearch**     | Recon                  | Directory discovery with built-in word list and extension filtering           |
| **DIG / LBD**     | Recon                  | Load balancer detection                                                       |
| **Shodan**        | Recon                  | Internet-connected device discovery                                           |
| **ZAP (Zaproxy)** | Scanning               | Automated web application vulnerability scanning + spider crawling            |
| **Nikto**         | Scanning               | Web server vulnerability detection                                            |
| **Acunetix**      | Scanning               | Automated commercial website security scanner                                 |
| **WPScan**        | Scanning               | WordPress-specific vulnerability and plugin scanner                           |
| **Burp Suite**    | Interception / Testing | HTTP proxy, request interception, brute force (Intruder), parameter tampering |
| **SQLMap**        | Exploitation           | Automated SQL injection exploitation                                          |
| **Metasploit**    | Exploitation           | Multi-purpose exploitation framework                                          |
| **BeEF**          | Exploitation           | Browser exploitation for XSS/client-side attacks                              |
| **PWN_XSS**       | Detection              | Automated XSS vulnerability detection                                         |

---

## 11. Key Takeaways

1. **Web applications ≠ websites** — web apps are interactive software hosted on servers, accessible via browser AND installable as phone apps; websites are static page-based collections accessible only via browser.

2. **All social media platforms are web applications** — Instagram, Facebook, Twitter, YouTube are all web apps. Security vulnerabilities in web applications therefore apply equally to social media, banking portals, e-commerce, and any other platform.

3. **The five most critical web app vulnerabilities** (covered here) are: SQL Injection (database manipulation), XSS (client-side script injection), CSRF (forged user requests), Broken Authentication, and Security Misconfiguration — all of which have well-known defenses that must be properly implemented.

4. **The 4-step testing process** — Information Gathering → Vulnerability Scanning → Exploitation → Reporting — mirrors a professional penetration testing engagement and should always be performed with **written authorization**.

5. **Parameters are attack vectors** — URL and form parameters that store user-identifying values (e.g., `?id=1`) must be access-controlled and ideally encrypted; failure to validate authorization allows any user to access any other user's data by simply changing the parameter value.

6. **Burp Suite is the central tool** for web application security testing — it intercepts and allows modification of any HTTP request/response in real time, enabling brute force, parameter tampering, session analysis, and much more.

7. **Automated tools are only the starting point** — tools like ZAP and Nikto can detect common vulnerabilities automatically, but manual testing (e.g., with Burp Suite) is required for more complex/logic-based vulnerabilities.

8. **Reporting is as important as testing** — a penetration test without a clear, actionable, categorized report (referencing OWASP Top 10) provides little value to the organization; screenshots, severity ratings, and specific remediation steps are essential.

9. **Bug Bounty Programs are the ethical path** for testing real-world platforms — companies like Facebook, Google, and Microsoft officially invite researchers to find and report vulnerabilities in exchange for monetary rewards, providing a fully legal path to practical experience.

10. **No unauthorized testing** — IT Act 2000, Section 66 makes unauthorized access to computer systems a criminal offense carrying up to 3 years imprisonment and/or ₹5 lakh fine. Always obtain written permission before testing any system you do not own.

---

_These notes cover web application security as a module within the broader ethical hacking curriculum. They complement the Digital Forensics course notes (Modules 1 & 2 + Practicals 1 & 2) — understanding how web applications are attacked is directly relevant to forensic investigation of cyber incidents involving web-based systems._
