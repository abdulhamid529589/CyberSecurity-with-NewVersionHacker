# Networking Fundamentals: The OSI Model (Open System Interconnection Model)

> **Topic:** The OSI Model — All 7 Layers Explained in Detail (Sender-side and Receiver-side)
> **Series Context:** This video follows an earlier video on "Services" (what services are, their purposes, and where they operate). The instructor recommends watching that video first, since this series builds step by step. The next video in the series covers the **TCP/IP Model**.

---

## 📑 Table of Contents

1. [What Is the OSI Model?](#1-what-is-the-osi-model)
2. [Why Do We Study the OSI Model?](#2-why-do-we-study-the-osi-model)
3. [Structure of the OSI Model: The 7 Layers](#3-structure-of-the-osi-model-the-7-layers)
4. [Sender-Side Walkthrough: Data Flowing Top → Bottom](#4-sender-side-walkthrough-data-flowing-top--bottom)
   - 4.1 [Layer 7 — Application Layer](#41-layer-7--application-layer)
   - 4.2 [Layer 6 — Presentation Layer](#42-layer-6--presentation-layer)
   - 4.3 [Layer 5 — Session Layer](#43-layer-5--session-layer)
   - 4.4 [Layer 4 — Transport Layer](#44-layer-4--transport-layer)
   - 4.5 [Layer 3 — Network Layer](#45-layer-3--network-layer)
   - 4.6 [Layer 2 — Data Link Layer](#46-layer-2--data-link-layer)
   - 4.7 [Layer 1 — Physical Layer](#47-layer-1--physical-layer)
5. [Sender-Side Complete Flow Summary (Application → Physical)](#5-sender-side-complete-flow-summary-application--physical)
6. [Receiver-Side Walkthrough: Data Flowing Bottom → Top](#6-receiver-side-walkthrough-data-flowing-bottom--top)
7. [Master Summary Table — All 7 Layers at a Glance](#7-master-summary-table--all-7-layers-at-a-glance)
8. [Key Technical Takeaways (Quick Reference)](#8-key-technical-takeaways-quick-reference)
9. [What's Next in This Series](#9-whats-next-in-this-series)

---

## 1. What Is the OSI Model?

**OSI** stands for **Open System Interconnection Model**.

### Core Definition

The OSI Model is a **conceptual framework** that explains how every computer in the world is able to talk to every other computer — how they exchange messages, files, and data with one another despite the fact that, internally, computers only understand **binary** (0s and 1s), while humans communicate in natural language.

> In simple terms: whenever you send a message, a file, or a request to someone over a network, that communication becomes possible _because of_ the process described by the OSI Model. The model explains, step by step, how human-readable data is converted, packaged, transmitted, and reconstructed so that two machines — and by extension, two people — can understand each other.

### Why Is It Called "Open System Interconnection"?

The name itself reflects its purpose: it describes how **open systems** (i.e., systems built by different vendors, running different hardware/software) can be **interconnected** and made to communicate using a common, standardized set of rules — rather than requiring every manufacturer to build proprietary, incompatible networking systems.

---

## 2. Why Do We Study the OSI Model?

> **We study the OSI Model to understand and standardize the communication process in computer networks.**

In practice, this means:

- It gives us a structured, layer-by-layer way to understand **exactly** what happens when data moves from one device to another.
- **Example:** If you send a PDF file to someone, the OSI Model explains precisely how that PDF travels from your device to theirs — which steps it passes through, what gets attached or transformed at each step, and (critically for troubleshooting) _where_ something might go wrong if the file fails to arrive or arrives corrupted.
- It provides a **common vocabulary and reference framework** used across the entire networking and cybersecurity industry — from network engineers debugging connectivity issues, to ethical hackers analyzing how an attack traverses a network, to protocol designers building new services.
- It **decouples** each function of networking into an independent layer, so that changes or problems in one layer (e.g., swapping WiFi for fiber optic cable at the Physical Layer) don't require rebuilding the layers above it (e.g., the Application Layer still works the same way regardless of the physical medium).

---

## 3. Structure of the OSI Model: The 7 Layers

The OSI Model consists of **seven distinct layers**, each with its own specific responsibilities. They are conventionally listed from bottom to top:

| Layer Number | Layer Name         |
| ------------ | ------------------ |
| **Layer 1**  | Physical Layer     |
| **Layer 2**  | Data Link Layer    |
| **Layer 3**  | Network Layer      |
| **Layer 4**  | Transport Layer    |
| **Layer 5**  | Session Layer      |
| **Layer 6**  | Presentation Layer |
| **Layer 7**  | Application Layer  |

### Critical Concept: Direction of Reading Depends on Perspective

This is one of the most important — and most commonly confused — aspects of the OSI Model:

| Perspective                                         | Reading Direction                    | Why                                                                                                                                                                                                    |
| --------------------------------------------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Sender** (the device initiating communication)    | **Top → Bottom** (Layer 7 → Layer 1) | Data originates as an application-level request and must be progressively packaged down through each layer until it becomes raw physical signals that can travel across a medium (wire, fiber, or air) |
| **Receiver** (the device getting the communication) | **Bottom → Top** (Layer 1 → Layer 7) | Raw physical signals arrive first and must be progressively unpacked, verified, and reconstructed at each layer until the original human-readable data emerges at the application level                |

Think of it like packing a parcel for shipping (sender: you start with the item, then wrap it, then box it, then label it, then hand it to the courier — inside-out to outside-in) versus receiving that parcel (receiver: you get the outer box first, then unwrap layer by layer until you reach the actual item — outside-in to inside-out).

---

## 4. Sender-Side Walkthrough: Data Flowing Top → Bottom

When a sender wants to transmit something (a message, a file, a webpage request), the data is built up starting at the **Application Layer** and is progressively transformed as it passes down through each subsequent layer, until it finally leaves the device as physical signals.

---

### 4.1 Layer 7 — Application Layer

This is where the communication process **begins**.

**Primary Responsibilities:**

- This is where the actual **data/packet generation** starts.
- The system determines **which service and which protocol** will be used to carry the data, based on what the user is trying to do.

**Key Protocols Operating at This Layer:**

| Protocol | Function                                                            |
| -------- | ------------------------------------------------------------------- |
| **HTTP** | Accessing websites; transferring web page data                      |
| **DNS**  | Resolving a website's domain name into its corresponding IP address |
| **FTP**  | File Transfer Protocol — downloading and uploading files            |
| **SMTP** | Sending email                                                       |
| **POP3** | Receiving/retrieving email                                          |

**Example Flow:** If you want to visit a website, your device typically needs the site's **IP address** first. Since you usually only know the domain name (e.g., google.com) and not the numeric IP, **DNS** is used first to resolve that name into an IP address. Once the IP is known, **HTTP** is used to actually request and retrieve the website's content.

> All of this — protocol selection, file transfer, and mail transfer/reception — happens at the Application Layer, which serves as the entry point where the user's intent gets translated into the start of a network request.

---

### 4.2 Layer 6 — Presentation Layer

Also known as the **Translation Layer**, because its defining role is translating and formatting data so it can be safely and correctly transmitted.

**Primary Responsibilities:**

**a) Translation of Data**
Data is never converted directly into binary. It must first be **translated** into an intermediate representation before that representation is converted into binary form.

**b) Character Encoding**
This translation is done using standardized **character encoding schemes**:

| Encoding Standard                                              | Use Case                                                           |
| -------------------------------------------------------------- | ------------------------------------------------------------------ |
| **ASCII** (American Standard Code for Information Interchange) | Encoding English-language text                                     |
| **Unicode / UTF**                                              | Encoding non-English languages (Chinese, Italian, and others)      |
| **ISO standards**                                              | Alternative encoding standards for various regional/language needs |

**How ASCII Works (Example):**
Each letter is mapped to a specific numeric code. For instance, in ASCII, the letter **'A' corresponds to the number 65**, and the letter **'E' corresponds to the number 67** (per the illustrative example given). So when you type a name or word, each character is first converted into its corresponding ASCII number, and _then_ that number is converted into binary for actual transmission.

**c) Compression and Decompression**
Data may be compressed at this layer to make transmission more efficient, and decompressed on the receiving end.

**d) Encryption and Decryption**
Security-related transformation of data happens here.

- **Example:** When you download a government ID document (like an Aadhaar card PDF) that requires a password (often your date of birth) to open it, that password protection mechanism is a function of the Presentation Layer.
- **Example — HTTPS:** Plain **HTTP** is not secure. This is why **HTTPS** was introduced — the "S" stands for the security added via **SSL (Secure Socket Layer)**. Since encryption/decryption is the Presentation Layer's job, the "S" security component of HTTPS is functionally implemented at this layer (even though HTTP itself is an Application Layer protocol).

**e) Format Preservation**
If you send data in a particular format — for example, an image file — the Presentation Layer is responsible for ensuring that the data still arrives at the destination in that same original format (i.e., the receiving image application must be able to display it as an image, not as corrupted or unreadable data).

---

### 4.3 Layer 5 — Session Layer

**Primary Responsibility:** Creating and managing communication **sessions**.

**a) Session Creation**

You may already be familiar with TCP's **three-way handshake**. The stable, established connection that results _after_ this handshake completes is what we call a **session**.

- It is the Session Layer's job to ensure this session is **properly created, properly maintained, kept stable**, and free of disruption for as long as it's needed.

**b) Synchronizing Data**

"Synchronization" here refers to continuously sending updates to the server so both ends stay in sync about the current state of the connection/data.

**Two illustrative real-world examples:**

1. **Google Drive:** You can log into your Google Drive account from any device, anywhere, and access the same synchronized data. The reason your files appear identically updated across devices is because of this ongoing synchronization process.

2. **Banking App Auto-Logout:** As long as you continue actively interacting with (clicking on) a banking app, it stays logged in. But if you stop interacting for even a short period (e.g., a minute or two), the app automatically logs you out. This happens because your clicks are constantly being sent to the server as synchronization signals — when those signals stop arriving, the server infers that the user is no longer actively present and terminates the session for security.

**c) Authentication and Authorization Checking**

- Whenever you need to prove your identity somewhere — for example, by entering a username and password on a website — that identity verification process (**authentication**) is checked at the Session Layer.
- The Session Layer also verifies whether the authenticated user is genuinely **authorized** and legitimate (i.e., checking for fraud or invalid access attempts).

---

### 4.4 Layer 4 — Transport Layer

**Primary Responsibility:** Providing reliable **end-to-end connectivity** between sender and receiver. This layer performs several distinct and important functions.

**a) Protocol Selection: TCP vs. UDP**

Based on which Application Layer protocol was selected earlier, the Transport Layer decides whether the data will be sent via **TCP** or **UDP**.

| Protocol                                | Characteristics                                                                                         |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **TCP** (Transmission Control Protocol) | Slower, because it requires a three-way handshake — but more **reliable**. Used for services like HTTP. |
| **UDP** (User Datagram Protocol)        | Faster, but less reliable (no handshake overhead). Used for services like DNS.                          |

For example: if the selected Application Layer protocol is HTTP, the data travels via TCP. If it's DNS, the data travels via UDP.

**b) IP Address and Port Assignment**

The Transport Layer is responsible for assigning **IP addresses and port numbers**.

- The **source port** assigned to your system is typically a **dynamically assigned port**.
- The **destination port** depends on the specific service being used. For example, if HTTP is selected, the destination port assigned will be **port 80**.

> Analogy used in the lecture: think of the **port** as the "path" and the **IP address** as the "end address" — together they define exactly where data needs to go and through which specific channel.

**c) Error Control via Checksum**

- The Transport Layer manages **error control**, ensuring that as few transmission errors as possible occur.
- It uses a **checksum** — conceptually similar to a **hash value** — as a method of error detection.
- **How it works:** When a packet is generated, a checksum (hash value) is calculated for it — this functions like a unique "identity" for that packet (analogous to an identity card). When the packet arrives at the destination, the receiving system recalculates the checksum/hash of the received data and **compares** it against the original.
  - If the values **match**, the data is confirmed intact — no tampering occurred in transit.
  - If the values **do not match**, this indicates the packet was altered/corrupted in transit — this typically results in a **"bad request"** error being returned.
- This mechanism ensures data integrity — verifying that data was **not tampered with** while in transit between sender and receiver.

**d) Buffer Overflow Control**

- Data is never received all at once in its complete, human-readable form. It is transmitted and received in **binary**, broken into smaller chunks/packets — for example, in units of **512 bytes** at a time — rather than as one giant block (e.g., a full 1 GB file is never translated all in one go).
- Incoming data first lands in a **buffer** (a temporary memory space in your system) before being processed and translated back into its original readable form, step by step.
- If too much data arrives simultaneously and overwhelms this buffered processing (e.g., multiple chunks arriving faster than they can be properly translated), the data can become **corrupted**.
- Managing this — ensuring incoming data doesn't overwhelm the processing pipeline and cause corruption — is the Transport Layer's **buffer overflow control** responsibility.

---

### 4.5 Layer 3 — Network Layer

**Primary Responsibility:** Determining the **source and destination** of the communication, and selecting the **best route** for data to travel.

**a) Determining Source and Destination Addressing**

The Network Layer is where **Source IP Address** and **Destination IP Address** get embedded into the packet.

- **Example:** If your system's IP is (illustratively) `1.1.1.1` and you're trying to reach `google.com`, the packet will carry your IP as the source IP, and Google's resolved IP address as the destination IP.
- The **port information** (determined earlier at the Transport Layer) also travels along with this — source port is dynamic, while destination port corresponds to the chosen service (e.g., port 80 for HTTP).

**b) Best Route Selection**

- When a packet leaves your PC, it typically first travels to your **local router**, then to your **ISP's router**, and — if the destination server is globally hosted — onward through a chain of routers up to a **global ISP**, and finally to the destination server.
- At each stage, there are usually **multiple possible paths/routes/cables** the data could take.
- The Network Layer's job is to determine the **shortest and highest-speed route** available, in order to minimize transmission problems and latency.

**c) Protocol Number vs. Port Number (Important Distinction)**

> A key technical distinction highlighted in the lecture: protocols that originate at the **Network Layer never use port numbers** — instead, they are identified by a **protocol number**.

| Layer                                                              | Identification Method                                |
| ------------------------------------------------------------------ | ---------------------------------------------------- |
| **Network Layer protocols** (e.g., **IP**, **ICMP**)               | Identified by a **Protocol Number** (no port number) |
| **Transport/Application Layer protocols** (e.g., **TCP**, **UDP**) | Identified by **Port Numbers**                       |

- Both **TCP** and **UDP** each have a total of **65,535 available ports**.
- However, note that TCP and UDP _themselves_, as protocols, are identified at the network level by a **protocol number** — not a port number. (The lecture poses this as a research question for viewers: look up and identify the specific protocol numbers assigned to TCP and UDP, and to IP.)

---

### 4.6 Layer 2 — Data Link Layer

**Primary Responsibility:** Converting data into **frames** and assigning **MAC addresses**.

**a) Framing**

