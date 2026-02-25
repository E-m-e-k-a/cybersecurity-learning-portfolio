# Cryptography Basics - TryHackMe

## Date Completed: February 22, 2026

## Module Overview
Covered cryptography fundamentals including the purpose of cryptography, symmetric and asymmetric encryption concepts, classical ciphers (Caesar), and fundamental operations (XOR, modulo) used in modern cryptographic algorithms.

## Why Cryptography Matters

Cryptography solves three core security problems:

### 1. Confidentiality
- Keeps data secret from unauthorized parties
- Encrypted data is unreadable without the key
- Example: HTTPS protects web traffic from eavesdropping

### 2. Integrity
- Ensures data hasn't been tampered with
- Detects unauthorized modifications
- Example: Digital signatures verify file authenticity

### 3. Authentication
- Verifies identity of sender/receiver
- Proves you're communicating with the right party
- Example: TLS certificates verify website identity

---

## Symmetric Encryption

### How It Works
**Same key** used for both encryption and decryption.
```
Plaintext → [Encrypt with Key] → Ciphertext
Ciphertext → [Decrypt with Key] → Plaintext
```

### Characteristics
- **Fast** - Efficient for large data
- **Key distribution problem** - How do you securely share the key?
- **One key** - If key is compromised, all data is exposed

### Common Algorithms
- **AES** (Advanced Encryption Standard) - Most widely used
- **DES** (Data Encryption Standard) - Outdated, weak
- **3DES** (Triple DES) - Better than DES but slower
- **ChaCha20** - Modern, fast stream cipher

### Use Cases
- Encrypting files on disk
- VPN traffic encryption
- Database encryption
- HTTPS bulk data transfer (after key exchange)

---

## Asymmetric Encryption

### How It Works
**Two keys:** Public key (can share) and Private key (keep secret)
```
Encrypt with Public Key → Only Private Key can decrypt
Encrypt with Private Key → Only Public Key can decrypt (digital signatures)
```

### Characteristics
- **Solves key distribution** - Can share public key openly
- **Slower** - Much slower than symmetric encryption
- **Key pairs** - Each user has their own pair

### Common Algorithms
- **RSA** - Most common, used in TLS/SSL
- **ECC** (Elliptic Curve Cryptography) - Smaller keys, same security
- **Diffie-Hellman** - Key exchange protocol

### Use Cases
- **TLS handshake** - Securely exchange symmetric keys
- **Digital signatures** - Verify sender identity
- **SSH keys** - Authenticate without passwords
- **Email encryption** (PGP/GPG)

---

## Hybrid Approach (Real World)

Most systems use **BOTH** symmetric and asymmetric encryption:

**Example: HTTPS**
1. **Asymmetric (RSA)** - Securely exchange session key
2. **Symmetric (AES)** - Encrypt bulk data with session key

**Why?**
- Asymmetric solves key distribution problem
- Symmetric provides speed for large data
- Best of both worlds

---

## Caesar Cipher

### What It Is
Simple **substitution cipher** that shifts each letter by a fixed number of positions.

### How It Works

**Example: Shift 3**
```
Plain:  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
Cipher: D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
```

**Encrypt "HELLO" with shift 3:**
```
H → K
E → H
L → O
L → O
O → R

HELLO → KHOOR
```

**Decrypt "KHOOR" with shift 3:**
```
K → H
H → E
O → L
O → L
R → O

KHOOR → HELLO
```

### Why It's Weak
- Only **25 possible keys** (shifts 1-25)
- **Brute force** in seconds
- No real security value

### Why We Study It
- Introduces **substitution** concept
- Shows basic cipher mechanics
- Foundation for understanding encryption
- Common in CTF challenges (ROT13)

---

## XOR Operation

### What It Is
**Exclusive OR** - Fundamental operation in cryptography.

### Truth Table
```
A  B  | A XOR B
0  0  |   0
0  1  |   1
1  0  |   1
1  1  |   0
```

**Rule:** Output is 1 only when inputs are **different**.

### Why It's Important in Crypto

