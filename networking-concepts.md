# Networking Concepts - TryHackMe

## Date Completed: February 12, 2026

## Module Overview
This room covered fundamental networking concepts essential for cybersecurity: the OSI and TCP/IP models, IP addressing and subnetting, routing basics, TCP vs UDP protocols, data encapsulation, and hands-on practice using Telnet to communicate with servers over TCP.

## Learning Objectives
- Understand OSI and TCP/IP networking models
- Learn IP addressing and subnet calculations
- Compare TCP and UDP protocols
- Understand data encapsulation process
- Practice establishing TCP connections with Telnet

---

## The OSI Model (Open Systems Interconnection)

### What is the OSI Model?

A conceptual framework that standardizes network communication into 7 distinct layers. Each layer has specific responsibilities and communicates with the layers directly above and below it.

**The 7 Layers (top to bottom):**
```
7. Application Layer    - User-facing applications (HTTP, FTP, SSH)
6. Presentation Layer   - Data formatting, encryption (SSL/TLS)
5. Session Layer        - Connection management
4. Transport Layer      - End-to-end communication (TCP, UDP)
3. Network Layer        - Routing and IP addressing
2. Data Link Layer      - MAC addresses, switches
1. Physical Layer       - Physical transmission (cables, signals)
```

---

### Layer-by-Layer Breakdown

#### Layer 7: Application Layer
**What it does:** Provides network services directly to applications

**Protocols:** HTTP, HTTPS, FTP, SSH, DNS, SMTP, Telnet

**Example:** 
- Web browser requesting a webpage
- Email client sending mail
- SSH client connecting to server

**Security relevance:**
- Most attacks target this layer (SQL injection, XSS, RCE)
- Application vulnerabilities are common entry points
- Where authentication and authorization happen

---

#### Layer 6: Presentation Layer
**What it does:** Data translation, encryption, compression

**Functions:**
- Converts data formats (ASCII, JPEG, MP3)
- Encryption/decryption (SSL/TLS)
- Data compression

**Security relevance:**
- SSL/TLS encryption protects data in transit
- Weak encryption = vulnerable to man-in-the-middle attacks
- Certificate validation happens here

---

#### Layer 5: Session Layer
**What it does:** Establishes, maintains, terminates connections

**Functions:**
- Session establishment
- Session management
- Session termination

**Security relevance:**
- Session hijacking attacks target this layer
- Session tokens must be protected
- Connection state management

---

#### Layer 4: Transport Layer
**What it does:** End-to-end data delivery, error checking, flow control

**Protocols:** 
- **TCP** (Transmission Control Protocol) - reliable
- **UDP** (User Datagram Protocol) - fast, unreliable

**Key concepts:**
- Port numbers (0-65535)
- Segmentation
- Flow control
- Error detection

**Security relevance:**
- Port scanning targets this layer
- TCP SYN floods (DoS attacks)
- Understanding ports is critical for security

---

#### Layer 3: Network Layer
**What it does:** Routing packets between networks, IP addressing

**Protocols:** IP, ICMP, IGMP

**Key concepts:**
- IP addresses (logical addressing)
- Routing (finding path to destination)
- Packet forwarding

**Security relevance:**
- IP spoofing attacks
- ICMP used in reconnaissance (ping sweeps)
- Routing vulnerabilities
- Understanding IP addressing for network segmentation

---

#### Layer 2: Data Link Layer
**What it does:** Node-to-node data transfer, MAC addressing

**Protocols:** Ethernet, Wi-Fi, ARP

**Key concepts:**
- MAC addresses (physical addressing)
- Switches operate here
- Frame creation

**Security relevance:**
- ARP spoofing/poisoning attacks
- MAC address filtering (weak security)
- Man-in-the-middle attacks at this layer

---

#### Layer 1: Physical Layer
**What it does:** Physical transmission of raw bits

**Components:** Cables, switches, NICs, wireless signals

**Security relevance:**
- Physical access = game over
- Cable tapping (rare but possible)
- Wireless sniffing

---

## The TCP/IP Model

### Four-Layer Model (Practical Implementation)

The TCP/IP model is what's actually used in real networks. It's simpler than OSI:
```
4. Application Layer     - Combines OSI layers 5, 6, 7
3. Transport Layer       - Same as OSI Layer 4
2. Internet Layer        - Same as OSI Layer 3
1. Network Access Layer  - Combines OSI layers 1, 2
```

---

### OSI vs TCP/IP Comparison

