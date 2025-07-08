---
title: What is Network Time Protocol?
date: 2022-11-20 10:30:30 +/-TTTT
categories: [Security, networking, x509, certificates, ssl, mtls, .net, azure]
tags: [networking, clock, pki, x509, architecture, certificates, ssl, security, attack, encryption]     # TAG names should always be lowercase
description: This post describes the importance of NTP and other such protocol and how they are used in today's modern system to keep system in sync.
mermaid: true
math: true
---

## Introduction

In an internet enabled enterprise and businesses, it's important that systems remains synchronized. Accurate timekeeping is more than just a convenience—it's a critical infrastructure requirement. Whether you're running a global data center, managing financial transactions, or simply syncing files across devices, time synchronization ensures everything happens in the right order, at the right time.

### What is Time Synchronization?

**Time synchronization is the process of coordinating the time settings of computers and devices within a network to ensure they all share the same time reference.** This is crucial for various applications, including logging events, scheduling tasks, and maintaining the integrity of data transactions. Without proper time synchronization, discrepancies can arise, leading to potential security vulnerabilities and operational issues.

### Advantages of Time Synchronization

**Data Consistency**: Ensures logs, transactions, and events are timestamped accurately. Prevents issues like out-of-order events in distributed systems.
**Security**: Time-based authentication protocols (e.g., Kerberos, TLS) rely on synchronized clocks. Helps detect and prevent replay attacks and log tampering.
**Network Coordination**: Enables coordinated actions across systems (e.g., in telecom, power grids, and industrial automation). Supports time-sensitive applications like VoIP, video conferencing, and real-time control.
**Troubleshooting and Auditing**: Accurate timestamps help trace issues, correlate logs, and audit system behavior.
**Legal and Compliance Requirements**: Many industries (e.g., finance, healthcare) require precise timekeeping for regulatory compliance.

### Implementation of Time Synchronization

Implementing time synchronization means ensuring all systems and devices follow the same clock using **standardized protocols**. This is essential for consistency, security, and coordination across networks. Fortunately, time synchronization can be achieved using various standardized protocols. These protocols vary in complexity and accuracy and cater to different industries and problem domains.

The most common protocol being Network Time Protocol (NTP). NTP is designed to synchronize the clocks of computers over a network, ensuring that all devices have a consistent time reference. Below are some of the protocols used in time synchronization:

| Protocol         | Description                                                                 | Target Use Cases / Industries                          |
|------------------|-----------------------------------------------------------------------------|--------------------------------------------------------|
| **NTP**          | Network Time Protocol; synchronizes clocks over IP networks with millisecond accuracy. | General computing, servers, desktops, IT infrastructure |
| **SNTP**         | Simple NTP; a lightweight version of NTP with reduced complexity and accuracy. | Embedded systems, IoT devices, simple networked devices |
| **PTP**          | Precision Time Protocol (IEEE 1588); provides sub-microsecond synchronization. | Telecom, industrial automation, power systems, finance |
| **IRIG**         | Inter-Range Instrumentation Group time codes; analog/digital time signals.   | Aerospace, military, telemetry, scientific research     |
| **GPS Time**     | Time synchronization using signals from GPS satellites.                      | Telecom, data centers, scientific labs, mobile networks |
| **DCF77 / WWVB / MSF** | Radio time signals broadcast by national time services.                 | Clocks, consumer electronics, legacy systems            |
| **IEEE 802.1AS** | Profile of PTP for Time-Sensitive Networking (TSN) and Audio/Video Bridging. | Automotive, industrial control, professional AV systems |
| **White Rabbit** | Extension of PTP for sub-nanosecond accuracy using fiber optics.             | Particle physics, scientific research (e.g., CERN)      |
| **Chrony**       | Modern NTP implementation optimized for variable network conditions.         | Linux systems, virtual machines, mobile devices         |

In this article we will focus on the NTP Protocol, we will see the details about NTP.

## What is NTP Protocol?

**NTP (Network Time Protocol)** is a standardized protocol used to synchronize the clocks of computers and devices over a network. It ensures that all systems agree on the correct time. It is a -

- **Standard**: Defined in RFC 5905
- **Protocol Layer**: Operates over **UDP port 123**
- **Accuracy**: Typically within **1–50 milliseconds** over the internet; better on local networks

### How NTP Works (Conceptually)