- Protocols and technologies associated with this layer include **Ethernet, Switches, and Bridges** — essentially, the cabling/hardware infrastructure that physically carries and translates the signal.
- All the data that has been processed through the layers above (protocol selected, encrypted/compressed, session created/synchronized, TCP/UDP and route decided) is now converted into **frames** at this layer.

**b) MAC Address Assignment**

- After data is converted into frames, the Data Link Layer assigns the **MAC (Media Access Control) Address**.
- **Data cannot be transmitted without a MAC address.**
- MAC addresses are assigned dynamically at **each hop** along the route:
  - From your PC to your router: source MAC = your PC's MAC address; destination MAC = your router's MAC address.
  - From your router to the ISP's router: source MAC = your router's MAC address; destination MAC = the ISP router's MAC address.
  - And so on, at each subsequent hop.

**c) Hop-to-Hop Error Control**

This is one of the most important distinctions in the lecture between the Transport Layer and the Data Link Layer:

> The **Transport Layer** performs error control on an **end-to-end (single-point, direct sender-to-receiver)** basis. The **Data Link Layer**, in contrast, performs its error control on a **hop-to-hop (point-to-point)** basis — meaning error checking happens independently between your PC and the router, then again between the router and the ISP's router, then again between that router and the next point, and so on.

- Similarly, session management and access control functions performed at the Data Link Layer level also operate hop-to-hop rather than end-to-end.
- Also worth noting: MAC addresses are also referred to as **physical addresses**, because they represent the hardware/physical identity of a device — as opposed to logical addressing like IP.
- The Data Link Layer also performs a full read/scan of the entire transmitted data to check for the presence of any errors.