| OSI Model | TCP/IP Model | Purpose |
|-----------|--------------|---------|
| Application | Application | User-facing services |
| Presentation | Application | Data formatting |
| Session | Application | Connection management |
| **Transport** | **Transport** | TCP/UDP, ports |
| **Network** | **Internet** | IP addressing, routing |
| Data Link | Network Access | MAC addresses |
| Physical | Network Access | Physical transmission |

**Key difference:** 
- OSI = theoretical framework (how it should work)
- TCP/IP = practical implementation (how it actually works)

**Why learn both?**
- OSI helps troubleshoot (identify which layer has the problem)
- TCP/IP is what you'll actually see in practice

---

## IP Addressing & Subnetting

### IPv4 Addresses

**Format:** Four octets (0-255) separated by dots

**Example:** 192.168.1.100

**Binary representation:**
```
192.168.1.100
11000000.10101000.00000001.01100100
```

---

### IP Address Classes (Original Design)

| Class | Range | Default Subnet | Private Range |
|-------|-------|----------------|---------------|
| A | 1.0.0.0 - 126.255.255.255 | /8 | 10.0.0.0/8 |
| B | 128.0.0.0 - 191.255.255.255 | /16 | 172.16.0.0/12 |
| C | 192.0.0.0 - 223.255.255.255 | /24 | 192.168.0.0/16 |

**Private IP ranges (RFC 1918):**
- 10.0.0.0 - 10.255.255.255
- 172.16.0.0 - 172.31.255.255
- 192.168.0.0 - 192.168.255.255

**Security note:** Private IPs are not routable on the internet (used internally)

---

### Subnetting Basics

**What is a subnet?**
A logical subdivision of an IP network.

**Subnet mask:** Defines which part is network vs host

**Example:**
```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
Network:       192.168.1.0
Broadcast:     192.168.255.255
Usable IPs:    192.168.1.1 - 192.168.1.254
```

**CIDR Notation:**
- /24 = 255.255.255.0 (256 addresses, 254 usable)
- /16 = 255.255.0.0 (65,536 addresses)
- /8 = 255.0.0.0 (16,777,216 addresses)

**Security relevance:**
- Network segmentation for security
- DMZ isolation
- Understanding target network ranges
- Calculating scan scope

---

### Routing Basics

**What is routing?**
The process of forwarding packets from source to destination across networks.

**Key concepts:**
- **Router:** Device that forwards packets between networks
- **Default gateway:** Router that connects local network to outside
- **Routing table:** Database of network paths

**Example:**
```
My computer (192.168.1.100) wants to reach google.com (8.8.8.8)
1. Check: Is 8.8.8.8 on my local network? (No - different subnet)
2. Send packet to default gateway (router)
3. Router forwards to next hop
4. Process repeats until packet reaches 8.8.8.8
```

**Security relevance:**
- Attackers need routing knowledge for lateral movement
- Understanding network topology for pentesting
- Firewall rules based on routing
- traceroute reveals network paths

---

## TCP vs UDP

### TCP (Transmission Control Protocol)

**Characteristics:**
- **Connection-oriented:** Three-way handshake before data transfer
- **Reliable:** Guarantees delivery, retransmits lost packets
- **Ordered:** Data arrives in correct sequence
- **Flow control:** Adjusts speed based on network conditions
- **Error checking:** Detects and corrects errors

**Three-Way Handshake:**
```
Client → Server: SYN (Synchronize)
Server → Client: SYN-ACK (Synchronize-Acknowledge)
Client → Server: ACK (Acknowledge)
[Connection established, data transfer begins]
```

**When TCP is used:**
- HTTP/HTTPS (web browsing)
- FTP (file transfers)
- SSH (remote access)
- Email (SMTP, POP3, IMAP)
- Any application requiring reliability

**Security implications:**
- TCP SYN flood attacks exploit handshake
- Port scanning detects open TCP ports
- Sequence number prediction attacks
- Connection hijacking possible

---

### UDP (User Datagram Protocol)

**Characteristics:**
- **Connectionless:** No handshake, just send
- **Unreliable:** No delivery guarantee
- **Unordered:** Packets may arrive out of order
- **No flow control:** Sender doesn't care about receiver state
- **Fast:** Lower overhead than TCP

**When UDP is used:**
- DNS (domain lookups)
- DHCP (IP address assignment)
- Streaming (video, VoIP)
- Gaming (speed over reliability)
- SNMP (network management)

**Security implications:**
- UDP flood attacks (volume-based DoS)
- UDP amplification attacks (reflection attacks)
- Harder to detect/filter than TCP
- No connection state to track

