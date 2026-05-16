# SSL/TLS Learning Journey: Phase 2 — Security Basics

>[!Important]
**Cryptography** is the practice and science of **concealing information** by converting **readable text (plaintext)** into an **unreadable format (ciphertext)**, so that only authorized parties can **decipher** it

---

## Purpose of This Phase

This phase builds the core security foundation required to understand SSL/TLS.

Before learning certificates, PKI, HTTPS, or TLS handshakes, you MUST understand:

- how data is protected
- how encryption works
- why authentication matters
- how integrity is verified
- why symmetric and asymmetric encryption both exist

This is one of the **MOST IMPORTANT** phases in the entire **SSL/TLS** learning journey.

>Without understanding these concepts clearly, SSL/TLS will feel confusing later.

---

## Learning Objectives

After completing this phase, you should be able to:

- explain encryption and decryption
- understand plaintext and ciphertext
- explain confidentiality, integrity, and authentication


---

## Core Security Terminologies - Part 1

### Plain Text

Plain text is the original readable data before encryption.

**Examples:**

```text
Hello World
mypassword123
bank-account-number
```

>Anyone can read plain text if they intercept it.

#

### Cipher Text

Cipher text is encrypted unreadable data.

**Example:**

```text
Xj82@kL#92!asP
```

Humans cannot understand ciphertext without proper decryption.

#

### Encryption

**Encryption is the process of converting:**

```text
Plain Text → Cipher Text
```

**Purpose:**

- protect data from unauthorized access
- maintain confidentiality

**Example:**

```text
HELLO → x93Ka#@
```

#

### Decryption

**Decryption converts:**

```text
Cipher Text → Plain Text
```

Only authorized users with the correct key should be able to decrypt the data.

---

## Why Encryption Exists

**Encryption exists to protect data during:**

- transmission
- storage
- communication

**Without encryption:**

- attackers can read passwords
- banking data can be stolen
- API traffic becomes visible
- login sessions can be hijacked

SSL/TLS exists mainly because the internet is untrusted.

---

## The CIA Triad (Core Security Principles of Cryptography)

These are foundational cybersecurity concepts.

### Confidentiality

**Confidentiality means:**

```
Only authorized people can read the data.
```

**Achieved using:**

- encryption
- access control
- permissions

**Example:**

```text
HTTPS encrypts website traffic
```

**Without confidentiality:**

- attackers can sniff passwords
- sensitive data becomes exposed

#

### Integrity

**Integrity means:**

```
Data was NOT modified during transmission.
```

**Example:**

- Original:

  ```text
  Transfer 1000 taka
  ```

- Modified by attacker:

  ```text
  Transfer 100000 taka
  ```

>Integrity mechanisms detect tampering.

**Usually achieved using:**

- hashing
- message authentication
- digital signatures

>SSL/TLS uses integrity checking heavily.

#

### Authentication

**Authentication means:**

```
Verifying identity.
```

**Questions answered:**

```text
Who are you?
Can I trust you?
```

**Examples:**

- website certificate validation
- username/password
- SSH keys
- MFA

**SSL/TLS authentication ensures:**

```text
You are really communicating with the actual server
```

and not an attacker.


---

## Final Summary

You MUST remember these concepts clearly:

### Encryption

```text
Protects confidentiality
```

### Integrity

```text
Ensures data was not modified
```

### Authentication

```text
Verifies identity
```

---

## Next Phase

### Phase 3 — Cryptography Fundamentals


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