# Social Engineering — Complete Notes

## Human Hacking: Types, Techniques, Attacks, Practical Phishing with SET, and Defenses

> **Course:** NewVersionHacker
> **Topic:** Social Engineering — The Human Element of Cybersecurity
> **Disclaimer:** All content is strictly for **educational purposes only**. The techniques described here are presented so you can recognize and defend against them. Never use social engineering against individuals or organizations without explicit written authorization.

---

## Table of Contents

1. [What Is Social Engineering?](#1-what-is-social-engineering)
2. [Phases of a Social Engineering Attack](#2-phases-of-a-social-engineering-attack)
3. [Three Types of Social Engineering](#3-three-types-of-social-engineering)
4. [Type 1 — Human-Based Social Engineering (10 Techniques)](#4-type-1--human-based-social-engineering-10-techniques)
5. [Type 2 — Computer-Based Social Engineering (6 Techniques)](#5-type-2--computer-based-social-engineering-6-techniques)
6. [Type 3 — Mobile-Based Social Engineering (4 Techniques)](#6-type-3--mobile-based-social-engineering-4-techniques)
7. [Practical — Phishing Attack Using SET (Social Engineering Toolkit)](#7-practical--phishing-attack-using-set-social-engineering-toolkit)
8. [Defense — Netcraft Browser Extension](#8-defense--netcraft-browser-extension)
9. [Why Social Engineering Works — The Psychology](#9-why-social-engineering-works--the-psychology)
10. [Social Engineering and Real-World Cybercrime](#10-social-engineering-and-real-world-cybercrime)
11. [Defenses Against Social Engineering](#11-defenses-against-social-engineering)
12. [Why This Matters for Cybersecurity](#12-why-this-matters-for-cybersecurity)
13. [Quick Revision Table](#13-quick-revision-table)
14. [Self-Test Questions](#14-self-test-questions)

---

## 1. What Is Social Engineering?

**Social Engineering** is the art of manipulating people into performing actions or divulging confidential information — without needing to hack any technical system.

In cybersecurity, it is also called **"Human Hacking"** — because instead of exploiting software vulnerabilities, the attacker exploits human psychology.

### Core definition

> Social Engineering is the use of **psychological manipulation, deception, and trust exploitation** to convince a target to reveal confidential information or take an action that benefits the attacker — bypassing technical security controls entirely.

### Real-world example

You receive a call:

> _"Congratulations! You have won a lottery of ₹2,00,000. To transfer the winnings, please share your OTP..."_

The call exploits **greed and urgency** — two powerful psychological triggers — to make you voluntarily hand over your banking OTP. No hacking of any computer was needed. The human was hacked.

### Four pillars of social engineering

| Pillar                                  | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Convincing people**                   | Building enough trust or urgency that the target is convinced to share what they normally wouldn't                                |
| **Psychological manipulation**          | Altering the target's mental state — creating fear, urgency, greed, or false trust — so they act against their own interests      |
| **Extracting confidential information** | The ultimate goal — obtaining passwords, OTPs, Aadhaar numbers, PAN cards, bank details, CVV, etc.                                |
| **Exploiting trust**                    | Using an established (real or fabricated) relationship to extract information the target would only share with someone they trust |

### Social Engineering vs. Computer Hacking

| Computer Hacking                           | Social Engineering                           |
| ------------------------------------------ | -------------------------------------------- |
| Exploits software/hardware vulnerabilities | Exploits human psychological vulnerabilities |
| Requires technical knowledge and tools     | Requires understanding of human psychology   |
| Can be prevented by patching software      | Prevented by awareness and training          |
| Leaves technical traces in logs            | Often leaves no technical evidence at all    |

---

## 2. Phases of a Social Engineering Attack

A structured social engineering attack follows a systematic process:

### Phase 1 — Research (Target Selection)

The attacker researches potential targets to find:

- Which company or organization to target
- What information is publicly available about it (OSINT — connects to the footprinting module)
- Who works there and what their roles are
- Any publicly visible discontent (employees who are unhappy, complain publicly, etc.)

**Key insight:** An employee who is publicly negative about their company is a much easier target than a loyal employee. Attackers specifically look for disgruntled employees because their resistance to manipulation is lower.

### Phase 2 — Target Selection

The attacker selects the specific person they will manipulate:

- Ideally someone with access to the information needed (admin, finance, HR)
- Preferably someone already identified as negative/disgruntled toward the organization
- Someone whose role means they handle sensitive data

### Phase 3 — Relationship Development

The attacker builds a relationship with the target:

- Establishing friendship, trust, or professional rapport over time
- Making the target feel comfortable enough to share information
- Could take days, weeks, or months depending on the target's caution level

**Note:** Many attacks skip this phase and use direct manipulation techniques (calls, emails, popups) without building any relationship.

### Phase 4 — Exploitation

The attacker uses the established relationship (or manipulation technique) to extract the target information:

- Casually asking for details within the context of the relationship
- Asking the target to perform an action (click a link, open a file, provide access)
- Leveraging the trust built in Phase 3 to lower the target's defenses

---

## 3. Three Types of Social Engineering

Social engineering is categorized based on the primary medium used:

| Type               | Medium                     | Technology Used                                  |
| ------------------ | -------------------------- | ------------------------------------------------ |
| **Human-Based**    | Direct human interaction   | Minimal — mostly conversation, physical presence |
| **Computer-Based** | Digital/internet platforms | High — websites, emails, software                |
| **Mobile-Based**   | Mobile devices and apps    | High — SMS, apps, phone calls                    |

---

## 4. Type 1 — Human-Based Social Engineering (10 Techniques)

Human-based attacks involve direct human interaction — person to person, with minimal or no technology. These are among the most effective attacks because they are hardest to detect technically.

---

### Technique 1 — Eavesdropping

**What it is:** Illegally listening to someone else's private conversation without their knowledge or consent.

**Examples:**

- Listening to someone discussing passwords or confidential projects on a phone call in a public space
- Positioning yourself near a conference room to overhear meetings
- Using audio recording devices to capture conversations

**Why it works:** People are less careful about what they say verbally compared to what they write digitally, especially in familiar/semi-private environments.

---

### Technique 2 — Vishing (Voice Phishing)

**Full form:** Voice + Phishing = Vishing

**What it is:** Conducting phishing attacks through telephone calls — calling a target and using social manipulation to extract sensitive information.

**Examples:**

- _"Hello, I'm calling from your bank. Your ATM card is about to be blocked. Please verify your card number and OTP to keep it active."_
- _"This is the IT department. We've detected suspicious activity on your account. Can you confirm your password?"_

**What's exploited:** Authority (fake bank employee), urgency (card about to expire), and trust (they called you, not the other way around).

---

### Technique 3 — Shoulder Surfing

**What it is:** Physically observing someone as they enter sensitive information — watching from behind or beside them without their knowledge.

**Examples:**

- Standing near an ATM and watching someone enter their PIN
- Looking over someone's shoulder as they type their password in an office or café
- Watching someone fill in a form with personal details

**Why it works:** Most people focus on the screen/keyboard and don't check their surroundings for observers.

**Defense:** Use your body to shield the keypad; use privacy screen protectors on laptops.

---

### Technique 4 — Piggybacking

**What it is:** An unauthorized person gaining entry to a secure area by using an authorized person — with the authorized person's knowledge (even if obtained through manipulation).

**How it works:**

- Attacker approaches a secure door alongside an authorized employee
- Claims to have forgotten their badge/ID card
- Makes a story ("I just started today," "I'm visiting the IT team") to convince the authorized person to let them in
- The authorized person, trying to be helpful, holds the door open

**Key distinction from tailgating:** In piggybacking, the authorized person knowingly lets the attacker in (even if deceived about their identity). In tailgating, the attacker enters without the authorized person's consent.

---

### Technique 5 — Elicitation

**What it is:** Extracting confidential information from a target through casual, seemingly innocent conversation — without the target realizing information is being extracted.

**How it works:**

- The attacker builds rapport through natural conversation
- Asks seemingly innocent questions that gradually reveal sensitive information
- The target never realizes they are being interviewed — it feels like normal conversation

**Example:**

> Attacker: "Hi! I work in building C — which team are you on?"
> Target: "Oh I'm in the finance department, accounts payable."
> Attacker: "Nice! Do you use SAP or Oracle for processing?"

The attacker just learned which department, what role, and which financial systems are in use — all through casual conversation.

---

### Technique 6 — Dumpster Diving

**What it is:** Searching through a target's discarded physical materials (trash/garbage) to find sensitive information.

**What attackers look for:**

- Discarded ATM receipts or bank statements
- Old electricity bills or utility bills (contains name, address, account number)
- Thrown-away credit/debit cards (even expired ones contain card numbers)
- Old Aadhaar/PAN card photocopies
- Printed emails or documents with company information
- Sticky notes with passwords
- Old employee ID cards

**Why it works:** People don't think of their garbage as a security risk. Documents are often thrown out without shredding.

**Defense:** Shred all documents before disposal; cut up old cards; don't throw away any document with personal information.

---

### Technique 7 — Impersonation

**What it is:** The attacker pretends to be someone they are not — a bank employee, IT support, government official, company executive — to gain trust and extract information.

**Examples:**

- _"This is the IT helpdesk. We need to verify your login credentials to fix a system issue."_
- _"I'm calling from HDFC Bank. Your account has been flagged for suspicious activity. Please verify your details."_
- _"Hi, I'm the new employee from the marketing team. Could you help me access the server room?"_

**What makes it work:** Humans naturally tend to trust people who present themselves as authorities or known entities. The impersonator provides just enough convincing detail to seem legitimate.

---

### Technique 8 — Tailgating

**What it is:** Physically entering a restricted/secure area without authorization by following closely behind someone who is authorized — without their knowledge or consent.

**How it differs from piggybacking:** In tailgating, the authorized person is completely unaware — the attacker simply follows close enough behind that the door closes after both of them have passed through. Sometimes the attacker carries boxes or equipment to seem like a legitimate delivery person.

**Common scenarios:**

- Wearing a fake badge that looks real enough for a casual glance
- Carrying boxes or equipment that make stopping to badge in look inconvenient
- Waiting near secure doors and entering with a group of authorized employees
- Dressing in a uniform (delivery, maintenance) to blend in

---

### Technique 9 — Reverse Social Engineering

**What it is:** The attacker positions themselves as an authority figure or expert such that the victim comes to _them_ for help — willingly sharing information or doing whatever the "expert" suggests.

**How it works:**

1. Attacker contacts victim and causes a problem (or the problem already exists)
2. Attacker presents themselves as the authority who can fix the problem
3. Victim, believing they've found the right expert, asks _them_ for advice and shares information to get help
4. Attacker extracts information while appearing to help

**Real example:**

- Caller: _"I'm calling from your bank's fraud prevention team. There was suspicious activity on your account."_
- Victim: _"Oh! What should I do?"_
- [The victim is now asking the attacker for help — classic reverse social engineering]
- Later, victim calls the "bank number" the attacker gave them and shares all their details

**Why it's particularly effective:** The victim seeks out the attacker, rather than being approached. This reversal makes the victim feel they are in control when they are actually being manipulated.

---

### Technique 10 — Diversion Theft

**What it is:** Redirecting a legitimate delivery or service person away from the correct destination, then substituting the attacker in their place to intercept information, packages, or gain access.

**Example:**

- A legitimate delivery person is coming to deliver a package to the target
- The attacker intercepts or redirects the delivery (changes the delivery address, sends the driver to a wrong location)
- The attacker then presents themselves at the original location as if they are the legitimate delivery person
- This enables physical access, document interception, or building infiltration

---

## 5. Type 2 — Computer-Based Social Engineering (6 Techniques)

Computer-based attacks use technology as the delivery mechanism. These are far more scalable than human-based attacks — a single attacker can target millions of people simultaneously.

---

### Technique 1 — Popup Windows

**What it is:** Fake popup alerts appearing while browsing, designed to alarm the user into taking immediate action.

**Common popup messages:**

- _"Your computer is infected with 5 viruses! Click here to remove them immediately."_
- _"Your password has been compromised. Click here to secure your account."_
- _"WARNING: Your internet connection is insecure. Update your security now."_

**What happens when clicked:** Redirects to a fake website that asks for login credentials, personal details, or payment information — all going to the attacker.

---

### Technique 2 — Chain Letters / Hoax Messages

**What it is:** Fraudulent messages circulated via WhatsApp, email, or social media offering fake rewards in exchange for forwarding the message to others.

**Common examples:**

- _"Forward this to 10 people and get a free Amazon voucher."_
- _"If you don't forward this message, your account will be deactivated."_
- Emotional/spiritual stories asking you to "share to spread good luck"

**Why they spread:** Exploit greed (free gift), fear (account deactivation), or emotional connection. Each person who forwards it spreads the attack further.

**What they actually do:** When recipients click embedded links, their IP address, browser info, and sometimes personal details are captured by the attacker.

---

### Technique 3 — Instant Message Phishing

**What it is:** Attackers use messaging platforms (Instagram DMs, WhatsApp, Telegram, Facebook Messenger) to contact targets with social engineering messages.

**Examples:**

- Fake customer support accounts asking for login credentials
- Messages claiming to be from friends with "urgent" requests for money
- Fake giveaway messages requiring "verification" through a link

**Why it works:** Messaging platforms feel informal and personal — people are less suspicious than with emails.

---

### Technique 4 — Spam Email

**What it is:** Unsolicited bulk emails sent to extract financial information or deliver malware.

**What spam emails contain:**

- Requests for social security numbers, banking details, login credentials
- Malicious attachments (Trojans, ransomware, keyloggers) disguised as legitimate files
- Fake links to login pages (credential harvesting)

**The long filename trick:** Attackers name malicious attachments with very long filenames:

```
Financial_Report_NewVersionHacker_Q3_2023_Updated_Final_IMPORTANT.exe
```

Human psychology causes people to read the beginning of the name ("Financial Report..."), assume it is legitimate, and never reach the `.exe` extension at the end that reveals it is an executable program.

---

### Technique 5 — Scareware

**What it is:** Fake security alerts designed to frighten users into taking immediate action.

**Characteristics:**

- Appear as alarming red popups while browsing
- Display urgent, scary messages: _"5 VIRUSES DETECTED — YOUR FILES WILL BE DELETED IN 60 SECONDS"_
- Create extreme urgency and panic
- Direct users to download fake "security software" (which is actually malware)

**Why it works:** When people are scared, their rational thinking is impaired. The urgency bypasses critical evaluation — people act first and think later.

**How to recognize it:** Legitimate antivirus software doesn't pop up in browsers demanding immediate action. These are always fake.

---

### Technique 6 — Phishing

**What it is:** Creating fake versions of legitimate websites or sending deceptive emails that appear to come from trusted organizations, designed to capture credentials.

This is covered in detail in the practical section below, as the entire practical demonstration focuses on phishing.

---

## 6. Type 3 — Mobile-Based Social Engineering (4 Techniques)

Mobile-based attacks exploit the ubiquity of smartphones and the apps people trust with their most sensitive data.

---

### Technique 1 — Publishing Malicious Apps

**What it is:** Uploading malware-containing applications to official app stores (Google Play, App Store) that appear legitimate.

**How it works:**

- Attacker creates an app that looks useful (game, utility, tool)
- Hides malicious code inside the app (spyware, keylogger, RAT)
- Submits to the app store
- Once downloaded by victims, the app silently collects and transmits data

---

### Technique 2 — Repacking Legitimate Apps (Cracked Apps)

**What it is:** Taking an existing legitimate paid application, decompiling it, inserting malicious code, and redistributing it as a "cracked" (free) version.

**The process:**

1. Attacker obtains a paid app (legally or otherwise)
2. Decompiles/reverses the app's code
3. Bypasses or replaces the license verification (cracks it)
4. Inserts malicious payload code (RAT, keylogger, spyware)
5. Repackages and distributes through unofficial channels
6. Users download the "free" version and install it

**Why people fall for it:** The app works exactly as expected — all features function correctly. The malicious component runs silently in the background.

**Warning:** Every cracked app is a security risk. The "free" version of a paid app costs you far more than the original price in stolen data.

---

### Technique 3 — Fake Security Applications

**What it is:** A multi-stage attack:

1. **Stage 1 (Damage):** Attacker first compromises the victim's phone (via malware or other means)
2. **Stage 2 (Alert):** A popup appears: _"Your banking app is insecure — your credentials may be compromised!"_
3. **Stage 3 (Hook):** The popup recommends downloading a specific "security app" from a linked page
4. **Stage 4 (Compromise):** The "security app" is actually a keylogger or RAT (Remote Access Trojan) — it records every keystroke including banking credentials
5. **Stage 5 (Theft):** Every time the victim logs into their banking app, the credentials go directly to the attacker

**The irony:** The victim downloads a fake "security" app that makes their phone drastically less secure.

---

### Technique 4 — SMS Phishing (Smishing)

**Full name:** SMS Phishing = Smishing

**What it is:** Phishing conducted via text messages (SMS) rather than email.

**Common messages:**

- _"You have won a bonus of ₹5,000! Click here to claim: http://fake-link.com"_
- _"Your SBI account has been suspended. Verify now: http://phishing-link.com"_
- _"Package held at customs. Pay ₹50 release fee: http://scam-site.com"_

**What the link does:** Redirects to a fake website that captures credentials, payment information, or installs malware.

**Also includes:** Voice calls (vishing) following up on SMS messages to add legitimacy.

---

## 7. Practical — Phishing Attack Using SET (Social Engineering Toolkit)

### What Is SET?

**SET (Social Engineering Toolkit)** is an open-source Python-based framework specifically designed to perform social engineering attacks for penetration testing. It is pre-installed on Kali Linux.

### Goal of This Practical

Clone a legitimate website → trick a victim into logging in → capture their credentials in plaintext.

### Step 1 — Launch SET

```bash
sudo setoolkit
```

SET opens with a menu interface.

### Step 2 — Select Social Engineering Attacks

```
Menu choice: 1  (Social-Engineering Attacks)
```

### Step 3 — Select Website Attack Vectors

```
Menu choice: 2  (Website Attack Vectors)
```

### Step 4 — Select Credential Harvester

```
Menu choice: 3  (Credential Harvester Attack Method)
```

**What this does:** Captures any credentials (username + password) entered by a victim on the cloned site.

### Step 5 — Select Site Cloner

```
Menu choice: 2  (Site Cloner)
```

**What Site Cloner does:** Creates an exact visual copy of any specified website, hosted on the attacker's machine.

### Step 6 — Enter Attacker's IP Address

SET asks: _"Enter the IP address for the POST back in Harvester/Tabnabbing:"_

```
Enter: 10.10.10.18  (your Kali machine's IP — where captured credentials will be sent)
```

### Step 7 — Enter Target Website to Clone

SET asks: _"Enter the url to clone:"_

```
Enter: http://www.targetwebsite.com  (the website to clone)
```

SET downloads and clones the site. When done, it starts a web server hosting the cloned site at `http://10.10.10.18`.

### Step 8 — Craft and Send Phishing Email

On the attacker machine, compose a convincing phishing email:

**Email structure:**

```
To:    victim@email.com
Subject: Your Membership Renewal — 70% Off Today Only!

Dear Member,

Your membership is about to expire. Renew now and save 70%!

[Avail Benefit]  → (hyperlink pointing to http://10.10.10.18)

Click above to secure your discounted renewal.

Best regards,
Member Services Team
```

**Key deception:** The displayed link text ("Avail Benefit") looks legitimate, but the actual hyperlink URL points to the attacker's cloned site.

### Step 9 — Victim Clicks the Link

The victim (Windows 11 machine in the lab) receives the email, clicks the link, and is directed to the cloned website — which looks identical to the real one.

The victim enters their username and password and clicks Login.

### Step 10 — Credentials Captured

What happens when the victim clicks Login:

1. The credentials are intercepted by SET and displayed in **cleartext** on the attacker's terminal
2. The victim is immediately redirected to the **real website** (so they suspect nothing — they just think they mistyped their password)

**SET terminal output:**

```
[*] WE GOT A HIT! Printing the output:
PARAM: username=testuser
PARAM: password=testpassword
```

The attacker now has the victim's credentials.

---

## 8. Defense — Netcraft Browser Extension

### What Is Netcraft?

**Netcraft** is a browser extension available for Chrome, Firefox, and Edge that analyzes websites and alerts users to potential phishing sites.

### What Netcraft Does

When you visit a website with Netcraft installed, it:

- Checks the site against its database of known phishing sites
- Analyzes hosting information, registration details, and reputation
- Shows a risk report for the current site
- **Blocks access to confirmed phishing sites with a red warning screen**

### How to Use Netcraft

1. Search "Netcraft extension" in your browser's extension store
2. Install it
3. The extension icon appears in your toolbar
4. Visit any website → click the icon → see the site's report

### What Netcraft Shows for a Real Site

- Server location and hosting provider
- Site age and registration details
- Risk rating (low for established, legitimate sites)
- No warnings or alerts

### What Netcraft Shows for a Phishing Site

- **Red warning screen** blocking access
- _"Suspected phishing site — access blocked"_
- Details about why the site was flagged

### Testing from the Practical

- **New Version Hacker's real website:** Shows normal report, all details about hosting, server location, site age — no alerts
- **The cloned phishing site:** Netcraft flags it immediately as a suspicious/phishing site and blocks access

---

## 9. Why Social Engineering Works — The Psychology

Social engineering is effective because it exploits fundamental aspects of human psychology:

| Psychological Trigger | How Attackers Exploit It                               | Example                                                   |
| --------------------- | ------------------------------------------------------ | --------------------------------------------------------- |
| **Authority**         | People tend to comply with perceived authority figures | Fake bank manager, IT admin, government officer           |
| **Urgency**           | Time pressure reduces rational thinking                | "Your account will be blocked in 24 hours!"               |
| **Fear**              | Scared people act impulsively without thinking         | "5 viruses detected — act now!"                           |
| **Greed**             | Desire for gain overrides caution                      | Lottery wins, cashback offers, free gifts                 |
| **Trust**             | People lower their guard with people they trust        | Friend networks, established relationships                |
| **Social proof**      | "Everyone else is doing it"                            | Chain letters: "50,000 people have already claimed..."    |
| **Helpfulness**       | People naturally want to be helpful                    | Holding doors for people with boxes                       |
| **Reciprocity**       | If someone does something for us, we feel obligated    | Attacker "helps" first, then asks for something in return |

---

## 10. Social Engineering and Real-World Cybercrime

Social engineering is the foundation of most financial cybercrime:

| Crime Type                    | Social Engineering Component                                                 |
| ----------------------------- | ---------------------------------------------------------------------------- |
| **Online Banking Fraud**      | Vishing calls from fake bank employees extracting OTPs                       |
| **Digital Arrest Scam**       | Fake officers calling, creating extreme fear, demanding money                |
| **UPI/Payment App Fraud**     | Fake payment requests disguised as incoming transfers                        |
| **KYC Fraud**                 | Fake KYC update calls extracting Aadhaar and banking details                 |
| **Job Offer Scam**            | Fake HR recruiters extracting documents and processing fees                  |
| **Romance Scam**              | Building relationship (Relationship Development phase) then extracting money |
| **Business Email Compromise** | Impersonating executives to authorize fraudulent wire transfers              |

---

## 11. Defenses Against Social Engineering

### Technical Defenses

| Defense                                   | What It Does                                                      |
| ----------------------------------------- | ----------------------------------------------------------------- |
| **Email spam filters**                    | Block phishing emails before they reach the inbox                 |
| **Multi-Factor Authentication (MFA)**     | Even if credentials are stolen, OTP/authenticator prevents access |
| **Netcraft / anti-phishing extensions**   | Detect and block phishing websites in the browser                 |
| **Domain reputation checks**              | Flag newly created domains (often used for phishing)              |
| **Email authentication (SPF/DKIM/DMARC)** | Verify that emails actually come from claimed domains             |
| **Endpoint protection**                   | Detect and block malicious downloads from phishing sites          |

### Human / Process Defenses

| Defense                                 | What It Does                                                                            |
| --------------------------------------- | --------------------------------------------------------------------------------------- |
| **Security awareness training**         | Teach employees to recognize social engineering attempts                                |
| **Verification protocols**              | Call back using official numbers to verify caller identity                              |
| **Information classification policies** | Define what information can be shared with whom and how                                 |
| **Visitor policies**                    | Require all visitors to be escorted; strict badge requirements                          |
| **Document shredding policy**           | Shred all sensitive documents before disposal                                           |
| **Two-person authorization**            | Require two people to authorize sensitive actions — prevents single-person manipulation |
| **Phishing simulation exercises**       | Regularly send fake phishing emails to employees; train those who fall for them         |

### What You Should Never Share

No legitimate organization will ever ask for these via phone, email, or chat:

- Passwords (any password)
- OTPs or one-time codes
- Full ATM/debit card number + CVV + expiry
- Net banking credentials
- Aadhaar or PAN card full number (for verification purposes)
- Date of birth used as a security question answer

---

## 12. Why This Matters for Cybersecurity

| Domain                  | Relevance                                                                                                                                |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Penetration Testing** | Social engineering is a required module in most professional pentests — the human attack vector is often the weakest link                |
| **Red Team Operations** | Full red team engagements always include a social engineering component (phishing campaigns, vishing, physical access attempts)          |
| **Security Awareness**  | Blue team and security teams design and deliver social engineering awareness training                                                    |
| **Incident Response**   | Most major data breaches include a social engineering component — phishing emails are the #1 initial access vector                       |
| **Digital Forensics**   | Investigators trace phishing campaigns, analyze fraudulent emails, and build evidence from social engineering attack artifacts           |
| **SOC Analyst Role**    | SOC analysts monitor for phishing emails, suspicious link clicks, and credential compromise — all directly related to social engineering |
| **CEH Certification**   | Social Engineering is an explicit module in the CEH exam, covering all techniques discussed in this lecture                              |

---

## 13. Quick Revision Table

| Concept                              | One-line Summary                                                                                  |
| ------------------------------------ | ------------------------------------------------------------------------------------------------- |
| **Social Engineering**               | Manipulating humans psychologically to extract information or cause actions — "human hacking"     |
| **Vishing**                          | Voice phishing — phone calls used to extract confidential information                             |
| **Eavesdropping**                    | Illegally listening to private conversations without consent                                      |
| **Shoulder Surfing**                 | Watching someone enter PINs/passwords from behind or beside them                                  |
| **Piggybacking**                     | Unauthorized person enters secure area using an authorized person (with their knowledge)          |
| **Tailgating**                       | Unauthorized person sneaks into secure area behind authorized person (without their knowledge)    |
| **Elicitation**                      | Extracting information through casual conversation without the target realizing                   |
| **Dumpster Diving**                  | Finding sensitive information in discarded physical materials (garbage)                           |
| **Impersonation**                    | Pretending to be someone else (bank employee, IT support, government official)                    |
| **Reverse Social Engineering**       | Attacker positions as authority so victim comes to them for help, sharing information voluntarily |
| **Diversion Theft**                  | Redirecting a legitimate service person and substituting oneself to gain access                   |
| **Popup Windows**                    | Fake browser alerts designed to scare users into clicking and sharing information                 |
| **Chain Letters**                    | Fake viral messages offering rewards for forwarding                                               |
| **Scareware**                        | Fake security alerts designed to frighten users into downloading malware                          |
| **Smishing**                         | SMS phishing — phishing attacks delivered via text message                                        |
| **Repacking**                        | Taking legitimate apps, inserting malware, and redistributing as "cracked" free versions          |
| **SET (Social Engineering Toolkit)** | Open-source pentest tool for executing social engineering attacks including site cloning          |
| **Site Cloner**                      | SET feature that creates an exact visual copy of any website to capture credentials               |
| **Credential Harvester**             | SET feature that captures and displays login credentials entered on the cloned site               |
| **Netcraft**                         | Browser extension that detects and blocks phishing websites                                       |

---

## 14. Self-Test Questions

1. Define social engineering in your own words and explain why it's sometimes more effective than technical hacking.
2. What are the four phases of a social engineering attack? Describe each.
3. What is the difference between piggybacking and tailgating?
4. Explain elicitation and give an example of how an attacker could extract three pieces of sensitive information through one casual conversation.
5. What is dumpster diving in the context of social engineering? What kinds of information can be found this way?
6. What is reverse social engineering, and why is it particularly effective?
7. Explain the three types of social engineering (human-based, computer-based, mobile-based) and give two examples of each.
8. How does scareware work psychologically? What does it make the victim believe, and what is the actual outcome?
9. Walk through the complete SET phishing attack process from launching SET to capturing credentials.
10. What is Netcraft, how is it used, and what does it show differently for a real website vs. a phishing site?
11. List five psychological triggers that social engineering exploits and give a real-world example of each.
12. What information should you never share with anyone who contacts you by phone or email, regardless of how legitimate they appear?

---

## What's Next

Social engineering is a broad field. This lecture covered the foundational theory and a basic phishing practical. Advanced topics include:

- **Spear Phishing** — highly targeted phishing aimed at specific named individuals using personal information gathered via OSINT
- **Whaling** — spear phishing targeting executives (CEOs, CFOs)
- **Business Email Compromise (BEC)** — impersonating executives to authorize fraudulent wire transfers
- **Pretexting** — elaborate fictional scenarios constructed to manipulate targets over extended periods
- **Advanced SET capabilities** — Java applet attacks, HTA attacks, QR code phishing
- **Beef (Browser Exploitation Framework)** — browser-based attack framework often used with phishing
- **Awareness Campaign Design** — how to design and deliver social engineering training for organizations

_Social engineering is the most commonly exploited attack vector in real-world breaches. According to Verizon's annual Data Breach Investigations Report, the majority of data breaches involve a human element — phishing, vishing, or other social engineering techniques. Technical defenses alone are insufficient without human security awareness._
