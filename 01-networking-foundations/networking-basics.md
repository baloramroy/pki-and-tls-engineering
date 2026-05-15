# SSL/TLS Learning Journey — Phase 1: Networking Foundations

## Purpose

This Standard Operating Procedure (SOP) defines the foundational **networking concepts** required before learning **SSL/TLS, certificates, PKI, HTTPS, and secure infrastructure** communication.

SSL/TLS does not operate independently. **TLS exists** on top of **existing network communication** layers and depends heavily on:

- DNS
- TCP/IP
- Port communication
- Client-server architecture
- HTTP communication flow

Without understanding these networking fundamentals, concepts such as:

- TLS handshake
- Certificate validation
- Reverse proxy TLS
- Mutual TLS (mTLS)
- Kubernetes ingress TLS
- Elasticsearch HTTPS
- Kafka SSL
- API security

become difficult to understand operationally.

This phase builds the protocol-level mental model required for all future SSL/TLS engineering tasks.

---

## Scope

This SOP applies to engineers responsible for:

- Linux system administration
- Infrastructure engineering
- DevOps operations
- Platform engineering
- Security engineering

This document specifically focuses on:

- foundational networking behavior
- browser communication flow
- transport communication
- HTTP/HTTPS workflow
- DNS communication
- TCP communication lifecycle

---

## Learning Objectives

After completing this phase, the engineer should be able to:

- Explain how a browser reaches a website
- Explain DNS resolution workflow
- Explain TCP communication lifecycle
- Explain the purpose of ports
- Understand socket communication
- Differentiate HTTP and HTTPS
- Explain where TLS operates in the protocol stack
- Understand the dependency relationship between DNS, TCP, TLS, and HTTP
- Perform foundational network troubleshooting commands
- Analyze basic HTTPS communication flow

---

## Definitions and Abbreviations

| Term   | Definition                                                        |
| ------ | ----------------------------------------------------------------- |
| DNS    | Domain Name System; translates domain names into IP addresses     |
| HTTP   | HyperText Transfer Protocol; plaintext web communication protocol |
| HTTPS  | HTTP over TLS-encrypted communication                             |
| IP     | Internet Protocol; addressing and routing protocol                |
| PKI    | Public Key Infrastructure                                         |
| Socket | Combination of IP address and port                                |
| TCP    | Transmission Control Protocol                                     |
| TLS    | Transport Layer Security                                          |
| URL    | Uniform Resource Locator                                          |

---

## Communication Stack Overview

Before learning individual topics, engineers must understand where TLS exists in communication architecture.

### Simplified Communication Stack

```text
Application Layer    → HTTP / HTTPS
Security Layer       → TLS/SSL
Transport Layer      → TCP
Network Layer        → IP
Data Link Layer      → Ethernet
```

### Critical Understanding

TLS does NOT replace networking.

TLS secures communication already **established over TCP/IP**.

Correct dependency chain:

```text
DNS
↓
TCP
↓
TLS
↓
HTTP
```

This sequence is foundational for all SSL/TLS learning.

---

## Topic 1 — Client-Server Architecture

### Concept Overview

Modern internet communication follows the **client-server architecture** model.

This architecture separates systems into:

- service consumers (clients)
- service providers (servers)

Nearly all modern infrastructure uses this model, including:

- websites
- APIs
- databases
- monitoring systems
- Kubernetes applications
- banking platforms

### Client Definition

A client is a system, application, or process that initiates communication and requests resources or services.

### Common Client Examples

| Client Type      | Example             |
| ---------------- | ------------------- |
| Browser          | Chrome, Firefox     |
| CLI Tool         | curl                |
| API Tool         | Postman             |
| Monitoring Agent | Zabbix Agent        |
| Mobile App       | Banking Application |

### Server Definition

A server is a system or process that listens for incoming requests and provides services, resources, or responses.

### Common Server Examples

| Server Type       | Purpose                     |
| ----------------- | --------------------------- |
| Web Server        | Serves web content          |
| Database Server   | Stores and retrieves data   |
| API Server        | Processes API requests      |
| Mail Server       | Handles email communication |
| Monitoring Server | Receives monitoring data    |

