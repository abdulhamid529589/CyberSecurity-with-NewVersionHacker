# Footprinting & Reconnaissance — Information Gathering (In-Depth Notes)

## https://youtu.be/8JaX97bdOAM

## What It Is, Why It Matters, Types, Methods, Google Dorking, Shodan, Social Media OSINT, and Tools

> **Course:** NewVersionHacker
> **Topic:** Footprinting and Reconnaissance
> **Disclaimer:** All techniques described here are strictly for **educational purposes only**. Never perform information gathering against systems, people, or organizations without explicit written authorization. Unauthorized reconnaissance is illegal in most jurisdictions.

---

## Table of Contents

1. [What Is Information Gathering / Footprinting & Reconnaissance?](#1-what-is-information-gathering--footprinting--reconnaissance)
2. [Why Information Gathering Is the First Step](#2-why-information-gathering-is-the-first-step)
3. [Types of Information Gathering](#3-types-of-information-gathering)
4. [Why Information Gathering Is Important — Key Benefits](#4-why-information-gathering-is-important--key-benefits)
5. [Methods of Information Gathering](#5-methods-of-information-gathering)
6. [Method 1 — Footprinting Through Search Engines](#6-method-1--footprinting-through-search-engines)
7. [Google Dorking — Advanced Search Operators](#7-google-dorking--advanced-search-operators)
8. [Google Advanced Search](#8-google-advanced-search)
9. [Method 2 — Shodan: The Hacker Search Engine](#9-method-2--shodan-the-hacker-search-engine)
10. [Method 3 — Social Media OSINT](#10-method-3--social-media-osint)
11. [Method 4 — Geolocation Tools (Google Earth)](#11-method-4--geolocation-tools-google-earth)
12. [What Information Can Be Gathered?](#12-what-information-can-be-gathered)
13. [Information Gathering in the Full Ethical Hacking Methodology](#13-information-gathering-in-the-full-ethical-hacking-methodology)
14. [Why This Matters for Cybersecurity](#14-why-this-matters-for-cybersecurity)
15. [Quick Revision Table](#15-quick-revision-table)
16. [Self-Test Questions](#16-self-test-questions)

---

## 1. What Is Information Gathering / Footprinting & Reconnaissance?

**Two names, one concept:**

- **Common/informal name:** Information Gathering
- **Technical name:** Footprinting and Reconnaissance (often abbreviated as "Footprinting and Recon")

### Definition

> Information Gathering is the systematic process of collecting **all available information about a target** — whether a system, network, organization, or individual — before attempting any attack or security assessment. It is the **first and most critical step** in ethical hacking and penetration testing.

### What "target" means

A target in ethical hacking can be:

- A **computer system or server** (IP address, OS, running services)
- A **network** (topology, open ports, connected devices)
- An **organization** (infrastructure, employees, technologies used)
- A **person** (contact details, social media presence, location, employer)

### What information is collected

For a system/network target:

- IP addresses and network ranges
- Open ports and services running on them
- Operating system type and version
- Known vulnerabilities and weaknesses
- Web technologies in use

For a person/organization target:

- Full name, date of birth, email, phone number
- Job title, employer, colleagues
- Physical address, location
- Social media accounts and activity
- Devices used (phone type, laptop brand/model)

---

## 2. Why Information Gathering Is the First Step

Information Gathering is not just the first step — it is the **foundation on which the entire attack or assessment is built**. The professional hacker analogy from the lecture illustrates this perfectly:

### Unprofessional hacker (no information gathering)

- Goes straight to trying random attacks
- Wastes enormous amounts of time
- 90% of attacks miss or fail — wrong tools for the wrong OS, wrong exploit for the wrong service
- **20 days of failed attempts**

### Professional hacker (with information gathering)

1. First discovers the target's IP address
2. Scans open ports (using Nmap or similar)
3. Identifies the operating system (Windows, Linux, etc.)
4. Discovers specific vulnerabilities in that OS/version
5. Selects the precise attack for that specific vulnerability
6. **Successful attack in 2 days**

The difference between 20 days of failure and 2 days of success is entirely due to information gathering.

```
Without Info Gathering:
Random attacks → Wrong tools → Wasted time → 90% failure rate

With Info Gathering:
Known target → Known OS → Known vulnerabilities → Accurate attack → 99% accuracy
```

---

## 3. Types of Information Gathering

Information gathering is divided into two fundamental types based on how the information is collected:

---

### Type 1 — Passive Information Gathering

**Definition:** Collecting information about a target **without directly interacting with or contacting the target system/person**. The target does not know reconnaissance is being performed.

**How it's done:**

- Searching the target on Google, Bing, or other search engines
- Browsing the target's public social media profiles
- Viewing publicly available WHOIS records
- Looking at third-party databases and news articles about the target
- Using tools like Shodan that index publicly exposed internet devices

**Key characteristic:** The target has **zero awareness** that someone is gathering information about them — because you are only viewing publicly available information from external sources, never touching the target's systems.

**Example:** Looking up a company's employees on LinkedIn, reading their blog, checking what technologies their website uses — all without ever sending a single packet to their servers.

---

### Type 2 — Active Information Gathering

**Definition:** Collecting information by **directly interacting with the target** — sending probes, queries, or requests to the target's systems. The target may detect that someone is scanning or probing them.

**How it's done:**

- Port scanning (Nmap sends packets to the target's IP)
- DNS queries directed at the target's DNS servers
- Sending direct messages or calls to gather social engineering information
- Banner grabbing from services running on the target
- Network enumeration

**Key characteristic:** Packets are actually **sent to the target system** — this means the activity may appear in the target's logs. This creates a legal and ethical requirement to have proper authorization before performing active reconnaissance.

**Comparison:**

| Aspect                  | Passive                            | Active                                |
| ----------------------- | ---------------------------------- | ------------------------------------- |
| Target awareness        | No — target doesn't know           | Possible — activity may be logged     |
| Interaction with target | None — only external sources       | Direct — packets sent to target       |
| Legal risk              | Lower — public data                | Higher — requires authorization       |
| Examples                | Google search, WHOIS, social media | Nmap scan, banner grabbing, DNS query |

---

## 4. Why Information Gathering Is Important — Key Benefits

### 1. Time Saving

Knowing what you're dealing with before attacking eliminates wasted effort. The professional hacker's 2-day success vs. 18 days of failure demonstrates this directly.

### 2. Easy Processing and Planning

With target information in hand, you can plan the exact sequence of steps and select the right tools upfront, rather than guessing and adjusting constantly.

### 3. Accuracy of Attacks

When you know the OS, services, and vulnerabilities, every attack you attempt is targeted and relevant. Without this, you're firing randomly and hoping something lands.

### 4. Reduced Detection Risk

Targeted, precise attacks generate far less noise and anomalous traffic than random, broad attacks — making detection less likely.

### 5. Scope Definition

Information gathering defines exactly what is in scope for testing and what systems are present — essential for both offensive and defensive security work.

---

## 5. Methods of Information Gathering

The lecture covers four primary methods of information gathering:

1. **Search Engine Footprinting** — using standard and specialized search engines
2. **Advanced Search / Google Dorking** — using advanced operators to extract specific information
3. **Social Media OSINT** — gathering information from social platforms
4. **Geolocation Tools** — using mapping tools for physical location intelligence

Additional methods exist (WHOIS lookup, DNS enumeration, email harvesting, Maltego, etc.) but are covered in advanced course modules.

---

## 6. Method 1 — Footprinting Through Search Engines

### Standard Search Engines

All major search engines can be used for basic information gathering:

| Search Engine  | URL            | Notes                                                                                      |
| -------------- | -------------- | ------------------------------------------------------------------------------------------ |
| **Google**     | google.com     | Most comprehensive index; supports advanced operators (Google Dorking)                     |
| **Bing**       | bing.com       | Microsoft's search engine; different index than Google — sometimes finds different results |
| **DuckDuckGo** | duckduckgo.com | Privacy-focused; no tracking; popular in Linux/security communities                        |
| **Censys**     | censys.io      | Specialized for internet-connected devices and certificates; advanced                      |
| **Shodan**     | shodan.io      | The "hacker's search engine" — indexes internet-facing devices (see Section 9)             |

### Why multiple search engines?

Each search engine has a different index and different indexing frequency. Information that doesn't appear in Google may appear in Bing, and vice versa. Professional OSINT includes searching multiple engines for comprehensive coverage.

---

## 7. Google Dorking — Advanced Search Operators

**Google Dorking** (also called Google Hacking) is the use of **advanced Google search operators** to extract specific, targeted information from Google's index that would otherwise be buried in irrelevant results.

These operators are built into Google's search syntax and are legitimate features — but they can be used to find things website owners didn't intend to expose publicly (exposed directories, sensitive files, etc.).

---

### Operator 1 — `filetype:` (File Type Filter)

**Purpose:** Restrict search results to a specific file format.

**Syntax:**

```
hacker book filetype:pdf
```

**Effect:** Only PDF files matching "hacker book" are returned. No HTML pages, no DOC files, no images.

**Other file types:**

```
report filetype:xls        (Excel files)
presentation filetype:pptx  (PowerPoint files)
config filetype:xml         (XML configuration files)
passwords filetype:txt      (Text files — often finds exposed credential lists)
```

**Why it matters for security:** Finding `passwords filetype:txt` or `config filetype:xml site:targetcompany.com` can expose accidentally published sensitive files.

---

### Operator 2 — `intitle:` (Single Word in Page Title)

**Purpose:** Restrict results to pages where the search term appears in the **HTML page title** (the text shown in browser tabs).

**Syntax (one word):**

```
intitle:Devendra
```

**Effect:** Only pages with "Devendra" in their title are returned.

**Limitation:** For a single word only. Using multiple words with `intitle:` applies the title filter only to the first word.

---

### Operator 3 — `allintitle:` (Multiple Words in Page Title)

**Purpose:** Require that **all specified words** appear in the page title.

**Syntax (multiple words):**

```
allintitle:Devendra Modi India
```

**Effect:** Only pages where "Devendra," "Modi," AND "India" all appear in the title.

**When to use:** When you need several specific words to all be present in the title — for targeted person or topic searches.

---

### Operator 4 — `inurl:` (Single Word in URL)

**Purpose:** Restrict results to pages where the search term appears in the **URL itself** (not just the content).

**Syntax:**

```
inurl:newversionhacker
```

**Effect:** Only pages whose URL contains "newversionhacker" are returned.

**Security use:** `inurl:admin` finds admin panels; `inurl:login` finds login pages; `inurl:config` may find exposed configuration pages.

---

### Operator 5 — `allinurl:` (Multiple Words in URL)

**Purpose:** Require that **all specified words** appear in the URL.

**Syntax:**

```
allinurl:India PM Modi
```

**Effect:** Only URLs containing all three words: "India," "PM," and "Modi."

---

### Operator 6 — `intext:` (Single Word in Page Body)

**Purpose:** Restrict results to pages where the search term appears in the **body text** of the page (not just the title or URL).

**Syntax:**

```
intext:newversionhacker
```

**Effect:** Only pages where "newversionhacker" is written somewhere in the page content.

---

### Operator 7 — `allintext:` (Multiple Words in Page Body)

**Purpose:** Require that **all specified words** appear in the page body text.

**Syntax:**

```
allintext:hacker India book
```

**Effect:** Only pages where all three words appear together in the text content.

---

### Operator 8 — `site:` (Restrict to Specific Domain)

**Purpose:** Limit results to a specific website or domain.

**Syntax:**

```
site:youtube.com
site:gov.in
```

**Effect:** Only results from the specified domain are shown.

**Security use:** `site:targetcompany.com filetype:pdf` finds all PDF files on that company's website; `site:targetcompany.com inurl:admin` looks for admin panels on their domain.

---

### Operator 9 — `-` (Exclude Terms)

**Purpose:** Remove specific terms from search results using a minus sign.

**Syntax:**

```
hacker -facebook
site:example.com -login
```

**Effect:** All results containing "facebook" are excluded from the hacker search.

---

### Operator 10 — `*` (Wildcard)

**Purpose:** Acts as a wildcard, matching any word in that position.

**Syntax:**

```
face*
hack*
```

**Effect:**

- `face*` returns Facebook, FaceTime, FaceApp, and anything else starting with "face"
- `hack*` returns hacker, hacking, hackathon, etc.

**Use:** When you know part of a search term but not the complete word.

---

### Operator 11 — `index of` (Directory Listing Exploit)

**Purpose:** Find websites that have **accidentally exposed their directory listing** — essentially showing all the files and folders on their web server like an open book.

**Syntax:**

```
index of /src
index of /admin
index of /backup
```

**Effect:** Finds websites with open directory listings for those paths. When a web server's directory listing is enabled and not protected, visiting the URL shows a file browser-style view of all files in that folder.

**Why it's significant:** A website with an open directory listing is like a building with all the doors open and labeled — anyone walking by can see and access everything inside. This is a genuine misconfiguration vulnerability.

**Example:** Searching `index of /src` and finding a result might show the source code, configuration files, database backups, or other sensitive content that should never be publicly accessible.

> **Important:** Only use this on websites you own or have explicit authorization to test. Accessing files through an exposed directory listing on someone else's server without permission is unauthorized access.

---

### Google Dorking Operators Quick Reference

| Operator      | Syntax Example                 | What It Does                         |
| ------------- | ------------------------------ | ------------------------------------ |
| `filetype:`   | `report filetype:pdf`          | Restrict to specific file type       |
| `intitle:`    | `intitle:admin`                | Single word must be in page title    |
| `allintitle:` | `allintitle:admin login panel` | All words must be in page title      |
| `inurl:`      | `inurl:login`                  | Single word must be in URL           |
| `allinurl:`   | `allinurl:admin config`        | All words must be in URL             |
| `intext:`     | `intext:password`              | Single word must be in page body     |
| `allintext:`  | `allintext:username password`  | All words in page body               |
| `site:`       | `site:example.com`             | Results only from this domain        |
| `-`           | `hacker -facebook`             | Exclude this term from results       |
| `*`           | `hack*`                        | Wildcard — any word in that position |
| `index of`    | `index of /backup`             | Find exposed directory listings      |

---

## 8. Google Advanced Search

Google provides a GUI-based advanced search interface at `google.com/advanced_search` that exposes these same operators visually, making them easier to combine without memorizing syntax.

Key fields available in Google Advanced Search:

| Field                    | What You Can Specify                                                 |
| ------------------------ | -------------------------------------------------------------------- |
| **All these words**      | All specified words must appear                                      |
| **This exact phrase**    | Exact phrase match (equivalent to quotation marks)                   |
| **Any of these words**   | At least one of the listed words must appear                         |
| **None of these words**  | Exclude pages containing these words                                 |
| **Numbers ranging from** | Specify a numeric range (useful for price, date, port numbers)       |
| **Language**             | Filter results by the language they're written in                    |
| **Region/Country**       | Filter results to a specific country's websites                      |
| **Last update**          | Only show results updated within a specific time range               |
| **Site/Domain**          | Equivalent to `site:` operator                                       |
| **Terms appearing**      | Where the terms must appear (anywhere, title, URL, links, body text) |
| **File type**            | Equivalent to `filetype:` operator                                   |

This is the recommended approach for complex multi-condition searches where multiple operators need to be combined.

---

## 9. Method 2 — Shodan: The Hacker Search Engine

**Shodan** (`shodan.io`) is a specialized search engine that continuously scans and indexes **internet-connected devices** — servers, routers, webcams, industrial control systems, medical devices, and anything else with a public IP address.

### How Shodan differs from Google

| Google                         | Shodan                                             |
| ------------------------------ | -------------------------------------------------- |
| Indexes web page content       | Indexes internet-facing devices and their services |
| Returns websites and documents | Returns IP addresses, open ports, service banners  |
| Used for finding information   | Used for finding exposed infrastructure            |

### What Shodan reveals

When you search Shodan, each result shows:

- **IP address** of the device
- **Open ports** and services running on each port
- **Service banners** (what the service says when you connect)
- **Geographic location** (country, city, ISP)
- **Organization** that owns the IP range
- **Operating system** (if detectable)
- **Known vulnerabilities** associated with the detected software versions

### Practical example from the lecture

**Search:** `CCTV` or `webcam`

**Result:** Thousands of live, internet-facing CCTV cameras from around the world — each showing:

- The camera's public IP address
- Open ports (e.g., TCP port 8080, TCP port 2000)
- Whether a web interface or login page is accessible

If a CCTV camera has a login page exposed publicly on port 8080 and is still running default credentials (very common in IoT devices), it can potentially be accessed without authorization — which is exactly the kind of vulnerability Shodan surfaces.

**Search variations used in the lecture:**

```
CCTV
webcam
live camera India
```

### Why Shodan is so powerful

Shodan essentially maps the **exposed attack surface of the entire internet in real time**. Security researchers and SOC analysts use it to:

- Find their own organization's exposed assets they may not know about
- Identify internet-facing systems with known vulnerabilities
- Understand what attack surfaces are visible from outside their network

Attackers use it to find vulnerable targets — which is why understanding Shodan is essential for defense.

> **Note:** The lecture also mentions **Censys** and **DuckDuckGo** as alternative specialized search engines with similar functionality for different use cases.

---

## 10. Method 3 — Social Media OSINT

**OSINT = Open Source Intelligence** — gathering information from publicly available sources.

Social media platforms are among the richest sources of freely available personal information:

### What social media reveals

| Platform      | Type of Information Available                                                                                                 |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Facebook**  | Real name, date of birth, hometown, current city, employer, relationship status, friend network, photos, check-ins, interests |
| **LinkedIn**  | Full professional history, current employer, job title, colleagues, skills, education, email (sometimes)                      |
| **Instagram** | Photos, location tags, daily routine, frequented locations, social circle                                                     |
| **Twitter/X** | Opinions, current activities, real-time location (via geotagged tweets), professional network                                 |
| **YouTube**   | Channel details, upload history, linked accounts, comments                                                                    |

### Common OSINT findings from social media

- **Date of birth** — often publicly posted for birthday wishes
- **Location** — check-ins, tagged locations, background of photos
- **Routine** — regular posts reveal daily habits, working hours, travel patterns
- **Social circle** — friends, colleagues, family — valuable for social engineering
- **Employer and job details** — organizational structure, insider information
- **Email address** — sometimes in profile or linked accounts
- **Device information** — photos sometimes contain EXIF metadata with device model, and photos of the device itself

### Social engineering risk

Hackers use social media information to craft convincing phishing emails or pretexting scenarios. Examples from the lecture:

- Date of birth visible on Facebook → used to answer security questions or for identity verification fraud
- Location tags showing you're traveling → house known to be empty = physical security risk
- Credit card fraud via social engineering using publicly available personal details

### AI and social media

The lecture notes that with the rise of AI tools, the speed and scale at which social media information can be scraped, correlated, and weaponized has increased dramatically. AI tools can now automatically gather, process, and analyze social media data at scale to build detailed profiles of individuals.

---

## 11. Method 4 — Geolocation Tools (Google Earth)

**Google Earth** (`earth.google.com`) provides detailed satellite and 3D imagery of physical locations, which is used in information gathering for:

- **Physical reconnaissance** — identifying the layout of a target building/facility
- **Location verification** — confirming a person or organization is actually located where claimed
- **Surveillance planning** — identifying entry/exit points, security cameras, parking, surrounding buildings
- **Tracking targets** — once a location is known from social media or other sources, Google Earth provides detailed visual context

### Practical use in ethical hacking

If an IP address is traced to a physical location (via WHOIS, geolocation databases, or other means), Google Earth can provide a detailed 3D view of that location — buildings, surroundings, access points.

The lecture demonstrates searching "Nagpur" in Google Earth to show a detailed 3D view of the city, illustrating how a specific address can be visualized in detail once location information is obtained.

---

## 12. What Information Can Be Gathered?

A comprehensive information gathering session can yield:

### For a target system/network:

| Category             | Information                                                                         |
| -------------------- | ----------------------------------------------------------------------------------- |
| **Network**          | IP address, IP range, network topology, ASN, ISP                                    |
| **Ports & Services** | Which ports are open, which services run on each port, service versions             |
| **Operating System** | OS type, version, patch level                                                       |
| **Vulnerabilities**  | Known CVEs affecting detected OS/service versions                                   |
| **Web Technologies** | CMS (WordPress, Joomla), server software (Apache, Nginx, IIS), programming language |
| **DNS**              | Domain records, subdomains, mail servers (MX records)                               |
| **Certificates**     | SSL/TLS certificate details, validity, organization                                 |

### For a target person/organization:

| Category              | Information                                            |
| --------------------- | ------------------------------------------------------ |
| **Personal**          | Full name, date of birth, phone number, email, address |
| **Professional**      | Employer, job title, colleagues, office location       |
| **Digital footprint** | Social media accounts, usernames, online activity      |
| **Devices**           | Phone model, laptop brand, OS used                     |
| **Location**          | Home address, work address, frequent locations         |
| **Relationships**     | Family, friends, professional network                  |

---

## 13. Information Gathering in the Full Ethical Hacking Methodology

Information Gathering is Step 1 of the standard ethical hacking methodology. It feeds directly into every subsequent step:

```
Step 1: INFORMATION GATHERING / FOOTPRINTING & RECON
           ↓
Step 2: SCANNING & ENUMERATION
        (Nmap port scanning, service enumeration — uses IPs found in Step 1)
           ↓
Step 3: VULNERABILITY ANALYSIS
        (Finding weaknesses — targets the services found in Step 2)
           ↓
Step 4: EXPLOITATION
        (Attacking vulnerabilities — uses OS/service info from Steps 1–3)
           ↓
Step 5: POST-EXPLOITATION
        (Maintaining access, pivoting — depends on system knowledge from Step 1)
           ↓
Step 6: REPORTING
        (Documenting everything — references all information from previous steps)
```

**Nothing works without Step 1.** Every subsequent step depends on the quality and completeness of information gathered in the reconnaissance phase.

---

## 14. Why This Matters for Cybersecurity

### For offensive security (Red Team / Pen Testing):

- Information gathering is always the first phase of any authorized penetration test
- The scope, attack vectors, and strategy are entirely determined by what recon reveals
- Tools like Shodan, Maltego, theHarvester, and Recon-ng are standard pentest toolkit items
- CEH, OSCP, and other certifications test recon skills explicitly

### For defensive security (Blue Team / SOC):

- Understanding what information is publicly available about your own organization helps you reduce your attack surface
- SOC analysts use OSINT to investigate threat actors and understand attack campaigns
- "Attack Surface Management" tools continuously monitor what your organization exposes to the internet — essentially automated defensive footprinting

### For OSINT investigations (Digital Forensics):

- Digital forensic investigators use OSINT to identify suspects, trace online activity, and build evidence
- Social media OSINT is frequently used in both criminal investigations and civil legal cases

### For personal security:

- Knowing how easily your information is gathered from social media is a strong motivator to review your privacy settings, remove birth dates from public profiles, disable location tagging on photos, and limit what you share publicly

---

## 15. Quick Revision Table

| Concept                           | One-line Summary                                                                         |
| --------------------------------- | ---------------------------------------------------------------------------------------- |
| **Information Gathering**         | Collecting all available information about a target before any attack or assessment      |
| **Footprinting and Recon**        | Technical name for information gathering — Step 1 of ethical hacking methodology         |
| **Passive Information Gathering** | Collecting info without interacting with the target — target is unaware                  |
| **Active Information Gathering**  | Collecting info by directly probing the target — may be detectable                       |
| **Google Dorking**                | Using advanced Google search operators to extract specific targeted information          |
| **`filetype:`**                   | Restrict Google results to a specific file type (PDF, XLS, PPT, etc.)                    |
| **`intitle:` / `allintitle:`**    | Require word(s) to appear in the page title                                              |
| **`inurl:` / `allinurl:`**        | Require word(s) to appear in the URL                                                     |
| **`intext:` / `allintext:`**      | Require word(s) to appear in the page body text                                          |
| **`site:`**                       | Restrict results to a specific domain                                                    |
| **`-`**                           | Exclude a term from search results                                                       |
| **`*`**                           | Wildcard — matches any word in that position                                             |
| **`index of`**                    | Find websites with exposed directory listings                                            |
| **Shodan**                        | Search engine that indexes internet-facing devices — shows open ports, services, banners |
| **Social Media OSINT**            | Gathering personal/organizational information from public social media profiles          |
| **Google Earth**                  | Geolocation and physical reconnaissance tool using satellite imagery                     |
| **OSINT**                         | Open Source Intelligence — gathering information from publicly available sources         |

---

## 16. Self-Test Questions

1. What is the difference between "Information Gathering" and "Footprinting and Reconnaissance"? Are they the same thing?
2. Explain why a professional hacker who spends time on information gathering is more successful than one who doesn't — use the time comparison example.
3. What is the difference between passive and active information gathering? Give a real-world example of each.
4. Write a Google Dork to find PDF files on `example.com` that contain the word "password" in the body text.
5. What is the difference between `intitle:` and `allintitle:`? When would you use each?
6. What does the `index of` Google Dork reveal, and why is finding it on a website a security problem?
7. What is Shodan, how does it differ from Google, and what kinds of devices does it index?
8. List five categories of information that can be gathered from a person's social media profiles.
9. How can Google Earth be used in the context of information gathering?
10. Draw the 6-step ethical hacking methodology and explain how information gathering feeds into each subsequent step.

---

## What's Next

Information gathering is a deep topic — the lecture notes this explicitly, saying the chapter alone takes 3–4 days to fully cover. This lecture provided an introduction and demo. Advanced topics in subsequent sessions include:

- **DNS Enumeration** — enumerating subdomains, mail servers, and zone transfers
- **WHOIS Lookups** — identifying domain registrant details, registration dates, contact info
- **Maltego** — graphical OSINT tool that visualizes relationships between entities (people, domains, IPs, companies)
- **theHarvester** — automated email, username, and subdomain enumeration tool
- **Recon-ng** — a full-featured OSINT framework similar to Metasploit but for reconnaissance
- **Email Header Analysis** — extracting IP addresses and mail server paths from email headers
- **Nmap** — the primary active scanning tool (crosses from passive recon into active scanning)

_Information gathering is the skill that separates a methodical, professional security tester from someone who is just trying random attacks. Mastering OSINT and reconnaissance is foundational to everything else in ethical hacking — every subsequent step depends entirely on the quality of information collected here._