---

### 4.7 Layer 1 — Physical Layer

**Primary Responsibility:** Determining the actual **transmission medium** and managing the real, physical transfer of bits.

**a) Transmission Mode Determination**

| Transmission Medium | Signal Type Used      |
| ------------------- | --------------------- |
| **Copper wire**     | Electromagnetic waves |
| **Optic fiber**     | Light                 |
| **WiFi**            | Magnetic/radio waves  |

The Physical Layer decides which of these transmission media/methods will actually carry the data onward — whether it be wireless, light-based (fiber), or electromagnetic (copper wire) — including infrastructure choices like wireless hubs versus fiber optics.

**b) Bit Rate Control**

- Data transfer happens **byte by byte, bit by bit**.
- The familiar download progress percentage you see (e.g., 65%, 90%) is tracked and controlled at this layer — it reflects exactly how much of that bit-by-bit transfer has completed.

**c) Synchronization**

- The Physical Layer ensures that the transmission rate/progress is **synchronized on both the sending and receiving sides** — meaning if the receiver shows 65% received, the sender's transmission progress should correspondingly correlate at 65% sent, keeping both ends in sync.

---

## 5. Sender-Side Complete Flow Summary (Application → Physical)

```
Layer 7 — Application Layer
   → Protocol selected (HTTP / DNS / FTP / SMTP, etc.)
   → Data/packet generation begins

        ↓

Layer 6 — Presentation Layer
   → Data translated (ASCII / Unicode encoding)
   → Encrypted / Decrypted, Compressed / Decompressed
   → Format preserved for transmission

        ↓

Layer 5 — Session Layer
   → Session created (post TCP 3-way handshake)
   → Data synchronization with server
   → Authentication & Authorization checked

        ↓

Layer 4 — Transport Layer
   → TCP or UDP selected
   → IP address & Port number assigned
   → Error control via Checksum
   → Buffer overflow control

        ↓

Layer 3 — Network Layer
   → Source IP & Destination IP determined
   → Best/shortest/fastest route selected

        ↓

Layer 2 — Data Link Layer
   → Data converted into Frames
   → MAC address assigned (hop-to-hop)
   → Hop-to-hop error control

        ↓

Layer 1 — Physical Layer
   → Transmission medium selected (wire / fiber / wireless)
   → Actual bit-by-bit transmission
   → Bit rate control & synchronization
```