### Communication Workflow

**Logical Flow**

```text
Client ---> Request ---> Server
Client <--- Response --- Server
```

**Real Web Example**

```text
Browser ---> HTTP GET ---> NGINX
Browser <--- HTML ------- NGINX
```

### Operational Relevance to SSL/TLS

TLS exists to secure communication between:

```text
Client <---- Encrypted Communication ----> Server
```

Without understanding client-server roles, TLS communication becomes difficult to visualize operationally.


---

## Topic 2 — IP Address Fundamentals

### Concept Overview

Every communicating device on a network requires a unique address.

IP addressing provides:

- host identification
- packet routing
- destination location

Without IP addressing, network communication cannot occur.

### IP Definition

IP stands for:

```text
Internet Protocol
```

An IP address uniquely identifies a device on a network.

### IPv4 Structure

IPv4 uses:

```text
32-bit addressing
```

Example:

```text
192.168.1.10
```

Each octet ranges from:

```text
0–255
```

### Types of IP Addresses

**Private IP**

Used internally within private networks.

**Common Ranges**

| Range          | Usage                     |
| -------------- | ------------------------- |
| 10.0.0.0/8     | Enterprise infrastructure |
| 172.16.0.0/12  | Internal systems          |
| 192.168.0.0/16 | Home/office networks      |

Private IPs are NOT internet-routable.

**Public IP**

Public IPs are globally routable internet addresses assigned by:

- ISPs
- cloud providers
- hosting providers

### Operational Relevance to TLS

TLS communication ultimately occurs between:

```text
Client IP <--> Server IP
```

Even when users use domain names, encrypted traffic still travels between IP endpoints.

### Important Networking Concepts

| Concept        | Description                |
| -------------- | -------------------------- |
| Source IP      | Packet sender              |
| Destination IP | Packet receiver            |
| Gateway        | Route to external networks |
| Routing        | Packet forwarding process  |


### Practical Commands

**View IP Information**

```bash
ip addr
```

**View Routing Table**

```bash
ip route
```

**Test Connectivity**

```bash
ping google.com
```

---

## Topic 3 — Port Fundamentals

### Concept Overview

- A single server can run multiple services simultaneously.
- Ports exist to distinguish services running on the same IP address.

### Port Definition

A port identifies a specific application or service endpoint on a host.

### Socket Concept

A socket represents:

```text
IP Address + Port
```

Example:

```text
192.168.1.10:443
```

### Important SSL/TLS Related Ports

| Port | Service       | TLS Usage                  |
| ---- | ------------- | -------------------------- |
| 80   | HTTP          | No TLS                     |
| 443  | HTTPS         | Standard TLS               |
| 22   | SSH           | Protocol-native encryption |
| 636  | LDAPS         | TLS-enabled LDAP           |
| 9093 | Kafka SSL     | TLS-enabled Kafka          |
| 9200 | Elasticsearch | Often TLS-enabled          |

### HTTPS Default Port Behavior

When a browser sees:

```text
https://example.com
```

it automatically assumes:

```text
example.com:443
```

>[!NOTE]
unless another port is explicitly specified.

### Operational Relevance to TLS

TLS listeners bind to specific ports.

Example:

```text
NGINX HTTPS listener → 443
Kubernetes API → 6443
Kafka SSL listener → 9093
```

### Practical Commands

**View Listening Services**

```bash
ss -tulnp
```

**Test Port Connectivity**

```bash
nc -zv google.com 443
```

**View Active TCP Sessions**

```bash
ss -ant
```

---

## Topic 4 — DNS Fundamentals

### Concept Overview

- Humans use domain names.
- Networks use IP addresses.
- DNS bridges this gap.

### DNS Definition

DNS stands for:

```text
Domain Name System
```

>[!important]DNS translates domain names into IP addresses.

### DNS Resolution Workflow

**Resolution Sequence**

