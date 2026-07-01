# Networking Protocols: TCP & UDP — Part 6 (In-Depth Notes)

## What Protocols Are, How TCP's Three-Way Handshake Works, TCP Flags, Connection Termination, and UDP

> **Course:** NewVersionHacker — Networking Fundamentals Series (Part 6)
> **Prerequisite:** Parts 1–5 cover IP addressing, NAT, MAC addresses, ARP, DHCP, and ports. This part builds directly on Part 5 — you need to know what ports are and how the 65535-port range is divided before this lecture makes full sense.

---

## Table of Contents

1. [What Is a Protocol?](#1-what-is-a-protocol)
2. [The Two Networking Protocols: TCP and UDP](#2-the-two-networking-protocols-tcp-and-udp)
3. [TCP — Transmission Control Protocol](#3-tcp--transmission-control-protocol)
4. [The Six TCP Flags](#4-the-six-tcp-flags)
5. [TCP Three-Way Handshake (Connection Establishment)](#5-tcp-three-way-handshake-connection-establishment)
6. [TCP Connection Termination (FIN Flag Sequence)](#6-tcp-connection-termination-fin-flag-sequence)
7. [TCP Sequence Numbers](#7-tcp-sequence-numbers)
8. [UDP — User Datagram Protocol](#8-udp--user-datagram-protocol)
9. [TCP vs UDP — Full Side-by-Side Comparison](#9-tcp-vs-udp--full-side-by-side-comparison)
10. [Which Services Use TCP vs UDP](#10-which-services-use-tcp-vs-udp)
11. [Corrections & Clarifications to the Original Transcript](#11-corrections--clarifications-to-the-original-transcript)
12. [Why This Matters for Cybersecurity](#12-why-this-matters-for-cybersecurity)
13. [Quick Revision Table](#13-quick-revision-table)
14. [Self-Test Questions](#14-self-test-questions)

---

## 1. What Is a Protocol?

### The road-safety analogy

When you ride a bike or drive a car, you don't just go wherever you want however you want — there are rules: wear a helmet, carry a license, stop at red lights, give way at intersections. These rules exist so that everyone on the road can travel safely and predictably, without chaos or accidents.

**A networking protocol is exactly the same thing: a set of rules and regulations that govern how data travels across a network so it reaches its destination safely, correctly, and reliably.**

Without protocols, two computers trying to communicate would be like two cars on a road with no traffic laws — data would collide, arrive in the wrong order, or never arrive at all.

### Formal definition

> **A protocol is a standardized set of rules and regulations that define how data is formatted, transmitted, received, and acknowledged between devices in a network.**

Protocols define:

- How a connection is established and ended
- How errors are detected and recovered from
- In what order steps happen
- What each side must do when it receives data
- How data is packaged and labeled for delivery

---

## 2. The Two Networking Protocols: TCP and UDP

At the **Transport Layer** of networking (Layer 4 in the OSI model), there are two fundamental protocols that govern how data actually moves between systems:

| Protocol | Full Name                     |
| -------- | ----------------------------- |
| **TCP**  | Transmission Control Protocol |
| **UDP**  | User Datagram Protocol        |

Everything else you've used on the internet — web browsing, video calls, file transfers, DNS lookups, email — uses one of these two as its underlying transport mechanism. Understanding how they work and how they differ is fundamental to both networking and cybersecurity.

---

## 3. TCP — Transmission Control Protocol

TCP is a **connection-oriented** protocol. Before any data is actually sent, TCP first establishes a proper, confirmed connection between sender and receiver. It then ensures data arrives completely, in order, and without errors — resending anything that gets lost or corrupted along the way.

### Core characteristics of TCP:

- **Connection must be established first** (via a handshake — explained in Section 5)
- **Reliable delivery:** TCP guarantees that data reaches the other side. If packets are dropped or corrupted, they are retransmitted automatically.
- **Ordered delivery:** Data arrives in the same sequence it was sent, even if individual packets took different paths.
- **Acknowledgment-based:** The receiver continuously confirms to the sender that data has been received, so the sender always knows the current status.
- **Slightly slower** than UDP because of all the overhead involved in establishing connections, tracking sequences, and acknowledging packets.

TCP uses a set of **flags** to coordinate this reliable communication. Understanding these flags is key to understanding how TCP manages connections and data flow.

---

## 4. The Six TCP Flags

TCP flags are 1-bit fields embedded in the TCP header of every packet. They act like signals — similar to the colored flags used by a train conductor to indicate start, stop, or caution. TCP has six such flags, each indicating a specific instruction or state.

---

### Flag 1: SYN (Synchronize)

- **Full name:** Synchronize
- **Purpose:** Initiates a new connection. Sent by the client to say _"I want to start a connection with you."_
- **Analogy used in lecture:** Before asking a stranger for the time, you first greet them or shake their hand — this "greeting" is the SYN. It sets the stage for the actual conversation (data transfer) to follow.
- **Key rule:** SYN is always the **first flag sent** when any new TCP connection is being established. No other communication happens before this.

---

### Flag 2: ACK (Acknowledgment)

- **Full name:** Acknowledgment
- **Purpose:** Confirms receipt of something — a SYN request, a data packet, or a FIN (termination) request.
- **Analogy used in lecture:** WhatsApp message ticks — one tick means the message left your phone; two ticks mean it was delivered; two blue ticks mean the receiver has seen it. Each of these is an acknowledgment from the system that something happened. In TCP, the ACK flag serves this same function — the receiver is continuously saying _"I got that."_
- **Important:** ACK is the most frequently sent flag — it is attached to almost every packet sent **after** the initial SYN, because the receiver constantly needs to acknowledge what it's received.

---

### Flag 3: FIN (Finish)

- **Full name:** Finish
- **Purpose:** Signals that the sender has finished sending data and wants to gracefully close its side of the connection.
- **Analogy:** Once a file transfer reaches 100%, there's no reason to keep the connection alive. Rather than just abruptly dropping it, TCP sends a FIN flag — a polite, formal "I'm done, let's close the connection."
- **Key rule:** FIN does not immediately close everything — it's part of a 4-step process explained in Section 6.

---

### Flag 4: RST (Reset)

- **Full name:** Reset
- **Purpose:** Abruptly resets and terminates a connection when something goes wrong — an error, an unexpected state, or a rejected connection attempt.
- **Analogy:** Restarting your computer to fix an issue. When a network connection reaches an unrecoverable error state, the RST flag clears everything and forces a fresh start, rather than trying to gracefully negotiate a close.
- **Key distinction from FIN:** FIN is a graceful, planned shutdown. RST is an emergency, immediate termination — something went wrong and the connection must stop now.

---

### Flag 5: PSH (Push)

- **Full name:** Push
- **Purpose:** Tells the receiving end to immediately process and pass the data up to the application, rather than buffering it (holding it temporarily in memory) before delivering it.
- **Scenario:** Imagine you're sending three files and two go through fine but one gets stuck in a buffer halfway through — possibly because of a network hiccup or the sender temporarily going out of range. The PSH flag forces that stuck data to be pushed through and delivered immediately, preventing data corruption from an incomplete transfer.

---

### Flag 6: URG (Urgent)

- **Full name:** Urgent
- **Purpose:** Marks certain data within a packet as urgent, signaling to the receiver that this particular data should be prioritized and processed immediately, ahead of anything else in the queue.
- **Analogy:** Saying _"Give me water quickly, I'm very thirsty!"_ — the word "urgently" is itself the signal that this request should jump the queue. URG in TCP does the same thing for data.

---

### TCP Flags Summary Table

| Flag    | Full Name      | Purpose                                              |
| ------- | -------------- | ---------------------------------------------------- |
| **SYN** | Synchronize    | Initiate a new connection                            |
| **ACK** | Acknowledgment | Confirm receipt of data, SYN, or FIN                 |
| **FIN** | Finish         | Gracefully close a connection                        |
| **RST** | Reset          | Abruptly terminate due to error                      |
| **PSH** | Push           | Force immediate delivery of buffered data            |
| **URG** | Urgent         | Prioritize marked data over everything else in queue |

---

## 5. TCP Three-Way Handshake (Connection Establishment)

Before any data is exchanged, TCP requires both sides to formally agree to communicate. This agreement process is called the **Three-Way Handshake** — named because it involves exactly three steps (three "hands" being exchanged).

**Parties in the example:**

- **Bob** = Client (the one initiating the connection)
- **Alice / Server** = Server (the one being connected to)

---

### Step 1 — SYN (Bob → Server)

Bob sends a **SYN packet** to the server.

**What it means:** _"Hello, I would like to establish a connection with you. Is your port open? I want to communicate on port 23."_

```
Bob  ──── SYN ────▶  Server
```

The SYN packet includes:

- Bob's initial sequence number (a random starting number used to track the order of packets — see Section 7)
- The port Bob wants to connect to (e.g., port 23)

---

### Step 2 — SYN + ACK (Server → Bob)

If the server is willing and its port is open, it replies with a **SYN-ACK packet** — this is actually two flags sent together.

**What it means:**

- The **ACK** part says: _"I received your SYN (connection request)."_
- The **SYN** part says: _"I also want to establish this connection with you — synchronize with me too."_

```
Bob  ◀── SYN + ACK ──  Server
```

If the server does NOT want to communicate (e.g., port is closed or firewall is blocking), it sends only an **ACK** (or a **RST**), and the connection is not established.

---

### Step 3 — ACK (Bob → Server)

Bob responds with a final **ACK packet**.

**What it means:** _"I received your SYN-ACK. Connection confirmed. We're synchronized and ready to communicate."_

```
Bob  ──── ACK ────▶  Server
```

At this point, the **connection is fully established** and actual data transfer can begin.

---

### Full Three-Way Handshake Diagram

```
CLIENT (Bob)                        SERVER
    │                                   │
    │──────────── SYN ─────────────────▶│  Step 1: "I want to connect"
    │                                   │
    │◀────────── SYN + ACK ─────────────│  Step 2: "Received. I also want to connect"
    │                                   │
    │──────────── ACK ─────────────────▶│  Step 3: "Confirmed. Connection established"
    │                                   │
    │═══════════ DATA TRANSFER ══════════│  (Actual data exchange begins)
```

---

## 6. TCP Connection Termination (FIN Flag Sequence)

When one side finishes sending data and wants to end the connection, TCP uses a **4-step termination process** (sometimes called the Four-Way Handshake or FIN sequence) — not the same as the 3-step connection process.

**Important:** Unlike a RST which kills the connection instantly, FIN is a graceful shutdown — both sides confirm they're truly done before closing.

---

### Termination — Step by Step

**Step 1 — FIN (Sender → Receiver)**
The sender has finished transferring data and signals it:

```
Sender ──── FIN ────▶ Receiver
```

_"I've sent everything I had to send. I want to terminate this connection."_

**Step 2 — ACK (Receiver → Sender)**
The receiver acknowledges the termination request:

```
Sender ◀──── ACK ──── Receiver
```

_"I received your FIN (termination request)."_
At this point, the receiver checks: **has ALL the data arrived correctly?**

**Step 3 — FIN (Receiver → Sender)**
Once the receiver confirms all data arrived safely, it sends its own FIN:

```
Sender ◀──── FIN ──── Receiver
```

_"I've confirmed all data received. I'm also ready to close."_

**Step 4 — ACK (Sender → Receiver)**
The sender sends a final acknowledgment:

```
Sender ──── ACK ────▶ Receiver
```

_"I received your FIN. Connection is now officially terminated."_

---

### Full Termination Diagram

```
CLIENT (Sender)                     SERVER (Receiver)
    │                                   │
    │──────────── FIN ─────────────────▶│  "I'm done sending"
    │                                   │
    │◀──────────── ACK ─────────────────│  "Got your FIN, checking data..."
    │                                   │
    │◀──────────── FIN ─────────────────│  "All data confirmed, I'm closing too"
    │                                   │
    │──────────── ACK ─────────────────▶│  "Got your FIN. Connection closed."
    │                                   │
   (connection fully terminated)
```

---

## 7. TCP Sequence Numbers

When data is broken into multiple packets (which always happens for large files), those packets don't necessarily all travel the same path or arrive in the same order they were sent. TCP uses **Sequence Numbers** to solve this.

- Every packet is stamped with a **sequence number** indicating its position in the overall data stream.
- Even if packet #2 arrives before packet #1 (due to different network routes), the receiver can **reorder them correctly** using the sequence numbers.
- **Analogy from the lecture:** Imagine sending three photos — you number them 1, 2, 3. If photo 3 arrives first and photo 2 arrives last, the receiver still knows the correct order based on the numbers, and will reassemble them properly.

> **Why this matters for security:** Sequence number prediction was historically used in **TCP session hijacking attacks** — understanding sequence numbers is why this attack was possible and why modern TCP implementations randomize the initial sequence number.

---

## 8. UDP — User Datagram Protocol

UDP is a **connectionless** protocol. Unlike TCP, it makes **no attempt to establish a connection before sending**, does not acknowledge receipt, does not check if data arrived, and does not retransmit lost packets.

### How UDP works:

1. A request arrives.
2. UDP immediately starts sending responses — no handshake, no confirmation, no waiting.
3. It keeps sending data without checking whether the other side received it.

```
CLIENT                              SERVER
    │──── Request ──────────────────▶│
    │◀─── Response (keeps coming) ───│  (no SYN, no ACK, no confirmation)
```

### Core characteristics of UDP:

- **No connection establishment** — data starts flowing immediately
- **No acknowledgment** — sender has no confirmation data arrived
- **No retransmission** — dropped packets are simply lost; UDP doesn't resend
- **No guaranteed ordering** — packets may arrive in any sequence
- **Faster** than TCP — because none of the overhead (handshakes, ACKs, retransmits) exists
- **Less reliable** — some packet loss is acceptable in use cases where speed matters more

### When is UDP preferable?

UDP is used in situations where **speed matters more than perfect reliability** — particularly:

- **Live video/audio streaming** (a dropped frame in a video call is better than a delay caused by retransmitting it)
- **Online gaming** (real-time position updates where a stale delayed update is worse than a lost one)
- **Broadcasting** (sending the same data to many recipients simultaneously — TCP's per-connection handshake doesn't scale well for broadcast)
- **DNS lookups** (a simple request-response query where retrying is easy if the response doesn't arrive)

---

## 9. TCP vs UDP — Full Side-by-Side Comparison

| Aspect                             | TCP                                          | UDP                                          |
| ---------------------------------- | -------------------------------------------- | -------------------------------------------- |
| **Full Name**                      | Transmission Control Protocol                | User Datagram Protocol                       |
| **Connection Type**                | Connection-oriented (handshake first)        | Connectionless (fire and forget)             |
| **Reliability**                    | High — guarantees delivery                   | Low — no delivery guarantee                  |
| **Speed**                          | Slower (due to handshake + ACKs)             | Faster (no overhead)                         |
| **Packet ordering**                | Guaranteed (via sequence numbers)            | Not guaranteed                               |
| **Acknowledgments**                | Yes — receiver confirms every packet         | No                                           |
| **Retransmission**                 | Yes — lost packets are resent                | No — lost packets are just gone              |
| **Error checking**                 | Yes — comprehensive                          | Minimal                                      |
| **Connection setup**               | Three-Way Handshake required                 | None                                         |
| **Connection close**               | Formal FIN sequence                          | None                                         |
| **Use cases**                      | Web, email, file transfer, SSH               | DNS, live streaming, gaming, VoIP, broadcast |
| **Checks if other side is online** | Yes (if SYN gets no reply, connection fails) | No (sends data regardless)                   |

### Key insight from the lecture:

> **TCP is like a certified letter service** — you know exactly when it was delivered, and you get confirmation. **UDP is like dropping a flyer through a door slot** — you don't know if anyone picked it up, but it's fast and you can send thousands quickly.

---

## 10. Which Services Use TCP vs UDP

The lecture provides a list showing that **most well-known services use TCP** because reliability matters more than raw speed for the majority of internet communication. The table below expands on the lecture's list with the correct protocol assignments:

| Port    | Service | Protocol           | Reason                                                                              |
| ------- | ------- | ------------------ | ----------------------------------------------------------------------------------- |
| 20 / 21 | FTP     | **TCP**            | File transfer must be reliable; missing data corrupts files                         |
| 22      | SSH     | **TCP**            | Remote login sessions cannot tolerate dropped commands                              |
| 23      | Telnet  | **TCP**            | Terminal sessions require ordered, reliable delivery                                |
| 25      | SMTP    | **TCP**            | Emails must arrive complete and in order                                            |
| 53      | DNS     | **Both TCP & UDP** | UDP for standard queries (fast); TCP for large responses or zone transfers          |
| 69      | TFTP    | **UDP**            | Trivial FTP — intentionally simple, used in environments where speed matters        |
| 80      | HTTP    | **TCP**            | Web pages must load completely and correctly                                        |
| 110     | POP3    | **TCP**            | Email retrieval must be complete                                                    |
| 123     | NTP     | **UDP**            | Time sync is a simple one-way broadcast; speed matters, perfect reliability less so |
| 143     | IMAP    | **TCP**            | Email management requires reliable, ordered sessions                                |
| 443     | HTTPS   | **TCP**            | Encrypted web traffic — reliability essential                                       |

> **Key observation from lecture:** TCP is by far the more commonly used protocol in everyday internet activity because most services prioritize **correctness and completeness** of data over raw speed. UDP's niche is specifically for time-sensitive, real-time, or broadcast applications where a tiny bit of data loss is tolerable.

---

## 11. Corrections & Clarifications to the Original Transcript

| Transcript said                                                                       | Correct / Clarified version                                                                                                                                        | Why it matters                                                                                                                                               |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| UDP full form is "User Data Protocol"                                                 | **User Datagram Protocol**                                                                                                                                         | "Datagram" is the specific technical term for a self-contained packet of data in UDP. This is the correct, standard full form.                               |
| SYN flag described as "synchronized" and seems to suggest it synchronizes the content | More precisely, SYN synchronizes **sequence numbers** between the two sides at the start of a connection                                                           | This nuance matters in security — sequence number randomization was introduced specifically to prevent SYN/sequence-based attacks.                           |
| Connection termination shown as involving only FIN flags back and forth               | Standard TCP termination is a **4-step process** (FIN → ACK → FIN → ACK)                                                                                           | The transcript describes this correctly in spirit but doesn't clearly enumerate the four distinct steps — easy to miscount on a first read.                  |
| DNS listed as using only UDP                                                          | DNS uses **both TCP and UDP (port 53)**                                                                                                                            | Standard DNS queries use UDP; DNS zone transfers and responses larger than 512 bytes use TCP. This distinction matters for port scanning and firewall rules. |
| Only six flags mentioned                                                              | TCP technically has **up to 9 flag bits** in modern implementations (the original RFC 793 had 6; later additions include NS, CWR, ECE for congestion notification) | For foundational study, the 6 flags covered are the most important and the ones that appear most frequently in security tools and exam contexts.             |

---

## 12. Why This Matters for Cybersecurity

This lecture's content connects directly to many core cybersecurity topics:

- **SYN Flood Attack (DoS):** An attacker sends thousands of SYN packets to a server but never completes the handshake (never sends the final ACK). The server allocates resources for each half-open connection and eventually runs out of capacity, denying service to legitimate users. Understanding the three-way handshake makes this attack immediately obvious.

- **TCP Session Hijacking:** By predicting or intercepting sequence numbers, an attacker can inject themselves into an existing TCP session. Sequence numbers (Section 7) are the mechanism that makes this possible — and also why modern systems randomize initial sequence numbers as a defense.

- **Port Scanning with Nmap:** When Nmap performs a "SYN scan" (also called a "half-open scan" or "stealth scan"), it sends only the SYN packet and never completes the handshake — letting it check if a port is open without establishing a full connection, reducing the chance of being logged. This only makes sense once you understand the handshake.

- **RST Injection:** An attacker can forge RST packets with spoofed source IPs to abruptly terminate established TCP connections between two parties — a technique used in connection disruption attacks and sometimes by censorship systems.

- **Firewall Rules and IDS Signatures:** Intrusion detection systems (IDS) and firewalls analyze TCP flags constantly — unusual flag combinations (e.g., a FIN+SYN together, or a URG+PSH+FIN together, called a "Xmas scan") are red flags for port scanning or exploitation attempts.

- **DNS Amplification Attack (DDoS):** Because DNS uses UDP (connectionless), attackers spoof a victim's IP as the source address and send DNS queries to many DNS servers — each server sends its (large) reply to the victim, amplifying the traffic. This works precisely because UDP has no handshake to verify the true source.

---

## 13. Quick Revision Table

| Concept                   | One-line Summary                                                                           |
| ------------------------- | ------------------------------------------------------------------------------------------ |
| **Protocol**              | A standardized set of rules governing how data is formatted and exchanged between systems  |
| **TCP**                   | Transmission Control Protocol — connection-oriented, reliable, ordered, slow               |
| **UDP**                   | User Datagram Protocol — connectionless, fast, unreliable, no ordering guarantee           |
| **SYN flag**              | Initiates a new connection; always the first step                                          |
| **ACK flag**              | Acknowledges receipt of something (SYN, data, or FIN)                                      |
| **FIN flag**              | Gracefully signals end of data and requests connection closure                             |
| **RST flag**              | Abruptly resets and terminates a connection due to error or rejection                      |
| **PSH flag**              | Forces immediate delivery of buffered data to avoid incomplete transfer                    |
| **URG flag**              | Marks certain data as urgent, to be processed ahead of the queue                           |
| **Three-Way Handshake**   | SYN → SYN+ACK → ACK — establishes a TCP connection before any data flows                   |
| **FIN Sequence (4-step)** | FIN → ACK → FIN → ACK — gracefully terminates a TCP connection                             |
| **Sequence Numbers**      | Numbers attached to packets to ensure correct ordering even if they arrive out of sequence |
| **TCP use cases**         | Web (HTTP/HTTPS), email (SMTP/POP3/IMAP), file transfer (FTP), remote access (SSH)         |
| **UDP use cases**         | DNS queries, live streaming, online gaming, VoIP, NTP, broadcast, TFTP                     |

---

## 14. Self-Test Questions

1. In your own words, what is a protocol and why are protocols necessary in networking?
2. What is the full name of TCP, and what is the full name of UDP?
3. Describe the Three-Way Handshake step by step — what flags are exchanged, in what order, and what does each one mean?
4. What is the difference between a FIN flag and a RST flag? When is each one used?
5. Why is the ACK flag the most frequently sent TCP flag?
6. What does a sequence number do, and why would incorrect ordering of packets be a problem?
7. Why is UDP faster than TCP, and what does it sacrifice to achieve that speed?
8. Give three real-world use cases where UDP is the better choice over TCP, and explain why in each case.
9. Why does DNS use both TCP and UDP, rather than just one of them?
10. How does the SYN Flood attack exploit the Three-Way Handshake mechanism?

---

## What's Next

This lecture established the two transport-layer protocols (TCP and UDP) and how TCP ensures reliable communication through flags, handshakes, acknowledgments, and sequence numbers. Likely upcoming topics include:

- **OSI Model and TCP/IP Model** — where TCP and UDP sit within the full layered model of networking, and how the layers interact
- **Packet structure in depth** — what's actually inside a TCP/UDP/IP packet header
- **Firewalls and packet filtering** — how firewalls use TCP flag inspection and port rules to allow or deny traffic
- **Practical tools:** Wireshark (to visually observe three-way handshakes and flag sequences on real traffic), Nmap (to see how SYN scans exploit the handshake), Netcat (to manually create TCP/UDP connections)

_TCP's three-way handshake and the six flags are among the most frequently tested topics in cybersecurity certifications (CompTIA Security+, CEH, OSCP) and appear constantly in real-world penetration testing, traffic analysis, and incident response work — this is foundational knowledge worth thoroughly memorizing._
