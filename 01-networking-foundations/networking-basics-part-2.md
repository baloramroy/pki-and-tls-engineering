# SSL/TLS Learning Journey: Networking Foundations - Part 2

## Learning Objectives

After completing this phase, the engineer should be able to:

- Explain DNS resolution workflow
- Explain TCP communication lifecycle
- Differentiate HTTP and HTTPS
- Explain where TLS operates in the protocol stack
- Perform foundational network troubleshooting commands
- Analyze basic HTTPS communication flow


## Topic 5 — TCP Fundamentals

### Concept Overview

- TCP is the foundation of reliable internet communication.
- HTTPS communication depends entirely on TCP.

### TCP Definition

**TCP stands for:**

```text
Transmission Control Protocol
```

>[!important]TCP is a connection-oriented, reliable transport-layer communication protocol.

#

### Core TCP Guarantees

| Guarantee              | Description                    |
| ---------------------- | ------------------------------ |
| Reliable Delivery      | Lost packets retransmitted     |
| Ordered Delivery       | Packets processed sequentially |
| Error Detection        | Corrupted packets detected     |
| Stateful Communication | Connection state maintained    |

#

### Why TCP Matters for TLS

TLS requires:

- reliable packet delivery
- ordered packet processing
- session continuity

>[!note]
Without TCP reliability, TLS record processing would fail.

#

### TCP vs UDP

| TCP                 | UDP                 |
| ------------------- | ------------------- |
| Reliable            | Faster              |
| Connection-oriented | Connectionless      |
| Ordered             | Unordered           |
| Used by HTTPS       | Used heavily by DNS |

#

### Operational Relevance

Protocols using TLS commonly operate over TCP:

- HTTPS
- SMTPS
- IMAPS
- Kafka SSL
- LDAPS
- Elasticsearch HTTPS

#

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

**This process is called:**

```text
TCP 3-Way Handshake
```

#

### Handshake Workflow

- **Step 1 — SYN**

  Client initiates connection request.

- **Step 2 — SYN-ACK**

  Server acknowledges and accepts request.

- **Step 3 — ACK**

  Client confirms acknowledgment.

#

### Visual Flow

```text
Client                Server
  | ---- SYN -------> |
  | <--- SYN-ACK ---- |
  | ---- ACK -------> |
```

#

### Critical TLS Understanding

TLS handshake begins ONLY AFTER successful TCP connection establishment.

**Correct sequence:**

```text
DNS
↓
TCP Handshake
↓
TLS Handshake
↓
Encrypted HTTP Communication
```

#

### Practical Labs

**Capture HTTPS Handshake Traffic**

```bash
sudo tcpdump -i any port 443
```

**Observe:**

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

**HTTP stands for:**

```text
HyperText Transfer Protocol
```

HTTP is a **stateless** request-response protocol.

**Default HTTP Port**

```text
80
```

**HTTP Request Structure**

```http
GET /api/users HTTP/1.1
Host: example.com
```

#

### Important HTTP Characteristics

| Characteristic    | Description             |
| ----------------- | ----------------------- |
| Stateless         | Requests independent    |
| Plaintext         | Data readable           |
| Application-layer | Top communication layer |

#

### Why HTTP Is Insecure

HTTP traffic is transmitted without encryption.

**Attackers may:**

- inspect credentials
- steal session tokens
- modify requests
- intercept data

This led to HTTPS adoption.

#

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

**HTTPS means:**

```text
HTTP over TLS
```

**Default HTTPS Port**

```text
443
```

#

### Core HTTPS Security Properties

| Property        | Purpose                |
| --------------- | ---------------------- |
| Confidentiality | Prevent data exposure  |
| Integrity       | Prevent modification   |
| Authentication  | Verify server identity |

#

### HTTPS Communication Stack

```text
HTTP
TLS
TCP
IP
```

#

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

#

### Important TLS Components

| Component     | Purpose               |
| ------------- | --------------------- |
| Certificate   | Server identity       |
| Public Key    | Encryption operations |
| Private Key   | Decryption/signing    |
| Cipher Suite  | Encryption algorithms |
| TLS Handshake | Session establishment |


#

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

**User accesses:**

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