---

### TCP vs UDP Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Yes (handshake) | No |
| Reliability | Guaranteed delivery | Best effort |
| Ordering | Packets arrive in order | May be out of order |
| Speed | Slower (more overhead) | Faster (less overhead) |
| Use case | Reliability critical | Speed critical |
| Header size | 20-60 bytes | 8 bytes |
| Error checking | Yes, with retransmission | Basic checksum only |

**Security perspective:**
- TCP = stateful (trackable connections)
- UDP = stateless (harder to track)

---

## Data Encapsulation

### What is Encapsulation?

The process of wrapping data with protocol information at each layer as it moves DOWN the OSI stack.

**The Process:**
```
Application Layer:    [DATA]
↓
Transport Layer:      [TCP Header][DATA]
↓
Network Layer:        [IP Header][TCP Header][DATA]
↓
Data Link Layer:      [Ethernet Header][IP Header][TCP Header][DATA][Ethernet Trailer]
↓
Physical Layer:       Transmitted as bits (1010101...)
```

**At each layer:**
- Header is added (protocol information)
- Original data becomes "payload" for next layer
- Receiver reverses process (de-encapsulation)

---

### Terminology at Each Layer

| Layer | Data Unit Name | Information Added |
|-------|----------------|-------------------|
| Application | Data | Application data |
| Transport | Segment (TCP) / Datagram (UDP) | Source/Dest ports |
| Network | Packet | Source/Dest IP addresses |
| Data Link | Frame | Source/Dest MAC addresses |
| Physical | Bits | Electrical/optical signals |

**Security relevance:**
- Packet analysis requires understanding encapsulation
- Deep packet inspection examines all layers
- Attackers can manipulate headers at any layer
- Encryption typically happens at specific layers

---

## Hands-On: Using Telnet

### What is Telnet?

A protocol for establishing unencrypted text-based connections to remote hosts over TCP.

**Default port:** 23

**Security warning:** Telnet sends everything in plaintext (including passwords!) - use SSH instead in production

**Why we still use it:** Great for testing and learning TCP connections

---

### Telnet Practice Examples

#### Example 1: Connect to HTTP Server

**Testing web server manually:**
```bash
telnet example.com 80
```

**Once connected, type HTTP request:**
```
GET / HTTP/1.1
Host: example.com
```

**Press Enter twice** (blank line signals end of request)

**Response:**
```
HTTP/1.1 200 OK
Content-Type: text/html
...
[HTML content]
```

**What this demonstrates:**
- TCP connection on port 80
- HTTP is text-based protocol
- Manual HTTP requests possible
- Understanding protocol structure

---

#### Example 2: Test SMTP Mail Server
```bash
telnet mail.example.com 25
```

**SMTP commands:**
```
HELO example.com
MAIL FROM: test@example.com
RCPT TO: recipient@example.com
DATA
Subject: Test

This is a test email.
.
QUIT
```

**Security relevance:** 
- Testing mail server configuration
- Understanding email protocol
- Identifying open mail relays (spamming vector)

---

#### Example 3: Banner Grabbing

**Connect to service and grab banner:**
```bash
telnet 192.168.1.100 22
```

**Response:**
```
SSH-2.0-OpenSSH_7.4p1 Ubuntu-10
```

**Information gained:**
- Service running: SSH
- Version: OpenSSH 7.4p1
- OS: Ubuntu

**Security use:**
- Reconnaissance during pentesting
- Version info helps find exploits
- Defenders can disable banners to hide info

---

### What We Learned from Telnet Practice

**Key insights:**
1. **TCP connections are two-way:** Client and server both send/receive
2. **Protocols are text-based:** Many protocols use human-readable commands
3. **Port numbers matter:** Different services on different ports
4. **Banner information leaks:** Services reveal version info
5. **Three-way handshake happens first:** Before any data exchange

**From Pre Security connection:**
- Applied TCP three-way handshake knowledge
- Used port numbers learned in networking module
- Understood client-server communication

---

## Security Applications

### Offensive Security (Pentesting)

**Network reconnaissance:**
- Understanding TCP/IP helps with port scanning
- Knowing common ports aids service identification
- Routing knowledge helps map networks
- Banner grabbing reveals versions

**Protocol exploitation:**
- TCP SYN scans leverage handshake process
- UDP scans for connectionless services
- Layer-specific attacks (ARP spoofing, IP spoofing)
- Understanding encapsulation for packet crafting

---

### Defensive Security (SOC)