At this point, the data physically leaves the sender's device and begins its journey across the network toward the destination.

---

## 6. Receiver-Side Walkthrough: Data Flowing Bottom → Top

On the receiving end, the entire process happens in **reverse order** — starting at the Physical Layer and working back up to the Application Layer.

| Layer (in order of processing) | What Happens at the Receiver                                                                                                                                                                                                            |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Physical Layer**          | Data is received first, via whichever transmission medium was used (wireless, wired, etc.)                                                                                                                                              |
| **2. Data Link Layer**         | Frames are unpacked ("de-framed"); MAC addresses and hop-level errors are checked                                                                                                                                                       |
| **3. Network Layer**           | The system verifies the IP address is correct and that the data has arrived at the intended destination; MAC address correctness is also confirmed here in conjunction with the Data Link Layer's check                                 |
| **4. Transport Layer**         | The system checks which protocol (TCP or UDP) the data arrived on, and verifies the assigned IP address is correct for this system                                                                                                      |
| **5. Session Layer**           | The system verifies that everything about the session is proper — that the connection remained stable and synchronized, and checks whether any problems occurred during transmission                                                    |
| **6. Presentation Layer**      | Any encryption is **decrypted**, and translation is reversed to restore the data to its original form — for example, passwords applied during encoding (like a PDF password) are removed here, restoring the file to its readable state |
| **7. Application Layer**       | The data is **finally received** in its complete, usable form and displayed to the user through whatever application/platform is relevant (browser, email client, file viewer, etc.)                                                    |