NTP uses a hierarchical system of time sources and a process of timestamp exchange to calculate and correct time differences. It uses **timestamping** to measure the round-trip delay and offset between the client and server clocks. It arranges the servers in a hierarchy called stratum, with Stratum 0 at the top and lower strata syncing from higher ones.
The time flows from the higher stratum servers to the lower ones, ensuring that all devices have a consistent time reference.

#### NTP Hierarchy (Stratum Levels)

| Stratum Level | Description                                      | Example Devices / Use Cases                     |
|---------------|--------------------------------------------------|-------------------------------------------------|
| **0**         | High-precision reference clocks (not networked)  | Atomic clocks, GPS receivers, radio clocks      |
| **1**         | Primary time servers directly connected to Stratum 0 | National time servers, GPS-disciplined servers |
| **2**         | Secondary servers syncing from Stratum 1         | Enterprise NTP servers, internal network servers|
| **3–15**      | Lower-tier servers or clients                    | PCs, routers, IoT devices, general clients      |
| **16**        | Unsynchronized (error state)                     | Indicates the device is not currently synced    |

#### Time Synchronization Process

When a client communicates with an NTP server, it uses **four timestamps** to calculate the time offset and network delay:

| Timestamp | Description |
|-----------|-------------|
| **T1** | Time request leaves the client (Originate Timestamp) |
| **T2** | Time request arrives at the server (Receive Timestamp) |
| **T3** | Time response leaves the server (Transmit Timestamp) |
| **T4** | Time response arrives back at the client (Destination Timestamp) |

### Key Formulas

- **Round-trip delay**: This denotes the network latency. This measures the total time taken for the request to go to the server and the response to return, minus the time the server spent processing the request.

$$ Round-trip delay = (T4 - T1) - (T3 - T2) $$

- **Local clock offset**: This denotes the difference between the client & server clocks. The client uses the offset to adjust its local clock and repeats this process periodically to stay in sync.

$$ Offset = ((T2 - T1) + (T3 - T4)) / 2 $$

The client calculates the round-trip delay and the local clock offset using these timestamps. The round-trip delay helps determine how long it takes for a request to travel to the server and back, while the offset indicates how much the client's clock deviates from the server's clock.
The client then uses these calculations to adjust its local clock and maintain synchronization with the NTP server.

### NTP Modes of Operation

The **Network Time Protocol (NTP)** supports multiple **operating modes** that define how a device behaves in a time synchronization network. These modes determine whether a device acts as a **client**, **server**, **peer**, or **broadcast source**.

---

| Mode Number | Mode Name         | Description |
|-------------|-------------------|-------------|
| **1**       | Symmetric Active  | A peer actively trying to synchronize with another peer. Used in **peer-to-peer** configurations. |
| **2**       | Symmetric Passive | Waits for a symmetric active peer to initiate communication. Also used in **peer-to-peer** setups. |
| **3**       | Client            | Sends requests to an NTP server and adjusts its clock based on the response. Most common mode for end-user systems. |
| **4**       | Server            | Responds to client requests. Typically used by public or enterprise NTP servers. |
| **5**       | Broadcast         | Sends time updates to multiple clients without waiting for requests. Used in local networks where many clients need time sync. |
| **6**       | NTP Control       | Used for remote management and monitoring of NTP servers. Not used for time synchronization itself. |
| **7**       | Private           | Reserved for internal use (e.g., debugging or proprietary extensions). Not used in standard NTP operations. |

---

![NTP Modes Diagram](assets/images/posts/2022-12-15/image.png)

### NTP Packet Structure

The **NTP packet** is the core data unit used in communication between NTP clients and servers. It contains all the information needed to calculate time offset and delay, and to maintain synchronization.

---

#### NTP Packet Format (48 bytes total)

An NTP packet is typically **48 bytes** long and consists of the following fields:

| Field | Size (bits) | Description |
|-------|-------------|-------------|
| **LI (Leap Indicator)** | 2 | Warns of an upcoming leap second adjustment. |
| **VN (Version Number)** | 3 | NTP version (e.g., 4 for NTPv4). |
| **Mode** | 3 | Indicates the mode (client, server, etc.). |
| **Stratum** | 8 | Indicates the stratum level (0–15). |
| **Poll Interval** | 8 | $ Log2 $ of the maximum interval between messages (in seconds). |
| **Precision** | 8 | $ Log2 $ of the system clock precision (negative value). |
| **Root Delay** | 32 | Total round-trip delay to the primary reference source (in seconds, fixed-point). |
| **Root Dispersion** | 32 | Maximum error relative to the primary reference source (in seconds, fixed-point). |
| **Reference ID** | 32 | Identifier of the reference clock (IP address or ASCII code). |
| **Reference Timestamp** | 64 | Time when the system clock was last set or corrected. |
| **Originate Timestamp (T1)** | 64 | Time when the request left the client. |
| **Receive Timestamp (T2)** | 64 | Time when the request was received by the server. |
| **Transmit Timestamp (T3)** | 64 | Time when the response left the server. |
| **Destination Timestamp (T4)** | Not in packet | Time when the response was received by the client (recorded locally). |