**Property: XOR is reversible**
```
A XOR B = C
C XOR B = A  ← Get back original!
```

**Example:**
```
Plaintext:  01001000  (H)
Key:        10101010
            --------
Ciphertext: 11100010  (XOR result)

Ciphertext: 11100010
Key:        10101010
            --------
Plaintext:  01001000  (H) ← Back to original!
```

### Used In
- Stream ciphers
- One-time pads
- Block cipher modes
- Hash functions
- Random number generation

---

## Modulo Operation

### What It Is
**Remainder after division** - Used to wrap values in a range.

### Examples
```
10 mod 3 = 1    (10 ÷ 3 = 3 remainder 1)
17 mod 5 = 2    (17 ÷ 5 = 3 remainder 2)
26 mod 26 = 0   (26 ÷ 26 = 1 remainder 0)
28 mod 26 = 2   (28 ÷ 26 = 1 remainder 2)
```

### Why It's Important in Crypto

**Wrapping values in range:**
```
Caesar Cipher uses mod 26 (26 letters in alphabet)

Letter X (position 24) + shift 5 = 29
29 mod 26 = 3 (letter C)

Wraps from Z back to A!
```

### Used In
- Caesar cipher (mod 26)
- RSA encryption (mod n)
- Hash functions
- Key generation algorithms

---

## Symmetric vs Asymmetric Comparison

| Feature | Symmetric | Asymmetric |
|---------|-----------|------------|
| Keys | Same key for encrypt/decrypt | Public + Private key pair |
| Speed | Fast | Slow |
| Key size | Smaller (128-256 bits) | Larger (2048-4096 bits) |
| Key distribution | Difficult (need secure channel) | Easy (public key can be shared) |
| Use case | Bulk data encryption | Key exchange, signatures |
| Examples | AES, DES, ChaCha20 | RSA, ECC, Diffie-Hellman |

---

## Security Applications

### For SOC Analysts

**Identifying encryption in traffic:**
- **Port 443** = HTTPS (TLS/SSL)
- **Port 22** = SSH (asymmetric auth + symmetric tunnel)
- Encrypted malware C2 channels

**Investigating incidents:**
- Ransomware uses encryption to lock files
- Understanding crypto helps analyze attack methods
- Weak encryption = vulnerability

**Protocol analysis:**
- TLS handshake uses asymmetric (RSA/ECC) then symmetric (AES)
- Identifying cipher suites in packet captures
- Detecting downgrade attacks (forcing weak encryption)

---

## What I Learned

### Technical Concepts:
- Difference between symmetric and asymmetric encryption
- How Caesar cipher demonstrates substitution
- XOR as a reversible operation for encryption
- Modulo for wrapping values in cipher algorithms

### Security Perspective:
- Encryption is math, not magic
- Weak encryption (like Caesar) is easily broken
- Modern security uses hybrid approach (asymmetric + symmetric)
- Understanding crypto helps analyze encrypted traffic

### Key Insight:
Every secure connection (HTTPS, SSH, VPN) relies on these fundamentals. Understanding how encryption works at a basic level helps me recognize when it's implemented poorly or being attacked.

---

## Real-World Examples

### HTTPS Connection
1. Browser connects to website
2. **Asymmetric (RSA)** - Exchange session key securely
3. **Symmetric (AES)** - Encrypt all data with session key
4. Fast, secure communication

### SSH Login
1. Client connects to server
2. **Asymmetric** - Verify server identity + authenticate user
3. **Symmetric** - Encrypt terminal session
4. Secure remote access

### Ransomware Attack
1. Malware generates **asymmetric key pair**
2. Encrypts files with **symmetric key** (fast)
3. Encrypts symmetric key with attacker's **public key**
4. Victim can't decrypt without attacker's **private key**

---

## Next Steps
- Study TLS/SSL handshake in detail
- Learn cryptanalysis techniques
- Practice identifying encryption in packet captures
- Understand hash functions and digital signatures

---

## Resources
- TryHackMe - Cryptography Basics Room
- Khan Academy - Cryptography course
- Cryptopals challenges
