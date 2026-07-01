# IoT & OT Hacking — Theory & Practical Introduction (In-Depth Notes)

## Internet of Things, Operational Technology, IIoT, Security Vulnerabilities, IoT Layers, OT Components, and MQTT Protocol Practical

> **Course:** Ethical Hacking & Cybersecurity Series — IoT & OT Hacking Module
> **Disclaimer:** All content in these notes is strictly for **educational purposes only**. Do not use any technique or tool described here against devices, networks, or systems you do not own or have explicit written authorization to test. Always operate within the law and applicable regulations.

---

## Table of Contents

1. [Why This Module Exists — The Big Picture](#1-why-this-module-exists--the-big-picture)
2. [What Is IoT (Internet of Things)?](#2-what-is-iot-internet-of-things)
3. [Common IoT Devices — Examples from Daily Life](#3-common-iot-devices--examples-from-daily-life)
4. [How IoT Devices Work — The Communication Flow](#4-how-iot-devices-work--the-communication-flow)
5. [The Five Layers of an IoT Architecture](#5-the-five-layers-of-an-iot-architecture)
6. [Security Problems in IoT — Layer-by-Layer Vulnerabilities](#6-security-problems-in-iot--layer-by-layer-vulnerabilities)
7. [What Is OT (Operational Technology)?](#7-what-is-ot-operational-technology)
8. [Common OT Device Use Cases](#8-common-ot-device-use-cases)
9. [OT Components — ICS, SCADA, DCS, RTU, PLC](#9-ot-components--ics-scada-dcs-rtu-plc)
10. [IIoT — The Convergence of IT and OT](#10-iiot--the-convergence-of-it-and-ot)
11. [IoT vs OT vs IIoT — Comparison Table](#11-iot-vs-ot-vs-iiot--comparison-table)
12. [Practical: Simulating an IoT Device and Capturing MQTT Traffic in Wireshark](#12-practical-simulating-an-iot-device-and-capturing-mqtt-traffic-in-wireshark)
13. [MQTT Protocol — What It Is and Why IoT Uses It](#13-mqtt-protocol--what-it-is-and-why-iot-uses-it)
14. [CAN Bus — The Next Layer of IoT Attack Surface](#14-can-bus--the-next-layer-of-iot-attack-surface)
15. [Why IoT Security Is Especially Difficult](#15-why-iot-security-is-especially-difficult)
16. [Why This Matters for Cybersecurity](#16-why-this-matters-for-cybersecurity)
17. [Quick Revision Table](#17-quick-revision-table)
18. [Self-Test Questions](#18-self-test-questions)

---

## 1. Why This Module Exists — The Big Picture

Most people think of hacking in terms of computers and servers. This module shifts that view dramatically: **the internet has entered almost every physical device around you** — your watch, your car, your hospital's MRI scanner, the power grid, water treatment plants, and even traffic signals.

This creates an enormous and largely underappreciated attack surface. The opening statement of this lecture is deliberately provocative:

> _"Why, even if you want to, can you not keep yourself secure? Why can you not save your data from the internet?"_

The answer is IoT. Devices you never thought of as "internet-connected computers" are collecting your data, transmitting it to cloud servers, and communicating with other systems — often with weak or no security.

This module covers:

- What IoT devices are and how they work
- What OT (Operational Technology) devices are and how they differ from IoT
- What IIoT is (the convergence of both)
- Security vulnerabilities at each layer
- The components inside OT systems (ICS, SCADA, DCS, RTU, PLC)
- A live practical: simulating an IoT temperature sensor, sending a command, and capturing the resulting MQTT traffic in Wireshark

---

## 2. What Is IoT (Internet of Things)?

**Full form:** Internet of Things

**Plain definition:** IoT refers to physical devices — embedded with sensors, software, and connectivity — that collect data from the real world and transmit it over the internet to cloud servers, from where it can be monitored and controlled remotely.

### The three core elements of any IoT device:

| Element                         | Description                                                                                                                                     |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sensors**                     | Hardware components that measure physical phenomena: temperature, pressure, humidity, sound, vibration, motion, heart rate, oxygen levels, etc. |
| **Connectivity**                | WiFi, Bluetooth, cellular, Zigbee, or other protocols that allow the device to communicate with a gateway and ultimately with a cloud server    |
| **Remote control / monitoring** | A smartphone app or web dashboard through which a user can view data or send commands to the device from anywhere in the world                  |

### The key insight:

IoT is what happens when ordinary physical objects — lights, locks, watches, cars, medical machines — are given the ability to sense their environment, connect to the internet, and be controlled remotely. They become "smart" because they can now communicate and receive instructions just like a traditional computer.

---

## 3. Common IoT Devices — Examples from Daily Life

| Device                                  | What Sensors / IoT Capability It Has                                                                                                                           |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Smart Watch / Fitness Band**          | Heart rate sensor, SpO2 (blood oxygen) sensor, accelerometer (step counting), GPS; syncs data to a phone app via Bluetooth, then to a cloud server             |
| **CCTV / IP Camera**                    | Image sensor, motion detection sensor; streams video live and stores footage to cloud or local NVR; remotely viewable from a phone                             |
| **Smart Speaker (Alexa, Google Home)**  | Microphone array (sound sensor); voice commands processed in the cloud; controls other IoT devices                                                             |
| **Smart Bulb (WiFi-enabled)**           | WiFi connectivity for remote on/off control; some have motion sensors that turn the bulb on when someone enters the room and off when they leave               |
| **Smart Car / Self-Driving Car**        | Hundreds of sensors (LIDAR, radar, cameras, pressure, GPS, accelerometer); communicates with manufacturer cloud servers for navigation, updates, and telemetry |
| **Smart Washing Machine / Fridge / AC** | WiFi connectivity; allows scheduling, status checks, and adjustments remotely via an app                                                                       |
| **Smart Router**                        | Can be managed remotely; some have traffic monitoring and parental controls accessible via a cloud interface                                                   |
| **Smart Meter (electricity/gas/water)** | Sends real-time usage data to the utility provider without requiring a meter reader to visit                                                                   |
| **Industrial Temperature Sensor**       | Measures temperature at a factory or server room; triggers alerts if thresholds are exceeded                                                                   |

**The key takeaway:** These devices collect data about you — your health, your location, your habits, your home's state — and store it on remote servers. A security vulnerability in any of these devices is a potential data leak of very personal information.

---

## 4. How IoT Devices Work — The Communication Flow

Understanding the data flow is essential for understanding where attacks can occur. Here is the complete journey data takes in an IoT system:

### Step-by-step flow (using a smartwatch as the example):

```
1. SENSORS collect data
   (e.g., heart rate sensor detects BPM, SpO2 sensor measures blood oxygen)
         │
         ▼
2. DATA is stored temporarily on the device
   (the watch's local memory holds the latest readings)
         │
         ▼
3. DATA is sent to an IoT GATEWAY
   (gateway = the access point between the local device network and the internet
    — could be your phone connected via Bluetooth, or a dedicated hub, or a WiFi router)
         │
         ▼
4. Gateway pushes data to the CLOUD SERVER
   (data is stored permanently in the cloud for historical tracking and analysis)
         │
         ▼
5. When you open the APP on your phone and request data:
   Your phone sends a request → to the gateway → to the cloud server
   Cloud server retrieves the data → sends it back to the gateway → gateway sends it to your phone
         │
         ▼
6. You see your health data displayed on the app
```

### Two-way communication also exists:

Commands also travel the reverse path:

```
Your app sends a command (e.g., "start workout tracking")
→ Cloud server receives it
→ Server sends it to the gateway
→ Gateway sends it to the physical device (watch)
→ Watch starts tracking
```

### Why the gateway matters:

The gateway (covered in networking modules — it's the "first IP of the router" in a home network context) is the critical bridge between the local sensor world and the public internet. In IoT, the gateway may be a dedicated IoT hub, a smartphone, or a small embedded device that aggregates sensor data from multiple IoT devices and pushes them to the cloud.

---

## 5. The Five Layers of an IoT Architecture

IoT devices have their own layered model, similar in concept to the OSI model for traditional networking. There are **5 layers** in a typical IoT architecture:

---

### Layer 1 — Application Layer (Top Layer)

- **Role:** Provides the **user interface** — the app on your phone, the web dashboard, the screen on a smart thermostat — through which a human monitors data and controls devices.
- **Analogy:** This is equivalent to the Application Layer in the OSI model — the part the user directly interacts with.
- **Example:** The Fitbit/Samsung Health app on your phone showing your heart rate graph.

---

### Layer 2 — Middleware Layer

- **Role:** Processes, filters, and manages the raw data coming in from sensors. It figures out what data is useful, what can be discarded, and routes the relevant, processed data up to the Application Layer (the app).
- **Think of it as:** A data processing engine that sits between the hardware sensors and the user-facing app. It translates raw sensor readings (e.g., raw electrical signals from a heart rate sensor) into meaningful, structured data (e.g., "72 BPM").

---

### Layer 3 — Internet Layer

- **Role:** Manages the actual internet connection — establishing and maintaining the secure communication channel between the IoT device/gateway and the cloud server.
- **Function:** Ensures data is transmitted securely between endpoints (the device side and the server side).

---

### Layer 4 — Access Gateway Layer

- **Role:** This is where data **enters and exits** the IoT network — the actual gateway described in Section 4. It manages the connection between the sensor/device network and the wider internet.
- **Two-way:** Data flows outward (sensor readings to the server) and inward (commands from the server to the device) through this layer.

---

### Layer 5 — Edge Technology Layer (Bottom Layer)

- **Role:** The actual **physical sensors, devices, and machines** — the hardware that touches the real world. This is where actual data collection happens.
- **Example:** The heart rate optical sensor chip inside your watch. The motion-detection photoelectric cell in a security light. The temperature probe in an industrial furnace.
- **"Edge"** here means "at the edge of the network" — as far from the cloud server as possible, as close to the physical reality being measured as possible.

---

### IoT Layer Summary Diagram

```
┌─────────────────────────────────────────────────────┐
│  Layer 1 — APPLICATION LAYER                        │
│  (User-facing app / dashboard / UI)                 │
├─────────────────────────────────────────────────────┤
│  Layer 2 — MIDDLEWARE LAYER                         │
│  (Data processing, filtering, management)           │
├─────────────────────────────────────────────────────┤
│  Layer 3 — INTERNET LAYER                           │
│  (Secure communication between device and server)   │
├─────────────────────────────────────────────────────┤
│  Layer 4 — ACCESS GATEWAY LAYER                     │
│  (Data entry/exit point — the IoT gateway)          │
├─────────────────────────────────────────────────────┤
│  Layer 5 — EDGE TECHNOLOGY LAYER                    │
│  (Physical sensors, devices, machines)              │
└─────────────────────────────────────────────────────┘
                         │
                  Real World (physical environment)
```

> **Important note:** OT (Operational Technology) devices use the **same five-layer architecture** as IoT devices. The layers are identical — only the scale, environment, and specific devices differ.

---

## 6. Security Problems in IoT — Layer-by-Layer Vulnerabilities

Because an IoT system spans multiple distinct layers (application, network, mobile, cloud), **each layer introduces its own set of vulnerabilities**. This is why IoT security is so complex — attacking it doesn't require a single vulnerability; there are attack surfaces across the entire stack.

### Application Layer Vulnerabilities

| Vulnerability                      | Description                                                                                                            |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Lack of input validation**       | The app doesn't properly check data entered by users or received from sensors — can be exploited for injection attacks |
| **Weak or missing authentication** | The app doesn't properly verify who is sending commands — unauthorized users could control the device                  |
| **Missing authorization**          | Even authenticated users may be able to perform actions they shouldn't have access to                                  |
| **No automatic security updates**  | Devices don't update their firmware/software automatically — known vulnerabilities remain unpatched indefinitely       |

### Network Layer Vulnerabilities

| Vulnerability                            | Description                                                                                                                            |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Improper firewall configuration**      | No filtering of what traffic is allowed to reach or leave the device                                                                   |
| **No encryption in transit**             | Data sent between the device, gateway, and server travels in plaintext — anyone who intercepts the traffic can read it                 |
| **Weak or missing network segmentation** | IoT devices are often on the same network as sensitive systems — a compromised IoT device provides a foothold into the broader network |

### Mobile Layer Vulnerabilities

| Vulnerability                          | Description                                                                                                                   |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Insecure APIs**                      | The API that moves data between the app and the cloud server may have authentication flaws, allowing unauthorized data access |
| **Unencrypted communication channels** | Data moving between the mobile app and the backend server may not be encrypted                                                |
| **Insecure local storage**             | Sensitive data (credentials, tokens, health records) stored in the phone's local storage without encryption                   |

### Cloud Layer Vulnerabilities

| Vulnerability                      | Description                                                                                     |
| ---------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Improper authentication**        | The cloud server doesn't properly verify who is connecting — unauthorized access to stored data |
| **No encryption for stored data**  | Data stored in the cloud server is kept in plaintext — any breach exposes everything            |
| **Insecure web server interfaces** | The server's management interface may have web vulnerabilities (SQL injection, XSS, etc.)       |
| **Insufficient access controls**   | Users may access other users' data due to missing or broken access controls                     |

### The Combined Risk

An IoT system is only as secure as its weakest layer. Because all four layers (Application + Network + Mobile + Cloud) must work together, a vulnerability in any single layer can compromise the entire system and expose all data collected by every sensor on that device.

---

## 7. What Is OT (Operational Technology)?

**Full form:** Operational Technology

**Plain definition:** OT refers to hardware and software systems that **monitor and control physical industrial processes** — as opposed to IoT, which is focused on consumer and smaller-scale devices. OT operates at industrial scale in critical infrastructure environments.

### Key distinctions from IoT:

| Aspect                    | IoT                                              | OT                                                             |
| ------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| **Scale**                 | Individual / consumer (watches, home appliances) | Industrial / critical infrastructure (power plants, factories) |
| **Users**                 | General public                                   | Industrial operators, engineers                                |
| **Stakes if compromised** | Personal data leaked                             | Power grid failure, gas leak, hospital shutdown                |
| **Lifecycle**             | Short (2–5 years)                                | Very long (10–30+ years)                                       |
| **Update frequency**      | Frequent                                         | Rarely updated — can't afford downtime                         |

### Both IoT and OT use the same five-layer architecture and share the same security vulnerabilities — the difference is the environment they operate in and the consequences of a breach.

---

## 8. Common OT Device Use Cases

| Sector                       | OT Applications                                                                                                |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Utilities — Electricity**  | Electrical grids controlled and monitored remotely; automated load balancing                                   |
| **Utilities — Water**        | Water treatment and distribution systems; automated chemical dosing                                            |
| **Utilities — Gas**          | Gas pipeline monitoring; automated pressure regulation and leak detection                                      |
| **Healthcare**               | ECG machines, MRI scanners, microscopes — all internet-connected and remotely controllable in modern hospitals |
| **Transport**                | Traffic signals (remotely controllable), truck/fleet tracking with GPS sensors, autonomous vehicles            |
| **Office/Building**          | Fire suppression systems, biometric attendance systems (fingerprint/RFID), building access control             |
| **Manufacturing / Robotics** | Robotic assembly lines, automated quality control, CNC machines                                                |
| **Military / Defense**       | Surveillance systems, remotely monitored perimeter security                                                    |

### The threat is real and serious:

As the lecture notes:

- If vulnerabilities in smartwatch sensors are exploited → your health data, age, blood pressure are leaked.
- If surveillance/traffic system OT is compromised → city-level surveillance data is exposed.
- If trucking/logistics OT is breached → supply chain visibility is compromised.
- If medical OT (ECG, MRI) is hacked → patient medical records are exposed AND machine operation could potentially be disrupted (life-threatening).

---

## 9. OT Components — ICS, SCADA, DCS, RTU, PLC

The OT ecosystem is made up of several specialized sub-systems and components. These are the "building blocks" of industrial control:

---

### 9.1 — ICS (Industrial Control System)

- **Full form:** Industrial Control System
- **Role:** The overarching category of systems that **automate and monitor industrial processes** — manufacturing, power generation, water treatment, etc.
- **How it works:** Uses sensors and controllers to collect data from machines and automatically adjust their operation.
- **Where used:** Power plants, manufacturing units, oil refineries.
- **Example:** An assembly line where dozens of machines must coordinate their operations — a single ICS manages and automates the entire coordination.

---

### 9.2 — SCADA (Supervisory Control and Data Acquisition)

- **Full form:** Supervisory Control and Data Acquisition
- **Role:** A specific type of ICS designed to monitor and control **large-scale, geographically distributed** industrial processes from a central location.
- **Interface:** Provides a real-time graphical interface (HMI — Human-Machine Interface) where operators can see the status of all monitored systems.
- **Where used:** Electrical grids, water supply systems, gas pipelines.
- **Example:** An electricity company's control room where operators can see the status of every transformer, generator, and transmission line across an entire city — and send commands to any of them — all through a central SCADA dashboard.

---

### 9.3 — DCS (Distributed Control System)

- **Full form:** Distributed Control System
- **Role:** A control system where the control logic is **distributed** across multiple controllers spread throughout the facility, rather than centralized in one location. All controllers remain interconnected.
- **Designed for:** Complex, continuous manufacturing processes.
- **Where used:** Chemical plants, oil refineries, pharmaceutical manufacturing.
- **Key difference from SCADA:** SCADA is designed for geographically wide, remote systems; DCS is designed for complex local processes within a single facility.

---

### 9.4 — RTU (Remote Terminal Unit)

- **Full form:** Remote Terminal Unit
- **Role:** A field device that **collects sensor data at a remote location** and transmits it back to a central monitoring station (usually a SCADA system). Can operate at long distances from the central station.
- **Where used:** Widely used in SCADA systems for remote data acquisition.
- **Example:** An RTU installed at a remote gas pipeline location reads sensors monitoring gas pressure, temperature, and flow rate, and continuously sends this data to the central SCADA monitoring station hundreds of kilometers away.

---

### 9.5 — PLC (Programmable Logic Controller)

- **Full form:** Programmable Logic Controller
- **Role:** A ruggedized digital computer specifically designed for **industrial automation** — executes programmed logic to control machines and processes based on input conditions.
- **How it works:** Uses IF-THEN logic: "IF temperature exceeds X, THEN shut down heater." "IF conveyor belt speed drops below Y, THEN trigger alarm."
- **Where used:** Manufacturing assembly lines, robotic systems, automated packaging.
- **Example:** A PLC controlling a robotic arm in a car manufacturing plant — determining exactly when, how far, and in which direction the arm moves based on sensor inputs and programmed conditions.

---

### OT Component Hierarchy

```
OT (Operational Technology)
└── ICS (Industrial Control System)
    ├── SCADA (wide-area remote monitoring and control)
    │   └── RTU (remote field device feeding data to SCADA)
    ├── DCS (distributed control for complex local processes)
    └── PLC (programmable logic for specific machine automation)
```

---

## 10. IIoT — The Convergence of IT and OT

**Full form:** Industrial Internet of Things (IIoT)

IIoT is what happens when **traditional OT systems** (SCADA, DCS, PLCs, industrial sensors) are **combined with IT infrastructure** (cloud computing, internet connectivity, big data analytics, programming languages like Python, SQL, Java).

### What each side brings:

| OT brings                      | IT brings                                 |
| ------------------------------ | ----------------------------------------- |
| Physical sensors and actuators | Cloud storage and processing              |
| SCADA systems                  | Internet connectivity                     |
| DCS and PLC controllers        | Network infrastructure                    |
| RTUs                           | Programming languages (Python, Java, SQL) |
| Mechanical devices             | Data analytics and machine learning       |

### The result of convergence:

- **Improved efficiency:** Machines can be remotely monitored and adjusted in real time based on data analytics.
- **Predictive maintenance:** Sensor data analyzed by ML algorithms can predict when a machine is about to fail before it actually does.
- **Better security:** Combining IT security tools (firewalls, intrusion detection, encryption) with OT systems improves overall security posture.
- **Remote control at scale:** An engineer can monitor and adjust a gas pipeline or power grid from a laptop anywhere in the world.

**IIoT = OT (industrial devices and machines) + IT (internet, cloud, analytics)**

---

## 11. IoT vs OT vs IIoT — Comparison Table

| Aspect                          | IoT                               | OT                                     | IIoT                                          |
| ------------------------------- | --------------------------------- | -------------------------------------- | --------------------------------------------- |
| **Full form**                   | Internet of Things                | Operational Technology                 | Industrial Internet of Things                 |
| **Primary users**               | Consumers / general public        | Industrial operators                   | Industrial operators + IT teams               |
| **Scale**                       | Individual / home                 | Industrial / critical infrastructure   | Industrial with IT-grade connectivity         |
| **Examples**                    | Smartwatch, smart bulb, IP camera | SCADA, PLC, DCS, RTU                   | Connected factory floor, smart grid           |
| **Connectivity**                | WiFi, Bluetooth, cellular         | Often proprietary industrial protocols | Industrial protocols + internet               |
| **Security stakes if breached** | Personal data / privacy           | Physical safety, infrastructure        | Both — data AND physical safety               |
| **Architecture**                | 5-layer IoT model                 | Same 5-layer model                     | Same 5-layer model                            |
| **Update frequency**            | Frequent (app updates)            | Rare (uptime-critical)                 | Increasingly frequent as IT practices adopted |

---

## 12. Practical: Simulating an IoT Device and Capturing MQTT Traffic in Wireshark

This practical demonstrates the entire IoT communication chain in a controlled simulation environment — a temperature sensor sending alerts to a server, with traffic captured in Wireshark.

### Setup Overview

The practical uses **two machines** (physical or virtual):

- **Machine 1 (Server):** Runs an MQTT broker — the server that receives messages from IoT devices.
- **Machine 2 (IoT Device Simulator):** Runs a simulation of an IoT device (a temperature sensor) that sends data to the MQTT broker.

This simulates exactly the communication described in Section 4:

```
Simulated IoT Sensor → MQTT Broker (Server) → Wireshark captures the traffic
```

### Step 1 — Start the MQTT Broker (Server Side)

The MQTT broker is started on one machine. When it starts successfully, it outputs:

```
Listening on TCP port 1883
```

**Port 1883** is the standard, unencrypted MQTT port. (Port 8883 is the TLS-encrypted version.)

This server now waits for IoT devices to connect and publish messages.

### Step 2 — Start the IoT Device Simulator

The IoT device simulator (representing the "smartwatch" or "temperature sensor") is started on the second machine. The simulator:

- Has the MQTT broker's IP address configured as its destination.
- Has port 1883 configured as the destination port.
- Begins listening for commands and preparing to publish sensor data.

When the device connects successfully, the Wireshark display on the server side shows a **new connection established** from the device's IP address.

> **Troubleshooting note from lecture:** If the simulator doesn't connect when both are on the same machine, it needs to be moved to a separate machine — the server and the IoT device cannot always share the same host OS instance.

### Step 3 — Explore the IoT Dashboard

The simulation tool provides a dashboard where:

- **Connected devices are listed** (e.g., "TS1 — Temperature Sensor 1")
- **Device details** include device ID, description, and subscribed topics
- **Commands** can be sent to specific devices

In the example, a temperature sensor named **TS1** was created for a company called **NVH Finance**. It is subscribed to receive commands on a specific topic.

### Step 4 — Send a Command to the IoT Device

From the dashboard/server, a message is published:

```
Message to: TS1
Topic: high_temperature
Content: "High Temp"
```

This simulates the server sending an alert command to the temperature sensor — as if the server detected high temperature and is commanding the device to respond.

### Step 5 — Verify the Command Was Received by the Device

Switching to the IoT device simulator window, the received command is visible:

```
Command received: TS1
Message: High Temperature
```

This confirms the complete communication path is working:

```
Server sends command → MQTT broker routes it → Device receives and displays it
```

### Step 6 — Capture the Traffic in Wireshark

Wireshark, running on the Ethernet interface, captures the MQTT packets in real time. After the command is sent, approximately **6 packets** appear in the capture, all using the **MQTT protocol**.

Clicking on an MQTT publish packet in Wireshark reveals:

```
MQTT Publish Message:
  Topic: high_temperature
  QoS Level: At Least Once (QoS 1)
  Message: "High Temp"
  Flags: Publish, Complete, Release, Acknowledge
```

### What Wireshark reveals:

| Field               | Value                              | Meaning                                                                   |
| ------------------- | ---------------------------------- | ------------------------------------------------------------------------- |
| **Protocol**        | MQTT                               | The IoT-specific messaging protocol in use                                |
| **Topic**           | `high_temperature`                 | The channel/subject the message was published to                          |
| **QoS Level**       | At Least Once (1)                  | Delivery guarantee level (explained in Section 13)                        |
| **Message Content** | "High Temp"                        | The actual command/data payload                                           |
| **TCP Flags**       | SYN, ACK, FIN (standard TCP flags) | MQTT runs over TCP — all the flag behavior from Part 6 still applies here |

**Key insight:** The TCP flags from Part 6 (SYN, ACK, FIN, etc.) are still present in the lower layers of the Wireshark capture — because MQTT runs on top of TCP. The IoT protocol (MQTT) is just an additional layer on top of the networking protocols you've already studied.

---

## 13. MQTT Protocol — What It Is and Why IoT Uses It

**Full form:** Message Queuing Telemetry Transport

MQTT is the dominant **lightweight messaging protocol** used in IoT systems. While normal devices use TCP/UDP for communication, IoT devices use MQTT as their application-layer protocol (running on top of TCP).

### Why MQTT instead of HTTP?

| Aspect                                | HTTP (used by browsers)           | MQTT (used by IoT)                   |
| ------------------------------------- | --------------------------------- | ------------------------------------ |
| **Design for**                        | Request-response (human browsing) | Machine-to-machine messaging         |
| **Overhead**                          | Heavy headers, stateless          | Extremely lightweight headers        |
| **Connection style**                  | One-shot requests                 | Persistent connections               |
| **Power efficiency**                  | Not optimized for low power       | Designed for battery-powered sensors |
| **Works on slow/unreliable networks** | Struggles                         | Designed to handle it                |

MQTT uses a **publish-subscribe model**:

- IoT devices **publish** data to a specific **topic** (e.g., `temperature/sensor1`)
- Other devices or servers that have **subscribed** to that topic receive the data automatically
- An **MQTT broker** (the server) routes messages between publishers and subscribers

### QoS (Quality of Service) Levels in MQTT

| QoS Level | Name          | Guarantee                                             |
| --------- | ------------- | ----------------------------------------------------- |
| **0**     | At Most Once  | Message may be lost — fire and forget, like UDP       |
| **1**     | At Least Once | Message will arrive at least once (may be duplicated) |
| **2**     | Exactly Once  | Message arrives exactly once — highest overhead       |

In the practical, **QoS 1 (At Least Once)** was used — meaning the broker ensures the message is delivered to the subscriber at least once, and sends an acknowledgment (PUBACK) back to the publisher.

### Standard MQTT Ports

| Port     | Usage                                              |
| -------- | -------------------------------------------------- |
| **1883** | Standard, unencrypted MQTT (used in the practical) |
| **8883** | MQTT over TLS/SSL (encrypted)                      |

> **Security relevance:** Capturing unencrypted MQTT traffic (port 1883) in Wireshark allows a network attacker to read all commands and sensor data in plaintext — exactly what the practical demonstrated. This is why production IoT systems should use port 8883 with TLS encryption.

---

## 14. CAN Bus — The Next Layer of IoT Attack Surface

The lecture briefly introduces **CAN Bus (Controller Area Network)** as the next topic for advanced classes:

- **Full form:** Controller Area Network
- **What it is:** A robust communication bus standard originally designed for vehicle systems, now used broadly in industrial and automotive IoT — allowing microcontrollers and devices to communicate without a host computer.
- **Where used:** Car ECUs (Engine Control Units), industrial robots, medical devices.
- **Why it matters for security:** CAN bus traffic can be spoofed or injected — a classic attack vector in automotive hacking (e.g., disabling a car's brakes or steering by injecting malicious CAN bus messages).
- **Tools used:** ICSim (Industrial Control Simulator), CANbus simulator — the lecture mentions these are demonstrated in advanced sessions.

---

## 15. Why IoT Security Is Especially Difficult

Beyond the layer-specific vulnerabilities in Section 6, several systemic factors make IoT security particularly challenging:

| Challenge                     | Explanation                                                                                                          |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Scale**                     | Billions of IoT devices are deployed — patching all of them is logistically impossible                               |
| **Long lifecycles**           | OT devices often run for 20–30 years; security was not designed in from the start                                    |
| **No screens/keyboards**      | Many IoT devices have no user interface for direct management — can't easily apply updates or check logs             |
| **Heterogeneous protocols**   | IoT uses dozens of different protocols (MQTT, CoAP, Zigbee, Z-Wave, BLE, etc.) — no single unified security standard |
| **Default credentials**       | Millions of IoT devices ship with unchanged default username/password combinations that are publicly known           |
| **Physical access**           | IoT devices are often in exposed physical locations — an attacker can directly access hardware                       |
| **Always on**                 | IoT devices run continuously, giving attackers more time to probe and exploit                                        |
| **No security-first culture** | IoT manufacturers often prioritize features and cost over security — security is an afterthought                     |

> **Shodan** (mentioned in the lecture) is a search engine that indexes internet-facing devices, including IoT devices. It is commonly used by security researchers and attackers alike to find vulnerable IoT devices exposed to the internet. Advanced sessions in this course will demonstrate Shodan-based reconnaissance.

---

## 16. Why This Matters for Cybersecurity

| Security Topic               | Connection to This Lecture                                                                                                                                                                               |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **IoT Hacking (CEH Module)** | This lecture covers the theory foundation for the CEH IoT hacking module — understanding architecture, layers, and vulnerabilities before attempting exploits                                            |
| **MQTT Protocol Analysis**   | Capturing MQTT traffic in Wireshark (as demonstrated) is a key reconnaissance step in IoT assessments                                                                                                    |
| **Man-in-the-Middle on IoT** | Unencrypted MQTT (port 1883) traffic can be intercepted and read or modified in transit — a real-world MITM scenario                                                                                     |
| **ICS/SCADA Security**       | SCADA attacks are among the most critical in all of cybersecurity — they target physical infrastructure. Famous real-world attacks: Stuxnet (Iranian nuclear facility), Ukraine Power Grid attack (2015) |
| **Shodan Reconnaissance**    | The lecture mentions using Shodan to find internet-exposed IoT devices — a standard first step in IoT penetration testing                                                                                |
| **CAN Bus Attacks**          | Automotive hacking via CAN bus injection is a growing field — directly relevant to vehicle cybersecurity testing                                                                                         |
| **Firmware Analysis**        | IoT devices run embedded firmware — reverse engineering and vulnerability research on firmware is a specialized skill covered in advanced IoT security modules                                           |

---

## 17. Quick Revision Table

| Concept              | One-line Summary                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------------- |
| **IoT**              | Internet of Things — consumer/small-scale devices with sensors that collect and transmit data to cloud servers |
| **OT**               | Operational Technology — industrial-scale systems (SCADA, PLCs, etc.) that control physical infrastructure     |
| **IIoT**             | Industrial IoT — the convergence of OT hardware with IT infrastructure (cloud, internet, analytics)            |
| **IoT Architecture** | 5 layers: Application → Middleware → Internet → Access Gateway → Edge Technology                               |
| **Edge Layer**       | Where physical sensors live — actual data collection from the real world                                       |
| **Gateway**          | The bridge between local IoT devices and the public internet/cloud                                             |
| **ICS**              | Industrial Control System — automates and monitors industrial processes                                        |
| **SCADA**            | Supervisory Control and Data Acquisition — wide-area industrial monitoring (power grids, water systems)        |
| **DCS**              | Distributed Control System — distributed controllers for complex local processes (refineries, chemical plants) |
| **RTU**              | Remote Terminal Unit — collects sensor data at remote locations and sends it to SCADA                          |
| **PLC**              | Programmable Logic Controller — executes IF-THEN logic for machine automation                                  |
| **MQTT**             | Message Queuing Telemetry Transport — lightweight publish-subscribe protocol used by IoT devices               |
| **Port 1883**        | Standard (unencrypted) MQTT port                                                                               |
| **Port 8883**        | Encrypted MQTT port (TLS/SSL)                                                                                  |
| **QoS in MQTT**      | Quality of Service levels: 0 = at most once, 1 = at least once, 2 = exactly once                               |
| **CAN Bus**          | Controller Area Network — used in vehicles and industrial devices for microcontroller communication            |
| **Shodan**           | Search engine for internet-exposed devices including IoT; used for IoT reconnaissance                          |

---

## 18. Self-Test Questions

1. What does IoT stand for, and what are its three core components?
2. Trace the complete data journey from a smartwatch's heart rate sensor all the way to the user seeing their BPM on their phone app — identifying every step and every component involved.
3. What are the five layers of an IoT architecture? Describe what each layer does.
4. Name two security vulnerabilities at each of the following layers: Application, Network, Mobile, Cloud.
5. What is the difference between IoT and OT? Give two real-world examples of OT devices that most people wouldn't immediately recognize as "internet-connected."
6. What does SCADA stand for, and in which specific industries is it most commonly deployed?
7. Explain the difference between SCADA and DCS — what different problems do they solve?
8. What is a PLC, and how does it use logic to control industrial machines?
9. What does IIoT mean, and what does combining OT with IT actually enable?
10. In the practical: (a) what port did the MQTT broker listen on, and (b) what information was visible in the Wireshark capture of the MQTT traffic?
11. Why is MQTT preferred over HTTP for IoT communication?
12. What are the three MQTT QoS levels, and what delivery guarantee does each provide?
13. Name three systemic reasons why IoT security is inherently more difficult than traditional IT security.

---

## What's Next

This lecture completed the theory and basic practical introduction to IoT/OT hacking. Advanced sessions in this module will cover:

- **Shodan reconnaissance** — finding internet-exposed IoT devices and gathering vulnerability intelligence
- **MQTT exploitation** — unauthorized topic subscription, message injection, broker enumeration
- **CAN Bus attacks** — using ICSim and CANbus simulators to inject malicious commands into automotive control systems
- **IoT firmware analysis** — extracting and reverse-engineering IoT device firmware to find hardcoded credentials and vulnerabilities
- **SCADA attack simulation** — targeting simulated industrial control systems in a controlled lab environment

_IoT and OT security is one of the fastest-growing areas in cybersecurity — attacks on critical infrastructure (power grids, hospitals, water systems) are increasing in frequency and sophistication. A solid understanding of how these systems work, as built in this lecture, is the essential foundation for both attacking and defending them._