---

##### Key Fields in NTP packets

| Field | Values |
|-------|--------|
| **LI (Leap Indicator)** | `00`: No warning   `01`: Last minute has 61 seconds   `10`: Last minute has 59 seconds   `11`: Alarm condition (clock not synchronized) |
| **VN (Version Number)** | `3` (NTPv3), `4` (NTPv4) |
| **Mode** | `1`: Symmetric active   `2`: Symmetric passive   `3`: Client   `4`: Server   `5`: Broadcast   `6`: NTP control   `7`: Private |
| **Timestamps** | T1: Originate Timestamp   T2: Receive Timestamp   T3: Transmit Timestamp   T4: Destination Timestamp |

#### Platform Support

NTP is **widely supported** across all modern computing platforms:

| Platform | Support |
|----------|---------|
| **Linux** | Built-in support via `ntpd`, `chronyd`, or `systemd-timesyncd` |
| **Windows** | Built-in via Windows Time Service (`w32time`) |
| **macOS** | Uses `ntpd` or `timed` |
| **Embedded Systems** | Often use **SNTP** (Simple NTP) for lightweight sync |
| **Cloud Platforms** | AWS, Azure, GCP provide NTP services for VM instances |

#### Security Considerations

- **NTPv4** supports symmetric key authentication.
- **NTS (Network Time Security)** adds encryption and integrity protection.
- It's important to use **trusted NTP servers** to avoid spoofing or tampering.

## Compliance Standards That Require Time Synchronization

Time synchronization is a critical requirement in many industries to ensure accurate logging, secure operations, and regulatory compliance. Below is a list of key standards and regulations that mandate or recommend synchronized time across systems.

### List of Compliance Standards

| Standard / Regulation | Requirement Summary | Industry / Domain |
|------------------------|---------------------|-------------------|
| **ISO/IEC 27001:2022** – Control 8.17 | Requires synchronized clocks across systems to ensure accurate logging, forensic analysis, and incident response. Recommends using NTP or PTP with secure configuration. | Information Security, IT |
| **PCI DSS (Payment Card Industry Data Security Standard)** | Requires accurate timekeeping for all systems involved in payment processing to ensure reliable audit trails. | Finance, Retail, E-commerce |
| **MiFID II (Markets in Financial Instruments Directive)** | Mandates timestamping of trades with microsecond accuracy using synchronized clocks (often via PTP). | Financial Trading |
| **FINRA (Financial Industry Regulatory Authority)** | Requires accurate timestamping of financial transactions for audit and compliance. | U.S. Financial Markets |
| **GDPR (General Data Protection Regulation)** | While not explicitly about time sync, it requires accurate logging and traceability of data access and processing, which depends on synchronized clocks. | Data Privacy, EU |
| **SMPTE 2059** | Defines time synchronization for audio/video equipment using PTP to ensure frame-accurate broadcast timing. | Broadcasting, Media |
| **IEEE 1588 (PTP Standard)** | Used in smart grids and industrial automation to ensure precise event logging and control. | Energy, Utilities, Industrial Control |
| **NERC CIP (North American Electric Reliability Corporation – Critical Infrastructure Protection)** | Requires synchronized time sources for logging and monitoring in critical infrastructure systems. | Power & Energy |
| **SOX (Sarbanes-Oxley Act)** | Requires accurate and auditable logs for financial reporting, which depend on synchronized time. | Corporate Governance, Finance |

## Industry Use Cases

