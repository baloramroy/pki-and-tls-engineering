# SSL/TLS Learning Journey: Phase 2 — Encryption Basic

## Learning Objectives

After completing this phase, you should be able to:

- Symmetric Encryption and Asymmetric Encryption
- differentiate symmetric vs asymmetric encryption
- understand where SSL/TLS uses each method
- understand why both methods are necessary

---


## Symmetric Encryption

### Definition

**Symmetric encryption uses:**

```text
ONE single shared key
```

**for both:**

- encryption
- decryption

### Visual Flow

```text
Plain Text
    ↓
Encrypt using Shared Key
    ↓
Cipher Text
    ↓
Decrypt using SAME Shared Key
    ↓
Original Plain Text
```

### Example

**Alice and Bob both know:**

```text
Secret Key = mysecret123
```

**Alice encrypts:**

```text
HELLO
```

>Bob decrypts using the SAME key.

#

### Important Characteristics

| Feature     | Description                  |
| ----------- | ---------------------------- |
| Speed       | Very Fast                    |
| Key Usage   | Same key for encrypt/decrypt |
| Performance | Efficient                    |
| Usage       | Bulk data encryption         |

#

### Main Advantages

- extremely fast
- low CPU usage
- efficient for large data transfer

### Main Disadvantages

**Main problem:**

```text
How do we securely share the secret key?
```

**If attacker steals the shared key:**

```text
Everything becomes compromised
```

**This is called:**

```text
Key Distribution Problem
```

#

### Common Symmetric Algorithms

| Algorithm | Status                  |
| --------- | ----------------------- |
| DES       | Old / insecure          |
| 3DES      | Legacy                  |
| AES       | Modern standard         |
| ChaCha20  | Modern high-performance |


### AES (Most Important)

**Advanced Encryption Standard** is the most widely used symmetric encryption algorithm today.

Used in:

- HTTPS/TLS
- VPNs
- disk encryption
- WiFi security
- cloud security

**SSL/TLS uses symmetric encryption** for actual data transfer after handshake completion.

---


## Asymmetric Encryption

### Definition

**Asymmetric encryption uses:**

```text
TWO mathematically related keys
```

- Public Key
- Private Key

#

### Key Concept

| Key Type    | Purpose         |
| ----------- | --------------- |
| Public Key  | Shared publicly |
| Private Key | Kept secret     |

### Visual Flow

```text
Public Key encrypts data
Private Key decrypts data
```

OR

```text
Private Key signs data
Public Key verifies signature
```

### Real World Analogy

**Imagine:**

```text
Public Key = Open padlock
Private Key = Actual padlock key
```

- Anyone can lock the box.
- Only owner can unlock it.

#

### Main Advantages

- solves key distribution problem
- enables authentication
- enables digital signatures
- enables secure communication over internet

### Main Disadvantages

| Problem                  | Description                |
| ------------------------ | -------------------------- |
| Slow                     | Much slower than symmetric |
| CPU Heavy                | Expensive computation      |
| Not ideal for large data | Inefficient                |

#

### Common Asymmetric Algorithms

| Algorithm      | Usage                         |
| -------------- | ----------------------------- |
| RSA            | Traditional SSL/TLS           |
| DSA            | Digital signatures            |
| ECC            | Modern efficient cryptography |
| Diffie-Hellman | Key exchange                  |

### RSA (Very Important)

RSA is one of the most famous asymmetric encryption algorithms.

**Used for:**

- encryption
- digital signatures
- certificate systems
- TLS authentication

---

## Symmetric vs Asymmetric Encryption

This is the CORE FOUNDATION of SSL/TLS.

### Direct Comparison

| Feature        | Symmetric       | Asymmetric                    |
| -------------- | --------------- | ----------------------------- |
| Keys Used      | One key         | Two keys                      |
| Speed          | Fast            | Slow                          |
| CPU Usage      | Low             | High                          |
| Security Model | Shared secret   | Public/private                |
| Main Problem   | Key sharing     | Performance                   |
| Best Use Case  | Data encryption | Key exchange & authentication |

### Core Understanding

**Symmetric encryption is:**

```text
FAST but difficult to share securely
```

**Asymmetric encryption is:**

```text
SECURE for key exchange but slow
```

>SSL/TLS combines BOTH methods together.


---

## How SSL/TLS Uses Both

SSL/TLS uses a hybrid cryptographic model.

### During TLS Handshake

**TLS uses:**

```text
Asymmetric cryptography
```

**Purpose:**

- authentication
- secure key exchange

### After Handshake

**TLS switches to:**

```text
Symmetric cryptography
```

**Purpose:**

- fast encrypted communication
- efficient data transfer

### Why TLS Does This

**Because:**

| Requirement              | Best Method |
| ------------------------ | ----------- |
| Identity verification    | Asymmetric  |
| Secure key exchange      | Asymmetric  |
| High-speed communication | Symmetric   |

**This hybrid design makes TLS:**

- secure
- scalable
- performant

---

## Very Important SSL/TLS Mental Model

Remember this forever:

```text
Asymmetric crypto establishes trust

Symmetric crypto transfers data efficiently
```

OR

```text
Asymmetric = Securely establish secret

Symmetric = Use secret for communication
```

This single concept explains much of TLS architecture.

---

## Real HTTPS Flow Example

**When opening:**

```text
https://google.com
```

Simplified process:

#

**Step 1 — Browser Connects**

- Browser contacts server.

**Step 2 — Certificate Verification**

- Server provides certificate containing public key.
- Browser validates identity.
- (Authentication)

**Step 3 — Secure Key Exchange**

- Browser and server securely establish shared symmetric session key.
- (Asymmetric cryptography)

**Step 4 — Encrypted Communication Begins**

- All actual website traffic now uses symmetric encryption.
- (Fast communication)

---

## Real-World Usage Examples

| Technology      | Uses                   |
| --------------- | ---------------------- |
| HTTPS           | Symmetric + Asymmetric |
| SSH             | Hybrid cryptography    |
| VPN             | Symmetric encryption   |
| WiFi WPA2/WPA3  | AES                    |
| Signal/WhatsApp | Hybrid encryption      |
| Disk Encryption | AES                    |


---

## Final Summary

You MUST remember these concepts clearly:

### Symmetric Encryption

```text
Fast
One shared key
Used for actual data transfer
```

### Asymmetric Encryption

```text
Two keys
Enables trust and secure key exchange
Slower but more secure for identity
```

### MOST IMPORTANT SSL/TLS FOUNDATION

```text
TLS uses asymmetric cryptography to securely establish trust and exchange keys.

TLS then uses symmetric cryptography for fast encrypted communication.
```

---

## Recommended Learning Order After This Phase

Next recommended topics:

| Topic             | Importance               |
| ----------------- | ------------------------ |
| Public key        | Core SSL concept         |
| Private key       | Core SSL concept         |
| Key pair          | Essential                |
| RSA               | Traditional algorithm    |
| ECC/ECDSA         | Modern algorithm         |
| Hashing           | Certificate validation   |
| SHA256            | Common hashing algorithm |
| Digital signature | Certificate trust        |


---