```text
Browser Cache
↓
OS Cache
↓
/etc/hosts
↓
Recursive Resolver
↓
Root Server
↓
TLD Server
↓
Authoritative Server
↓
IP Returned
```

### Important DNS Record Types

| Record | Purpose                    |
| ------ | -------------------------- |
| A      | Domain → IPv4              |
| AAAA   | Domain → IPv6              |
| CNAME  | Alias mapping              |
| MX     | Mail routing               |
| TXT    | Verification/security data |

### Operational Relevance to TLS

TLS certificates are typically issued for domain names.

Example:

```text
CN=api.example.com
```

NOT:

```text
CN=192.168.1.10
```

Certificate validation depends heavily on correct DNS resolution.

### Critical Security Relevance

- DNS compromise can redirect clients to malicious systems.
- TLS certificate validation acts as a defense mechanism against DNS hijacking.

### Failure Scenarios

| Failure               | Result                       |
| --------------------- | ---------------------------- |
| Wrong A record        | Traffic reaches wrong server |
| NXDOMAIN              | Domain unreachable           |
| Expired DNS TTL cache | Stale resolution             |
| DNS poisoning         | Potential MITM attack        |

### Practical Commands

**Query DNS Records**

```bash
dig google.com
```

**Full Resolution Trace**

```bash
dig +trace google.com
```

**Alternative Query Tool**

```bash
nslookup google.com
```

---

## Topic 5 — TCP Fundamentals

### Concept Overview

- TCP is the foundation of reliable internet communication.
- HTTPS communication depends entirely on TCP.

### TCP Definition

TCP stands for:

```text
Transmission Control Protocol
```

>[!important]TCP is a connection-oriented, reliable transport-layer communication protocol.

### Core TCP Guarantees

| Guarantee              | Description                    |
| ---------------------- | ------------------------------ |
| Reliable Delivery      | Lost packets retransmitted     |
| Ordered Delivery       | Packets processed sequentially |
| Error Detection        | Corrupted packets detected     |
| Stateful Communication | Connection state maintained    |

### Why TCP Matters for TLS

TLS requires:

- reliable packet delivery
- ordered packet processing
- session continuity

>[!note]
Without TCP reliability, TLS record processing would fail.

### TCP vs UDP

| TCP                 | UDP                 |
| ------------------- | ------------------- |
| Reliable            | Faster              |
| Connection-oriented | Connectionless      |
| Ordered             | Unordered           |
| Used by HTTPS       | Used heavily by DNS |

### Operational Relevance

Protocols using TLS commonly operate over TCP:

- HTTPS
- SMTPS
- IMAPS
- Kafka SSL
- LDAPS
- Elasticsearch HTTPS


### Practical Commands

**View TCP Connections**

```bash
ss -ant
```

**Capture TCP Packets**

```bash
sudo tcpdump -i any tcp
```

---

## Topic 6 — TCP 3-Way Handshake

### Concept Overview

Before data transfer begins, TCP establishes a reliable session between client and server.

This process is called:

```text
TCP 3-Way Handshake
```

### Handshake Workflow

- **Step 1 — SYN**

  Client initiates connection request.

- **Step 2 — SYN-ACK**

  Server acknowledges and accepts request.

- **Step 3 — ACK**

  Client confirms acknowledgment.

### Visual Flow

```text
Client                Server
  | ---- SYN -------> |
  | <--- SYN-ACK ---- |
  | ---- ACK -------> |
```

### Critical TLS Understanding

TLS handshake begins ONLY AFTER successful TCP connection establishment.

Correct sequence:

```text
DNS
↓
TCP Handshake
↓
TLS Handshake
↓
Encrypted HTTP Communication
```

### Practical Labs

**Capture HTTPS Handshake Traffic**

```bash
sudo tcpdump -i any port 443
```

Observe:

- SYN
- SYN-ACK
- ACK

---

## Topic 7 — HTTP Fundamentals

### Concept Overview

```text
HTTP is the core application-layer protocol used for web communication.
```

### HTTP Definition

HTTP stands for:

```text
HyperText Transfer Protocol
```