---

## 7. Master Summary Table — All 7 Layers at a Glance

| #   | Layer            | Core Responsibility                                                                      | Key Protocols / Concepts                                                        |
| --- | ---------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| 7   | **Application**  | Protocol selection; initiates packet generation                                          | HTTP, DNS, FTP, SMTP, POP3                                                      |
| 6   | **Presentation** | Translation, encryption/decryption, compression, format preservation                     | ASCII, Unicode/UTF, ISO, SSL (HTTPS)                                            |
| 5   | **Session**      | Session creation, data synchronization, authentication/authorization check               | TCP 3-way handshake, Google Drive sync example, banking app auto-logout example |
| 4   | **Transport**    | TCP/UDP selection, IP+Port assignment, error control (checksum), buffer overflow control | TCP, UDP, Checksum/Hash, Port 80 example                                        |
| 3   | **Network**      | Source/Destination IP addressing, best route selection                                   | IP, ICMP (identified by Protocol Number, not Port Number)                       |
| 2   | **Data Link**    | Framing, MAC address assignment, hop-to-hop error control                                | Ethernet, Switch, Bridge, MAC = Physical Address                                |
| 1   | **Physical**     | Transmission medium selection, bit rate control, synchronization                         | Copper wire (electromagnetic), Fiber optic (light), WiFi (magnetic wave)        |

