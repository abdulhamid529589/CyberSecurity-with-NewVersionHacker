# HTTP Request & Response: Structure, Methods, and Status Codes

> **Source:** "New Version Hacker" channel — Ethical Hacking / Cybersecurity Series
> **Instructor:** Devendra Pandey
> **Topic:** Request & Response Packets — HTTP vs. HTTPS, Request/Response Structure, HTTP Methods, Status Codes, and a Practical Browser Demonstration
> **Series Context:** This marks the conclusion of the networking portion of the course (following Services → OSI Model → OSI vs. TCP/IP → DNS → DNS Records). The instructor notes a parallel **Digital Forensics** playlist is also running, with Ethical Hacking and Forensics videos alternating roughly every six days.

---

## ⚠️ Disclaimer

This content is intended **strictly for educational purposes**. It does not endorse illegal or malicious activity. Always use this knowledge responsibly and in compliance with applicable laws and regulations.

---

## 📑 Table of Contents

1. [What Is Request and Response? (Core Concept)](#1-what-is-request-and-response-core-concept)
2. [HTTP vs. HTTPS](#2-http-vs-https)
3. [Why HTTPS Is More Secure: SSL/TLS](#3-why-https-is-more-secure-ssltls)
4. [HTTP/HTTPS and the OSI Model](#4-httphttps-and-the-osi-model)
5. [HTTP vs. HTTPS — Side-by-Side Comparison](#5-http-vs-https--side-by-side-comparison)
6. [Why Secure Communication Matters](#6-why-secure-communication-matters)
7. [Structure of an HTTP Request](#7-structure-of-an-http-request)
8. [Example of an HTTP Request (Annotated)](#8-example-of-an-http-request-annotated)
9. [HTTP Methods](#9-http-methods)
   - 9.1 [GET](#91-get)
   - 9.2 [POST](#92-post)
   - 9.3 [PUT](#93-put)
   - 9.4 [DELETE](#94-delete)
   - 9.5 [PATCH](#95-patch)
   - 9.6 [OPTIONS](#96-options)
   - 9.7 [HEAD](#97-head)
   - 9.8 [TRACE](#98-trace)
   - 9.9 [CONNECT](#99-connect)
10. [Structure of an HTTP Response](#10-structure-of-an-http-response)
11. [Example of a Typical HTTP Response (Annotated)](#11-example-of-a-typical-http-response-annotated)
12. [Set-Cookie: What It Is and Why It's Sensitive](#12-set-cookie-what-it-is-and-why-its-sensitive)
13. [HTTP Status Codes](#13-http-status-codes)
    - 13.1 [1xx — Informational](#131-1xx--informational)
    - 13.2 [2xx — Success](#132-2xx--success)
    - 13.3 [3xx — Redirection](#133-3xx--redirection)
    - 13.4 [4xx — Client Errors](#134-4xx--client-errors)
    - 13.5 [5xx — Server Errors](#135-5xx--server-errors)
14. [Practical Demonstration: Viewing Requests/Responses in Browser DevTools](#14-practical-demonstration-viewing-requestsresponses-in-browser-devtools)
15. [Summary Table — HTTP Methods](#15-summary-table--http-methods)
16. [Summary Table — HTTP Status Codes](#16-summary-table--http-status-codes)
17. [Key Takeaways](#17-key-takeaways)

---

## 1. What Is Request and Response? (Core Concept)

### The Basic Scenario

- You have a **system (PC)** and a **server** (e.g., hosting a website called `newversionhacker.com`).
- Whenever you access any website or any resource, a **packet is generated** on your system and sent out toward the server.

### Defining Request and Response

| Term         | Definition                                                                                                                                                       |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Request**  | The packet **you send** to a server, asking to access something — essentially telling the server "this is who I am, and I want to access your website/resource." |
| **Response** | The **reply the server sends back** to you, in answer to your request.                                                                                           |

> **Simple framing used in the lecture:** _"Whenever you request something from someone, we call it a request. When they reply to you, we call it a response."_

### Authorization Matters

- Not every request is automatically granted. If you ask for something you're **authorized** to access (e.g., a public product page), the server will typically respond with that content easily.
- However, if you request something you're **not authorized** for (e.g., trying to access an **administrator-only page**), the server will **not** grant that request — because you don't have the necessary permissions.

---

## 2. HTTP vs. HTTPS

Whenever a packet travels from your PC to a server (and the reply travels back), it does so via one of two protocols: **HTTP** or **HTTPS**.

### HTTP (Hypertext Transfer Protocol)

- **Full form:** **H**yper**T**ext **T**ransfer **P**rotocol
- **HTML** = the document/file type this protocol is designed to transmit (HyperText Markup Language) — the underlying content of most web pages.
- **"Hyperlink"** refers to clickable links that redirect you directly to another resource.
- HTTP's core job: **transmitting** data — sending it from one system (your PC) to another (a server), or vice versa.
- **Described in the lecture as:** _"HTTP operates as a request-response protocol in client-server computing mode"_ — meaning it's the mechanism that enables the client and server to communicate with each other.

### The Problem with Plain HTTP: It's Not Secure

- Data sent via HTTP travels in **clear text** — meaning anyone intercepting that traffic can **read it directly**, including sensitive details like **usernames and passwords**.

### HTTPS (Hypertext Transfer Protocol Secure)

- **Full form:** HyperText Transfer Protocol **Secure**
- As the name implies, this version is built specifically for **secure communication**.
- **Current adoption:** The lecture notes that HTTP is now rarely seen in practice — approximately **99% of websites today use HTTPS**, since everyone wants their communications secured.

---

## 3. Why HTTPS Is More Secure: SSL/TLS

HTTPS achieves its security using encryption protocols:

| Protocol | Full Form                | Role                                                  |
| -------- | ------------------------ | ----------------------------------------------------- |
| **SSL**  | Secure Socket Layer      | Encrypts data exchanged between client and server     |
| **TLS**  | Transport Layer Security | Provides security specifically at the transport layer |

> **What the "S" in HTTPS represents:** The S stands for the security provided via **SSL** (Secure Socket Layer)/**SSL certificates** — this is what encrypts the data flowing between client and server.

### What This Encryption Guarantees

1. **Confidentiality** — Ensures the exchanged data cannot be seen/read by unauthorized parties (e.g., during a banking transaction, no one else can view your data).
2. **Integrity** — Ensures the data **cannot be manipulated/tampered with** in transit. Whatever you send is guaranteed to arrive exactly as sent, without alteration.

---

## 4. HTTP/HTTPS and the OSI Model

An important clarification the lecture draws from the earlier **OSI Model** lecture:

> While **HTTP itself is an Application Layer protocol**, the "S" (security) component of HTTPS — encryption and decryption — is technically the responsibility of the **Presentation Layer**, since securing and encrypting/decrypting data is a Presentation Layer function (as covered in the OSI Model lecture).

So: **HTTP = Application Layer function**, but **the encryption that makes it "HTTPS" = Presentation Layer function.**

---

## 5. HTTP vs. HTTPS — Side-by-Side Comparison

| Aspect                      | HTTP                                    | HTTPS                                                                                   |
| --------------------------- | --------------------------------------- | --------------------------------------------------------------------------------------- |
| **Security**                | Not secure — data visible in clear text | Secure — data is encrypted                                                              |
| **Port Number**             | **80**                                  | **443**                                                                                 |
| **Port Category**           | Well-known port                         | Well-known port                                                                         |
| **Certificate Requirement** | No certificate required                 | Requires an **SSL/SSL certificate** — without it, HTTPS communication will not function |

---

## 6. Why Secure Communication Matters

The lecture outlines several concrete reasons secure (HTTPS) communication is essential:

1. **Protecting sensitive/personal data** — payment details, personal information, and any private communication need protection from unauthorized access.
2. **Preventing eavesdropping** — _"Eavesdropping"_ refers to secretly watching/listening in on someone's communication. Proper encryption prevents this from being possible.
3. **Preventing data tampering** — Ensures no one can alter data in transit, preserving its integrity.
4. **Maintaining authenticity** — Only the intended/authorized party can actually access the data.
5. **Reducing phishing attack risk** — Since encrypted links/connections are harder to spoof or manipulate, properly secured communication reduces (though doesn't eliminate) phishing risk. (The lecture notes that reading/analyzing links carefully is also a separate skill for identifying phishing, covered in more depth later in the broader course.)

---

## 7. Structure of an HTTP Request

Whenever a packet leaves your PC heading to a server, it is structured in three main parts:

| Section             | Description                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Request Line** | The very first line — contains the **protocol version**, the **method** being used (GET, POST, etc.), and the specific **resource/path** being requested (e.g., `index.htm`)                                        |
| **2. Headers**      | Additional metadata about your system/request — what your system supports, what type of data it can handle, which website you're trying to reach, which browser/OS you're using, what content type you expect, etc. |
| **3. Body**         | Contains data being sent along with the request — e.g., **credentials** like a username and password when logging into a system                                                                                     |

> **Reminder from the lecture:** _Whatever appears at the top of the packet is the request line; below that are the headers; and at the very bottom is the body._

---

## 8. Example of an HTTP Request (Annotated)

Based on the walkthrough example in the lecture, a typical request looks like this conceptually:

```
GET /index.htm HTTP/1.1          ← Request Line (Method, Resource, Protocol Version)

Host: newversionhacker.com       ← Which website you're trying to reach
User-Agent: Mozilla/5.0 (...)    ← Browser name + version + operating system info
Accept-Language: en-US           ← Which language you understand (so server can reply appropriately)
Accept-Encoding: gzip, deflate, br  ← Which compression/encoding formats your system supports
Connection: keep-alive           ← Requests the connection stay open until the transaction completes
Upgrade-Insecure-Requests: 1     ← Tells the server to prefer sending secure content only

[Body — e.g., username/password credentials, if applicable]
```

### Field-by-Field Explanation

| Field                         | Meaning                                                                                                                                         |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Host**                      | The domain/website name you're trying to access                                                                                                 |
| **User-Agent**                | Identifies your browser (name + version) and your operating system — important context for later "information gathering" topics in the course   |
| **Accept-Language**           | Tells the server which language you understand, so it can reply appropriately (e.g., replying in English rather than a language you can't read) |
| **Accept-Encoding**           | Tells the server which compression formats (gzip, deflate, br, etc.) your system can handle, so it knows how to package its response            |
| **Connection: keep-alive**    | Requests that the connection remain open until the full transaction (request + reply) completes                                                 |
| **Upgrade-Insecure-Requests** | Signals to the server that your system prefers to receive only **secure** content, not insecure                                                 |

---

## 9. HTTP Methods

### Why Multiple Methods Exist

The lecture explains the reasoning behind having multiple distinct HTTP methods with an illustrative thought experiment: imagine if there were only **one single method** used for everything — sending a message, requesting a PDF, downloading a file, etc. This would create **confusion on the server side**, since it couldn't distinguish the _intent_ behind different requests, potentially leading to errors or unintended actions (e.g., a file being deleted instead of downloaded).

> **Solution:** HTTP/HTTPS remains the single overarching protocol, but it defines **multiple distinct methods**, each communicating a specific _intent_ to the server about what action should be performed.

### 9.1 GET

- **Purpose:** Used to **retrieve/download data** from a server, or simply to **access a website**.
- **Example use case:** Accessing a webpage, viewing product information, downloading a file.
- Data for GET requests travels along with the **URL** itself.

### 9.2 POST

- **Purpose:** Used to **submit/store new data** on the server.
- **Example use case:** Filling out and submitting a form — e.g., creating a new account, uploading information to a server for the first time.
- **Lecture description:** _"Submit data to be processed to specify resource — data sent in the request body."_

### 9.3 PUT

- **Purpose:** Used to **update existing resources** on the server.
- **Example use case:** You already have an account, and you want to modify a small existing detail — such as changing your name, mobile number, or address.
- **Key distinction from POST:** POST creates/stores **new** data (e.g., a new account); PUT **updates** data that already exists.
- Data sent via PUT travels securely within the **request body**.

### 9.4 DELETE

- **Purpose:** Used to **delete existing data/resources** from the server.
- **Example use case:** Deleting an uploaded file (e.g., a PDF you no longer need on a cloud drive), or deleting an entire account.

### 9.5 PATCH

- **Purpose:** Used to **partially update/replace** specific resources — distinct from PUT's broader update function.
- **Key distinction from PUT:** While PUT updates fields like an address or phone number, PATCH is used for more surgical **replacement** of a specific piece of data.
- **Example given:** If you accidentally typed "Dav" instead of "Dev" in your profile name, PATCH would be used to replace just that specific character/segment. Similarly, replacing "ABCD" with "1234" in a field would be a PATCH-type operation.
- **Conceptual summary:** _"To patch means to put other things in their place"_ — i.e., replacing specific elements rather than updating the resource as a whole.

### 9.6 OPTIONS

- **Purpose:** Used to **describe the available communication methods** for a target resource.
- **Example use case:** If a PDF exists on a server, OPTIONS can reveal **all the different ways** you could interact with/download that resource — essentially listing out the possible communication options available for that resource.

### 9.7 HEAD

- **Purpose:** Similar to GET, but instead of retrieving the actual content, it retrieves only the **metadata** about that content.
- **Example use case:** Before downloading a large PDF, you might want to know its **file size**, **format**, and **estimated download time** — HEAD retrieves exactly this kind of metadata without downloading the full resource itself.
- **Practical analogy:** This is the same mechanism behind seeing a file's size (e.g., "2.3 GB") displayed before you actually click download.

### 9.8 TRACE

- **Purpose:** Performs a **loop-back test** along the path to a target resource — primarily useful for **troubleshooting**.
- **Example use case:** Programmers/developers use TRACE to help identify where errors are occurring on a website — it essentially reports back what error occurred and where, aiding in debugging.
- **Two functions summarized:**
  1. Assists in general **searching/tracing**.
  2. Primarily used for **loopback testing / troubleshooting** — helping fix bugs on a website by automatically reporting errors that occur.

### 9.9 CONNECT

- **Purpose:** Used to establish a **tunnel/connection** — most relevant when using a **proxy** or **VPN**.
- **How it works:** When you use a proxy to access a website, your request first goes to the **proxy**, which then forwards it to the actual destination server. The **CONNECT method** is what establishes this tunnel between your system and the server _through_ the proxy.
- **Key insight:** Even though your IP address may appear to change (as is typical with proxies/VPNs), the underlying **connection itself remains constant** — this consistent tunnel is made possible by the CONNECT method.

---

## 10. Structure of an HTTP Response

Just as a request has a defined structure, so does the **response** sent back by the server:

| Section            | Description                                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Status Line** | Indicates the **HTTP version** being used and the **status code** (e.g., `200 OK`) — telling you whether your request was successfully processed or not |
| **2. Headers**     | Metadata about the response — content type, content length, **Set-Cookie** information, etc.                                                            |
| **3. Body**        | Contains the actual **data/content** being returned — e.g., the full HTML/code of the website you requested                                             |

> **Status Line clarified:** If the server approves your request (i.e., confirms you're allowed to access what you asked for), it will return a status code like **`200`**, meaning **"OK"** — your request has been successfully processed.

---

## 11. Example of a Typical HTTP Response (Annotated)

```
HTTP/1.1 200 OK                          ← Status Line (version + status code)

Date: [date of access]                    ← Date the website was accessed
Last-Modified: [date]                     ← When the requested content was last updated
Content-Type: text/html                   ← Format of the returned content
Content-Length: 137                       ← Total length/size of the content
Connection: keep-alive                    ← Connection maintained until transaction completes

[Body — the full HTML/code of the requested website]
```

### Field-by-Field Explanation

| Field                      | Meaning                                                                                        |
| -------------------------- | ---------------------------------------------------------------------------------------------- |
| **Date**                   | The date/time the website was accessed                                                         |
| **Last-Modified**          | When the requested content was last updated                                                    |
| **Content-Type**           | The format of the returned data (e.g., `text/html`)                                            |
| **Content-Length**         | The total size/length of the returned content                                                  |
| **Connection: keep-alive** | Same concept as in the request — maintains the connection until the full transaction completes |

---

## 12. Set-Cookie: What It Is and Why It's Sensitive

### What Is a Set-Cookie?

When a server responds to your request, it often includes a special header called **Set-Cookie**. This cookie essentially functions as a unique **session/identity marker** — conceptually similar to how everyone has a unique Aadhaar card (national ID) number.

> **Key point:** Every time you create a new session/connection with a website, you receive a **different** Set-Cookie value — it's unique to that specific session.

### Why Set-Cookies Are Highly Sensitive

Set-Cookies function as **sensitive credentials**. The lecture illustrates the risk with a concrete example:

- Suppose you're in the process of **changing your password** on a website.
- You send the request to change your password, and the server responds with a corresponding **Set-Cookie ID** tied to that specific session/action.
- **If this Set-Cookie ID falls into someone else's hands**, that person could potentially **use it themselves** to complete or manipulate that same action — for example, they could change your password instead of you, even without knowing your original password, simply by possessing that intercepted cookie/session ID.

> **Core risk summarized:** _Whoever obtains this ID can tamper with the account/action associated with it — making Set-Cookie values a critical piece of information to protect from interception._

---

## 13. HTTP Status Codes

Status codes are what the **server** uses to communicate the outcome of a request back to the client — complementing the **methods** (which is how the _client_ tells the server what it wants to do).

> **Clarifying distinction from the lecture:** _"We use methods to tell the server what we want; the server uses status codes to tell us what happened."_

### 13.1 1xx — Informational

| Code    | Meaning                                                                                                                                                                         |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **100** | **Continue** — Information/headers have been received, but the body hasn't arrived yet; the connection should be kept alive while waiting for the rest of the data.             |
| **101** | **Switching Protocols** — Indicates the protocol currently being used isn't considered secure/reliable enough by the server, so the server will switch to a different protocol. |

### 13.2 2xx — Success

| Code    | Meaning                                                                                                                                                                                                                        |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **200** | **OK** — The request was successfully processed; you're permitted to access what you requested.                                                                                                                                |
| **201** | **Created** — Something was successfully created on the server (e.g., a new file, resource, or account).                                                                                                                       |
| **204** | **No Content** — The server successfully processed the request but has no content to return (e.g., you requested an image, but the server doesn't actually have that image — it acknowledges the request but returns nothing). |

### 13.3 3xx — Redirection

| Code    | Meaning                                                                                                                                                                                       |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **301** | **Moved Permanently** — The requested resource has been permanently moved to a different location.                                                                                            |
| **302** | **Not Found** _(as described in the lecture)_ — What you requested could not be located at the expected location.                                                                             |
| **304** | **Not Modified** — Indicates the resource hasn't been modified since the last request; also used in contexts where modification was attempted but you weren't authorized to make that change. |

### 13.4 4xx — Client Errors

| Code    | Meaning                                                                                                                                                                                           |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **400** | **Bad Request** — There's likely a spelling/formatting mistake in what you requested, causing the server to fail to understand the request.                                                       |
| **401** | **Unauthorized** — You attempted to access something requiring authentication/authorization you don't have (e.g., trying to access an admin page without admin privileges).                       |
| **403** | **Forbidden** — The client doesn't have the necessary access rights/permissions to the requested content (even if some authentication exists, you specifically lack permission for this content). |
| **404** | **Not Found** — Whatever you requested doesn't exist on the server.                                                                                                                               |

> **401 vs. 403 distinction (as explained in the lecture):** 401 relates to needing proper authentication for something like admin-level access; 403 relates to lacking permission/rights to specific content even without necessarily needing full re-authentication.

### 13.5 5xx — Server Errors

| Code    | Meaning                                                                                                                                                              |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **500** | **Internal Server Error** — The server encountered an unexpected condition, is undergoing maintenance, or something else is happening internally on the server side. |
| **502** | **Bad Gateway** — Something is being accessed in an improper/unauthorized manner, or the gateway/method being used isn't correct.                                    |
| **503** | **Service Unavailable** — The server is currently unavailable or not capable of providing a response at this time.                                                   |

> **Important distinction:** 3xx/4xx codes generally reflect issues stemming from the **client's** request (e.g., wrong URL, lack of permission, resource not found), while **5xx codes reflect problems occurring on the server side itself**.

---

## 14. Practical Demonstration: Viewing Requests/Responses in Browser DevTools

The instructor demonstrates viewing live HTTP request/response data using a browser's built-in developer tools:

### Steps Demonstrated

1. **Open Developer Tools** — Right-click → **Inspect** (or equivalent browser shortcut).
2. Navigate to the **Network** tab within DevTools.
3. With the Network tab open and recording, **navigate to a website** (the lecture uses `youtube.com` as the example).
4. Observe the list of network requests that populate — each entry shows details including the **Request Method** used (in this case, **GET**).
5. Clicking into a specific request reveals:
   - The **Response** tab — showing the actual returned content/programming (e.g., the site's HTML/code).
   - The **Headers** section — showing all the request and response header fields discussed earlier in this lecture (Host, User-Agent, Content-Type, Set-Cookie, etc.).

> **Practical takeaway:** Browser DevTools' Network tab is a direct, hands-on way to observe the exact request method, headers, status codes, and response body for any web request — making all the theoretical concepts covered in this lecture directly observable in real time.

---

## 15. Summary Table — HTTP Methods

| Method      | Purpose                                                   | Example Use Case                                                      |
| ----------- | --------------------------------------------------------- | --------------------------------------------------------------------- |
| **GET**     | Retrieve/download data or access a resource               | Loading a webpage, downloading a file                                 |
| **POST**    | Submit/store new data on the server                       | Filling out and submitting a signup form                              |
| **PUT**     | Update existing resource data                             | Changing your name, address, or phone number on your profile          |
| **DELETE**  | Remove data/resources from the server                     | Deleting an uploaded file or an entire account                        |
| **PATCH**   | Partially replace/correct specific resource data          | Fixing a typo in a single field (e.g., "Dav" → "Dev")                 |
| **OPTIONS** | Describe available communication methods for a resource   | Discovering all the ways a PDF on a server can be accessed/downloaded |
| **HEAD**    | Retrieve metadata about a resource without downloading it | Checking a file's size/format before downloading                      |
| **TRACE**   | Perform loopback testing along the path to a resource     | Troubleshooting/debugging errors on a website                         |
| **CONNECT** | Establish a tunnel connection (commonly via proxy/VPN)    | Maintaining a consistent connection while using a proxy service       |

---

## 16. Summary Table — HTTP Status Codes

| Category | Range         | Meaning                            | Key Examples                                                               |
| -------- | ------------- | ---------------------------------- | -------------------------------------------------------------------------- |
| **1xx**  | Informational | Request received, still processing | 100 (Continue), 101 (Switching Protocols)                                  |
| **2xx**  | Success       | Request successfully processed     | 200 (OK), 201 (Created), 204 (No Content)                                  |
| **3xx**  | Redirection   | Resource moved / not modified      | 301 (Moved Permanently), 302 (Not Found — per lecture), 304 (Not Modified) |
| **4xx**  | Client Error  | Problem with the client's request  | 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found)    |
| **5xx**  | Server Error  | Problem on the server's side       | 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable)  |

---

## 17. Key Takeaways

1. **Request = what you ask for; Response = what the server sends back.** Authorization determines whether a request is granted.
2. **HTTP is not secure** (clear-text data); **HTTPS is secure**, using **SSL/TLS encryption** to guarantee confidentiality and integrity. HTTP uses **port 80**; HTTPS uses **port 443** and requires an **SSL certificate**.
3. While HTTP/HTTPS is an **Application Layer** protocol, the encryption that makes HTTPS "secure" is technically a **Presentation Layer** function (per the OSI Model).
4. Every HTTP **request** has three parts: **Request Line, Headers, Body**. Every HTTP **response** also has three parts: **Status Line, Headers, Body**.
5. **HTTP methods** (GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD, TRACE, CONNECT) each communicate a distinct _intent_ from client to server — preventing the ambiguity/confusion that would arise from using a single universal method for everything.
6. **Status codes** are how the server communicates the _outcome_ of a request back to the client — organized into 1xx (informational), 2xx (success), 3xx (redirection), 4xx (client error), and 5xx (server error) categories.
7. **Set-Cookie** values function as sensitive session identifiers — if intercepted, they can potentially allow an attacker to hijack or manipulate an ongoing session/action (e.g., a password change).
8. Browser **DevTools' Network tab** provides a direct, practical way to observe real HTTP requests, methods, headers, and responses for any website.

---

_Notes compiled in detail from the "New Version Hacker" channel's "Request and Response" lecture transcript (Devendra Pandey). Technical terms garbled by automatic transcription (e.g., "STTP"/"SCTP" → HTTP, "ATTP" → HTTP, "gate" → GET) have been corrected to proper technical terminology for accuracy and future reference._