| Industry / Domain         | Use Case Description                                                                 | Protocols Used                                      |
|---------------------------|----------------------------------------------------------------------------------------|-----------------------------------------------------|
| **Financial Services**    | High-frequency trading, regulatory compliance (e.g., MiFID II), accurate audit trails | PTP, GPS                                            |
| **Healthcare**            | Synchronizing EHRs, medical imaging, telemedicine, legal traceability                 | NTP, SNTP                                           |
| **Energy & Smart Grids**  | Time-stamping grid events, real-time monitoring, fault isolation                      | PTP, IRIG-B, IEEE 1588 Power Profile                |
| **Telecommunications**    | Synchronizing base stations, handovers, QoS, network slicing                          | PTP (ITU-T G.8275), GPS, SyncE                      |
| **Industrial Automation** | Coordinating robotics, sensors, and controllers in real-time                          | IEEE 802.1AS, PTP, White Rabbit                     |
| **Broadcasting & Media**  | Frame-accurate editing, live broadcasting, AV bridging                                | SMPTE 2059, PTP, IEEE 1588 AVB Profile              |
| **Cloud & Data Centers**  | Coordinating distributed systems, databases, microservices, log consistency           | NTP, Chrony, NTS                                    |

## Azure Support for NTP

Azure provides built-in support for NTP through its various services. Azure VMs are automatically configured to use the Azure time service, which is based on NTP. This ensures that all VMs in Azure have a consistent time source, which is critical for distributed applications and services.

In addition to the built-in NTP support, Azure also allows you to configure your own NTP servers if needed. This can be useful for organizations that have specific compliance requirements or need to synchronize with on-premises time sources.

### Azure Time Synchronization Architecture

Time synchronization in Microsoft Azure is essential for ensuring consistency across virtual machines (VMs), services, and logs. Azure uses a combination of **internal time servers**, **host-based synchronization**, and **standard protocols like NTP and PTP** to maintain accurate time across its infrastructure.

#### Azure Host Time Source

- Azure hosts are synchronized to **Microsoft-owned Stratum 1 time servers**.
- These servers are backed by **GPS antennas** and **atomic clocks**.
- This ensures that all Azure infrastructure operates on a highly accurate and reliable time base.

#### Time Sync in Azure Virtual Machines

- **Windows VMs**

  - By default, Windows VMs in Azure use:
  - **Host time** (via Hyper-V integration)
  - **time.windows.com** as a fallback NTP source
  - Time sync is managed by the **VMICTimeSync** service and **W32Time** (Windows Time Service).
  - Modes:
  - **Sample Mode**: Polls host every 5 seconds and adjusts every 30 seconds.
  - **Sync Mode**: Activated after resume or large drift (>5 seconds).

- **Linux VMs**

  - Linux VMs can use:
  - **Chrony** (preferred for newer distros)
  - **ntpd** (older systems)
  - Time can be synced from:
  - **Azure host** via **PTP (Precision Time Protocol)** using `/dev/ptp_hyperv`
  - **External NTP servers** (e.g., `ntp.ubuntu.com`, `pool.ntp.org`)

#### Azure App Services

- Runs on **virtualized infrastructure** managed by Microsoft.
- Time is synchronized automatically via:
  - **Windows Time Service** (for Windows-based App Services).
  - **Chrony or ntpd** (for Linux-based App Services).
- Time zone is **UTC by default**.
- You can use `WEBSITE_TIME_ZONE` setting for Windows App Services to display time in a specific zone.
- Manual time sync configuration is **not required or supported**.

#### Azure Functions

- Time sync depends on the **underlying host OS**.
- Functions inherit time from the **Azure-managed infrastructure**, which is synced to Microsoft’s Stratum 1 time servers.
- No manual configuration needed; time is **automatically managed**.

#### Azure Kubernetes Service (AKS)

- AKS nodes (VMs) follow the same time sync principles as regular Azure VMs.
- You can monitor **time drift** using tools like **Prometheus** and **Grafana**.
- For regulated industries, custom monitoring solutions can be deployed to ensure compliance.

#### Azure SQL Database & Cosmos DB

- These are **fully managed PaaS services**.
- Time synchronization is handled internally by Azure.
- All timestamps (e.g., in logs, transactions) are based on **UTC**.
- No user-level access to time sync configuration.

##### Summary Table

| Azure Service         | Time Sync Method                          | User Configuration |
|-----------------------|-------------------------------------------|--------------------|
| **Virtual Machines**  | Host time, NTP, Chrony/ntpd               | Optional           |
| **App Services**      | Host time via W32Time or Chrony/ntpd      | Not required       |
| **Azure Functions**   | Host time (auto-managed)                  | Not required       |
| **AKS (Kubernetes)**  | Host time, monitor with Prometheus/Grafana| Optional           |
| **SQL & Cosmos DB**   | Internal UTC-based sync                   | Not accessible     |

## Conclusion

Time synchronization is extremely important service to keep all servers in sync. This is particularly necessary in scenarios where transaction tracking is mandatory. NTP is a widely used protocol that helps achieve this goal by providing accurate and reliable time information over the network.