---

## 8. Key Technical Takeaways (Quick Reference)

- **HTTPS's "S"** comes from **SSL (Secure Socket Layer)**; the actual encryption/decryption work happens at the **Presentation Layer**, even though HTTP/HTTPS is conceptually an Application Layer protocol.
- **TCP vs. UDP:** TCP is reliable but slower (requires a three-way handshake); UDP is faster but less reliable (no handshake).
- **Checksum/Hash (Transport Layer):** Functions as a data-integrity "fingerprint" for a packet — used to detect whether data was tampered with during transit.
- **MAC Address (Data Link Layer):** Also called the "physical address" of a device; it is reassigned at every hop along a route (unlike the IP address, which typically stays consistent as source/destination end-points).
- **Protocol Number vs. Port Number:** Network Layer protocols (IP, ICMP) use protocol numbers, not port numbers. Transport Layer protocols (TCP, UDP) use port numbers — each with a total range of 65,535 possible ports.
- **End-to-End vs. Hop-to-Hop Error Control:** The Transport/Session Layers handle error control and session management on an end-to-end basis (sender directly to receiver). The Data Link Layer instead handles this hop-to-hop — independently at each intermediate point along the path (PC→Router, Router→ISP, etc.).
- **Directionality Rule:** Always remember — **Sender reads Top-to-Bottom (Layer 7→1)**; **Receiver reads Bottom-to-Top (Layer 1→7)**.

---

## 9. What's Next in This Series

The instructor indicates that the **next video** in this series will cover the **TCP/IP Model** — including what it is and how it functions — building further on the foundational concepts established in this OSI Model lecture.

---

_Notes compiled in detail from the "New Version Hacker" channel's OSI Model lecture transcript. Technical terms garbled by automatic transcription (e.g., "Eka" → ASCII, "STTP" → HTTP) have been corrected to their proper technical terminology for accuracy and future reference._