**Network monitoring:**
- Analyzing packet captures requires protocol knowledge
- Identifying normal vs anomalous traffic
- Understanding what protocols should/shouldn't exist
- Troubleshooting connectivity issues

**Threat detection:**
- Port scans visible in network logs
- Unusual protocols indicate compromise
- Wrong port/protocol combinations (red flag)
- Understanding attack patterns at each layer

---

## Connection to Previous Learning

### From Pre Security - Networking Modules:

**Built directly on:**
- OSI model introduction → Now deeper understanding
- TCP/IP basics → Now practical application
- IP addressing → Now subnetting and routing
- Common protocols → Now hands-on practice

**This module added:**
- OSI vs TCP/IP comparison
- Detailed encapsulation process
- Hands-on Telnet practice
- Security implications at each layer

---

## What I Found Challenging

**OSI vs TCP/IP confusion:**
- Initially struggled with which model to use when
- Realized: OSI for troubleshooting, TCP/IP for implementation

**Subnetting calculations:**
- Binary math was rusty
- Practice with subnet calculators helped
- Understanding CIDR notation took time

**Encapsulation process:**
- Tracking headers through layers was abstract
- Wireshark analysis made it concrete (future learning)

---

## What Made Sense

**TCP three-way handshake:**
- Clear, logical process
- Easy to visualize
- Connected to port scanning knowledge

**IP addressing:**
- Built on Pre Security knowledge
- Private vs public ranges make sense
- Network segmentation purpose clear

**Telnet hands-on:**
- Seeing protocols in action was powerful
- Understanding "behind the scenes" of web browsing
- Realizing how simple protocols actually are

---

## Next Steps

### Immediate practice:
1. Use Wireshark to analyze packet captures
2. Practice subnetting calculations
3. Test Telnet connections to various services
4. Identify protocols in network traffic

### Advanced learning:
1. Deep packet analysis
2. Protocol-specific attacks
3. Network security monitoring
4. Routing and switching concepts

### Security applications:
1. Port scanning with protocol knowledge
2. Network traffic analysis
3. Identifying C2 communication
4. Understanding attack patterns

---

## SOC Analyst Relevance

### Daily SOC Operations:

**Network traffic analysis:**
- Understanding what's normal requires protocol knowledge
- Identifying suspicious connections
- Analyzing SIEM alerts related to network activity

**Incident response:**
- Troubleshooting requires OSI model thinking
- "Is this a Layer 3 issue or Layer 4?"
- Understanding attack vectors at each layer

**Threat hunting:**
- Looking for protocol anomalies
- Unusual port usage
- Unexpected network communication

**Examples:**
```
Normal: DNS on port 53 UDP
Suspicious: DNS on port 8080 TCP (DNS tunneling?)

Normal: HTTP on port 80 TCP
Suspicious: HTTP on port 443 UDP (unusual)
```

---

## Key Takeaways

✅ **OSI Model** - 7 layers, theoretical framework for troubleshooting
✅ **TCP/IP Model** - 4 layers, practical implementation
✅ **IP Addressing** - Logical addressing, subnetting for segmentation
✅ **Routing** - Moving packets between networks
✅ **TCP vs UDP** - Reliable vs fast, connection vs connectionless
✅ **Encapsulation** - Headers added at each layer going down the stack
✅ **Telnet practice** - Hands-on understanding of TCP connections

**Bottom line:** You cannot do cybersecurity without understanding networking. Every attack, every defense, every security control operates within these networking fundamentals.

Whether you're analyzing suspicious traffic as a SOC analyst or exploiting services as a pentester, networking knowledge is mandatory.

---

## Resources

- RFC 791 (IP)
- RFC 793 (TCP)
- RFC 768 (UDP)
- TryHackMe Networking Concepts room
- Wireshark for packet analysis practice

---

## Reflection

This module solidified my understanding of how networks actually work. Pre Security taught me the basics, but this module showed me the practical implementation.

The most valuable insight: Security happens at every layer. Application vulnerabilities, transport attacks, network reconnaissance, data link spoofing - understanding the full stack is essential.

For my career path (SOC → Offensive):
- **As SOC analyst:** I'll analyze network traffic, requiring deep protocol knowledge
- **As pentester:** I'll exploit protocol weaknesses, leveraging this foundation

Networking isn't just an IT skill - it's THE foundation of all cybersecurity work. You can't protect what you don't understand, and you can't exploit what you don't comprehend.

This knowledge will be applied in every future module, every security tool, every investigation. It's that fundamental.