HTTP is a **stateless** request-response protocol.

### Default HTTP Port

```text
80
```

### HTTP Request Structure

```http
GET /api/users HTTP/1.1
Host: example.com
```

### Important HTTP Characteristics

| Characteristic    | Description             |
| ----------------- | ----------------------- |
| Stateless         | Requests independent    |
| Plaintext         | Data readable           |
| Application-layer | Top communication layer |

### Why HTTP Is Insecure

HTTP traffic is transmitted without encryption.

Attackers may:

- inspect credentials
- steal session tokens
- modify requests
- intercept data

This led to HTTPS adoption.


### Practical Commands

**Inspect HTTP Communication**

```bash
curl -v http://example.com
```

**Capture HTTP Packets**

```bash
sudo tcpdump -i any port 80
```

---

## Topic 8 — HTTPS Fundamentals

### Concept Overview

```text
HTTPS secures HTTP communication using TLS encryption.
```

### HTTPS Definition

HTTPS means:

```text
HTTP over TLS
```

### Default HTTPS Port

```text
443
```

### Core HTTPS Security Properties

| Property        | Purpose                |
| --------------- | ---------------------- |
| Confidentiality | Prevent data exposure  |
| Integrity       | Prevent modification   |
| Authentication  | Verify server identity |

### HTTPS Communication Stack

```text
HTTP
TLS
TCP
IP
```

### Internal HTTPS Workflow

```text
TCP Handshake
↓
TLS Handshake
↓
Certificate Validation
↓
Encrypted HTTP Communication
```

### Important TLS Components

| Component     | Purpose               |
| ------------- | --------------------- |
| Certificate   | Server identity       |
| Public Key    | Encryption operations |
| Private Key   | Decryption/signing    |
| Cipher Suite  | Encryption algorithms |
| TLS Handshake | Session establishment |

### Practical Commands

**Inspect HTTPS Communication**

```bash
curl -v https://example.com
```

**TLS Inspection Tool**

```bash
openssl s_client -connect google.com:443
```

Critical SSL troubleshooting command.

---

## Topic 9 — Complete Browser Request Lifecycle

### Scenario

User accesses:

```text
https://api.example.com/data
```

### Complete Communication Flow

- **Step 1 — URL Parsing**

    Browser identifies:
    - HTTPS protocol
    - destination host
    - port 443

- **Step 2 — DNS Resolution**

    Domain resolved to IP address.

- **Step 3 — TCP Connection**

    TCP 3-way handshake establishes reliable session.

- **Step 4 — TLS Handshake**

    Browser and server:
    - negotiate encryption
    - exchange certificates
    - establish secure session

- **Step 5 — Encrypted HTTP Request**

    HTTP request encrypted inside **TLS tunnel**.

- **Step 6 — Encrypted HTTP Response**

    Server sends **encrypted response**.

- **Step 7 — Browser Rendering**

    Browser **decrypts and renders** content.

### Complete Layer Flow

```text
User Types URL
        ↓
DNS Resolution
        ↓
TCP Handshake
        ↓
TLS Handshake
        ↓
Certificate Validation
        ↓
Encrypted HTTP Request
        ↓
Encrypted HTTP Response
        ↓
Browser Render
```

---

## Practical Labs

All labs must be completed before progressing to Phase 2.

### DNS Analysis

```bash
dig +trace google.com
```

### Port Inspection

```bash
ss -tulnp
```

### Connectivity Testing

```bash
nc -zv google.com 443
```

### HTTP vs HTTPS Comparison

```bash
curl -v http://example.com
curl -v https://example.com
```

### TCP Packet Capture

```bash
tcpdump -i any port 443
```

### TLS Inspection

```bash
openssl s_client -connect google.com:443
```

---

## Next Phase

### Phase 2 — Security Basics

**Learn:**

- Encryption
- Decryption
- Plain text
- Cipher text
- Authentication
- Integrity
- Confidentiality

**Must Understand**

Difference between:

- Symmetric encryption
- Asymmetric encryption

> This is the MOST IMPORTANT SSL foundation.
