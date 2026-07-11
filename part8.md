# Cyber Security Course — Common Network Services & Ports

> Detailed, structured notes compiled from the course video transcript (Hacker channel). Use the index below to jump to any section. Note: this transcript appears to be machine-translated from Hindi, so wording has been cleaned up and clarified where the meaning was clear from context.

---

## 📑 Table of Contents

1. [Introduction & Recap](#1-introduction--recap)
2. [What Is a "Service"? (Software vs. Service Analogy)](#2-what-is-a-service-software-vs-service-analogy)
3. [Why This Matters for Cyber Security](#3-why-this-matters-for-cyber-security)
4. [Services That Aren't Useful for Access (SCTP Example)](#4-services-that-arent-useful-for-access-sctp-example)
5. [FTP — File Transfer Protocol (Ports 20 & 21)](#5-ftp--file-transfer-protocol-ports-20--21)
6. [SSH & Telnet — Remote Access (Ports 22 & 23)](#6-ssh--telnet--remote-access-ports-22--23)
7. [SMTP & POP3 — Mail Services (Ports 25 & 110)](#7-smtp--pop3--mail-services-ports-25--110)
8. [DNS — Domain Name System (Port 53)](#8-dns--domain-name-system-port-53)
9. [HTTP & HTTPS — Web Traffic (Ports 80 & 443)](#9-http--https--web-traffic-ports-80--443)
10. [NTP — Network Time Protocol (Port 123)](#10-ntp--network-time-protocol-port-123)
11. [IMAP — Internet Message Access Protocol (Port 143)](#11-imap--internet-message-access-protocol-port-143)
12. [RPC / RPC Bind (Port 111)](#12-rpc--rpc-bind-port-111)
13. [NetBIOS / Samba — File & Printer Sharing (Ports 139, 445, 137)](#13-netbios--samba--file--printer-sharing-ports-139-445-137)
14. [rlogin — Remote Login Service](#14-rlogin--remote-login-service)
15. [Bind Shell Example (Port 1524)](#15-bind-shell-example-port-1524)
16. [NFS — Network File System (Port 2049)](#16-nfs--network-file-system-port-2049)
17. [Quick Reference: Port Number Cheat Sheet](#17-quick-reference-port-number-cheat-sheet)
18. [Conclusion & What's Next](#18-conclusion--whats-next)

---

## 1. Introduction & Recap

- This video follows an earlier session covering **TCP** and its flags, demonstrated through a live project.
- **Today's focus:** understanding how many **network services** exist in the cyber security field, what these services actually do, and how they can be used to **access a system**.

---

## 2. What Is a "Service"? (Software vs. Service Analogy)

To explain the difference between **software** and **service**, the instructor uses a messaging analogy:

- You use various messaging **software** — WhatsApp, Facebook Messenger, standard SMS apps, etc.
- What are these programs actually _providing_? They're providing the **service** of sending and receiving messages.
- **Key distinction:**
  - **Service** = the underlying function being performed (e.g., "sending messages").
  - **Software** = the actual application/program that uses that service (e.g., WhatsApp, Messenger).

This same distinction applies across networking and cyber security: many different services exist, each with a distinct function, and various pieces of software can make use of a given service.

---

## 3. Why This Matters for Cyber Security

- Not all running services are useful from an access/penetration-testing perspective — some services only carry data back and forth without providing a way to actually access or control a system.
- The course will focus specifically on **common services** that are relevant to system access, along with their **port numbers**, since recognizing these during a scan is an essential skill.

---

## 4. Services That Aren't Useful for Access (SCTP Example)

- Example given: a protocol like **SCTP** (mentioned alongside HTTP/HTTPS in context) primarily works to **carry requests and bring back responses** — essentially just transporting data.
- **Key point:** You cannot gain system _access_ through such a service — at most, you can observe **what data/request was sent and what response came back**. There's no foothold for access via this type of service alone.

---

## 5. FTP — File Transfer Protocol (Ports 20 & 21)

| Detail        | Value                                                    |
| ------------- | -------------------------------------------------------- |
| **Full form** | File Transfer Protocol                                   |
| **Ports**     | **20** (data/receiving) and **21** (commands/sending)    |
| **Function**  | Transferring and receiving files (uploading/downloading) |

- FTP's core job is **file transfer** — sending and receiving files between systems.
- Any software that needs to send/receive files (the example given: sending a file through a messaging app like VK) may rely on FTP as the underlying service, even though the **software** the user interacts with is different from the **service** doing the actual work.

---

## 6. SSH & Telnet — Remote Access (Ports 22 & 23)

| Detail       | Value                                                          |
| ------------ | -------------------------------------------------------------- |
| **SSH**      | Port **22** — "Secure Shell"                                   |
| **Telnet**   | Port **23**                                                    |
| **Function** | Both provide **remote access** to a computer over the internet |

- **Remote Desktop** software is a real-world example of an application that relies on one of these underlying remote-access services.
- **Key difference between the two:**
  - **SSH (Secure Shell)** — more **secure**.
  - **Telnet** — **not very secure**, and provides **command-line access**; historically the more common remote access method before SSH became standard.

---

## 7. SMTP & POP3 — Mail Services (Ports 25 & 110)

| Detail       | Value                                                                      |
| ------------ | -------------------------------------------------------------------------- |
| **SMTP**     | Port **25** — "Simple Mail Transfer Protocol"                              |
| **POP3**     | Port **110** — a version of POP, described as **less secure**              |
| **Function** | Transferring/sending mail (SMTP) and receiving/storing mail locally (POP3) |

- **SMTP** is used for **sending** mail — e.g., when sending an email through Gmail, SMTP is the service being used behind the scenes.
- **POP3** is used to **save/store received mail on local storage** (your local hard disk), as opposed to services like Gmail that store mail on the provider's own servers rather than locally.
- Both services fall under the broader category of **mail services**.

---

## 8. DNS — Domain Name System (Port 53)

| Detail        | Value                                                      |
| ------------- | ---------------------------------------------------------- |
| **Full form** | Domain Name System                                         |
| **Port**      | **53**                                                     |
| **Function**  | Translates domain names into IP addresses (and vice versa) |

**How it works (explained via example):**

1. You have a website/email address you want to visit, but your PC doesn't inherently know that website's **public IP address**.
2. Your PC generates a request packet and sends it to your **router**.
3. The router doesn't know the website's IP address either, so it forwards the request to a **DNS server**.
4. The DNS server looks up the domain name and returns the corresponding **IP address**.
5. That IP address is then used to actually reach the website.

- DNS is sometimes described as the **"Address Book of the Internet"** — it stores the IP addresses and domain names of websites worldwide.
- Every system has a **DNS setting** (e.g., "Preferred DNS") visible in network/IP configuration settings, pointing to a default DNS server address.

---

## 9. HTTP & HTTPS — Web Traffic (Ports 80 & 443)

| Detail       | Value                                                       |
| ------------ | ----------------------------------------------------------- |
| **HTTP**     | Port **80** — "Hyper Text Transfer Protocol"                |
| **HTTPS**    | Port **443** — HTTP + **SSL** ("Secure Socket Layer")       |
| **Function** | Sending requests to, and receiving responses from, websites |

- The **"S"** in HTTPS stands for **Secure** (via SSL — Secure Socket Layer), making it the more secure version of HTTP.
- Both protocols are fundamentally about handling **web requests and responses** — whenever you see "http://" or "https://" in front of a website address, one of these two services is being used.

---

## 10. NTP — Network Time Protocol (Port 123)

| Detail        | Value                                    |
| ------------- | ---------------------------------------- |
| **Full form** | Network Time Protocol                    |
| **Port**      | **123**                                  |
| **Function**  | Synchronizes the time/clock on a machine |

- NTP handles **time synchronization** — ensuring a machine's system clock reflects the correct, consistent time, and manages related time settings.

---

## 11. IMAP — Internet Message Access Protocol (Port 143)

| Detail        | Value                                                   |
| ------------- | ------------------------------------------------------- |
| **Full form** | Internet Message Access Protocol                        |
| **Port**      | **143**                                                 |
| **Function**  | Enables **remote access to email/mail** from any device |

- While SMTP and POP3 handle sending/receiving and local storage of mail, **IMAP** solves a different problem: accessing your mailbox **remotely from a different device** — e.g., if you're away from your primary device but need to check the same mailbox from another computer.

---

## 12. RPC / RPC Bind (Port 111)

| Detail        | Value                                                                      |
| ------------- | -------------------------------------------------------------------------- |
| **Full form** | Remote Procedure Call                                                      |
| **Port**      | **111** (RPC Bind/Binder)                                                  |
| **Function**  | High-level protocol enabling remote program/procedure calls across systems |

- **RPC** is described as a **high-level communication protocol** that enables smooth, high-quality communication between systems, avoiding communication issues.
- **RPC Bind (RPC Binder)** acts as a kind of **server/directory**: if you don't know the address of a target system/program directly, you first query the RPC Binder server, which tells you which systems/networks are available and reachable.
- **Practical use case:** if a program exists on a remote system and you need to **call/run it** from another system, RPC (with the help of RPC Bind to locate the right server/IP) enables this — you first discover available systems via RPC Bind, then use RPC to actually invoke the remote program.
- The instructor notes this concept will make more sense once demonstrated practically in a later, hands-on system access session.

---

## 13. NetBIOS / Samba — File & Printer Sharing (Ports 139, 445, 137)

| Detail          | Value                                      |
| --------------- | ------------------------------------------ |
| **Common name** | Often known by its service name, **Samba** |
| **Ports**       | **139**, **445**, and **137**              |
| **Function**    | Network printer sharing and file sharing   |

- **Samba** enables **printer sharing** and **file sharing** across a network.
- **Real-world example:** printing wirelessly to a network printer from a different device — this relies on file-sharing functionality, since a file typically needs to be shared/transmitted to the printer before a print job can complete.

---

## 14. rlogin — Remote Login Service

- **Function:** helps a user **log in to a remote host**.
- Example given: multiple users (e.g., three or four) logging into a shared host, each being identified/authenticated through this login service.
- The instructor notes this service (along with a few other minor ones) doesn't have a widely relevant common port worth memorizing for this course's purposes, and moves on relatively quickly, focusing instead on the more broadly useful, common ports.

---

## 15. Bind Shell Example (Port 1524)

| Detail                    | Value                                                                                                         |
| ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Port shown in example** | **1524**                                                                                                      |
| **Context**               | A scanned machine (referred to as "Metasploitable" in the demo) showing a shell/terminal running on this port |

- The instructor demonstrates a scan result showing a **shell (terminal) running** on port 1524 on the target/demo machine — described as essentially a remote terminal/command-line access point exposed on that port.
- (Note: this reflects a real-world scanning example rather than a formally standardized "common service," included here as encountered during the demonstration.)

---

## 16. NFS — Network File System (Port 2049)

| Detail        | Value                                                                                        |
| ------------- | -------------------------------------------------------------------------------------------- |
| **Full form** | Network File System (referred to in the transcript as "network file sharing")                |
| **Port**      | **2049**                                                                                     |
| **Function**  | Sharing files across multiple devices/systems on a local network via a shared storage device |

**Example given to illustrate the problem NFS solves:**

- Imagine an office with **~100 users**, and you need to share a **100 GB file** with all of them.
- Giving each of the 100 users their own separate 100 GB copy would consume excessive storage and be impractical (e.g., copying via USB drive to each person individually).
- **Solution:** Use a single shared **storage device** on the network, and give **all users on the local network access to it**. Everyone can then access the shared file(s) directly from that one device, rather than needing individual copies.
- This is the core function of NFS — sharing files across multiple systems/devices through one central device on a local network.
- The instructor notes that NFS is also relevant in the context of **digital forensics**, to be covered in more detail elsewhere.

---

## 17. Quick Reference: Port Number Cheat Sheet

| Port(s)                       | Service             | Full Name / Notes                                                        |
| ----------------------------- | ------------------- | ------------------------------------------------------------------------ |
| 20, 21                        | **FTP**             | File Transfer Protocol — file transfer (20 = data, 21 = control/sending) |
| 22                            | **SSH**             | Secure Shell — secure remote access                                      |
| 23                            | **Telnet**          | Remote access, command-line, less secure                                 |
| 25                            | **SMTP**            | Simple Mail Transfer Protocol — sending mail                             |
| 53                            | **DNS**             | Domain Name System — resolves domain ↔ IP                                |
| 80                            | **HTTP**            | Hyper Text Transfer Protocol — web requests/responses                    |
| 110                           | **POP3**            | Post Office Protocol v3 — receiving/local mail storage, less secure      |
| 111                           | **RPC / RPC Bind**  | Remote Procedure Call — remote program invocation                        |
| 123                           | **NTP**             | Network Time Protocol — time synchronization                             |
| 137, 139, 445                 | **NetBIOS / Samba** | File and printer sharing                                                 |
| 143                           | **IMAP**            | Internet Message Access Protocol — remote mail access                    |
| 443                           | **HTTPS**           | HTTP + SSL — secure web requests/responses                               |
| 2049                          | **NFS**             | Network File System — shared file access across a network                |
| _(varies, e.g. 1524 in demo)_ | **Bind shell**      | Remote terminal/shell access exposed on a scanned port                   |

---

## 18. Conclusion & What's Next

- This session covered the most common network **services** and their associated **port numbers** — the instructor emphasizes that these are worth memorizing, since recognizing them during a scan is foundational to further practical work.
- The instructor notes that the _real_ understanding of how these services can be leveraged for **system access** will come in **future hands-on/practical sessions**, where these services will actually be used to access systems.

---

_End of course notes._
