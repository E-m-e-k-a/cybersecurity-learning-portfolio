# Week of February 16-22, 2026

## Overview
Focused week on networking fundamentals, protocol analysis, and traffic inspection — building the foundation for network security monitoring and threat detection.

## Modules Completed

### 1. Command Line (Windows CLI)
**Key Skills:**
- System enumeration with `systeminfo`, `ver`, `set`
- Network diagnostics with `ipconfig`, `ping`, `tracert`, `nslookup`
- Connection monitoring with `netstat -ano`
- Process identification with `tasklist`

**Security Application:**
- Rapid system assessment during incidents
- Network troubleshooting and investigation
- Matching processes to network connections for malware detection

---

### 2. Networking Essentials
**Protocols Covered:**
- **DHCP** - DORA process (Discover, Offer, Request, Acknowledge)
- **ARP** - IP to MAC address resolution
- **ICMP** - Network diagnostics (ping, error reporting)
- **Traceroute** - Packet path mapping
- **Routing** - Packet forwarding between networks
- **NAT** - Private to public IP translation

**Security Application:**
- Understanding normal protocol behavior for anomaly detection
- ARP spoofing detection
- Network path analysis
- DHCP rogue server identification

---

### 3. Networking Core Protocols
**Application Layer Protocols:**
- **DNS** (Port 53) - Record types (A, AAAA, MX, TXT, CNAME), nslookup, whois
- **HTTP/HTTPS** (Ports 80/443) - Methods (GET, POST, PUT, DELETE), manual requests via telnet
- **FTP** (Port 21) - File transfer commands and operations
- **SMTP** (Port 25) - Email sending
- **POP3** (Port 110) - Email download
- **IMAP** (Port 143) - Email synchronization

**Security Application:**
- Protocol-based reconnaissance detection
- Identifying encrypted vs unencrypted communications
- Email header analysis for phishing investigations
- DNS enumeration and threat intelligence

---

### 4. Network Security Approaches
**Three Methods to Secure Network Traffic:**
1. **TLS** - Wraps protocols with encryption (HTTPS, SMTPS, POP3S, IMAPS)
2. **SSH** - Remote access and secure tunneling for plaintext protocols
3. **VPN** - Encrypted tunnels between networks

**Security Application:**
- Identifying insecure protocol usage
- Understanding when encryption is applied
- Choosing appropriate security method for different scenarios

---

### 5. Wireshark Fundamentals
**Core Concepts:**
- Packet capture and PCAP file analysis
- Interface components (toolbar, packet list, packet details, packet bytes)
- Color coding for quick traffic identification
- Merging multiple PCAP files
- Layer-by-layer packet inspection

**Security Application:**
- Visual traffic analysis
- Protocol behavior investigation
- Incident response evidence collection
- Network forensics

---

### 6. Tcpdump Basics
**Command-Line Packet Analysis:**
- Reading PCAP files with `-r`
- Hostname suppression with `-n` for speed
- TCP flag filtering for port scan detection
- Packet size filtering for data exfiltration detection
- Combining with Unix tools (`wc -l`, `awk`, `sort`, `uniq`)

**Key Commands:**
```bash
# Detect port scans
sudo tcpdump -r traffic.pcap 'tcp[tcpflags] == tcp-rst' | wc -l

# Find data exfiltration
sudo tcpdump -r traffic.pcap 'greater 15000' -n

# Count DNS queries
sudo tcpdump -r traffic.pcap 'udp port 53' | wc -l
```

**Security Application:**
- Processing massive PCAP files efficiently
- Automated threat hunting with filters
- Port scan and reconnaissance detection
- Large data transfer identification

---

## Key Takeaways

### Technical Growth:
- Transitioned from GUI-dependent analysis to command-line proficiency
- Understanding protocols at implementation level, not just theory
- Ability to capture, filter, and analyze network traffic

### Security Mindset:
- Every protocol is both a communication tool and potential attack surface
- Understanding normal behavior is essential for detecting anomalies
- Command-line tools scale better for real-world investigations

### SOC Analyst Relevance:
All six modules directly apply to daily SOC operations:
- Protocol knowledge enables alert triage
- Traffic analysis tools (Wireshark/Tcpdump) are core investigation tools
- Understanding encryption helps identify security gaps
- Command-line proficiency required for remote system investigation

---

## Previous Projects
- **Homelab Setup** - Ubuntu (defensive), Kali Linux (offensive), Metasploitable (vulnerable target)
- Host-only networking for isolation
- Connectivity testing and Nmap service enumeration

---

## Next Steps
- Continue Cybersecurity 101 path
- Deeper traffic analysis and filtering techniques
- Protocol-specific threat detection
- Comprehensive project after path completion

---

## Statistics
- **Modules Completed:** 6
- **Skills Acquired:** Network protocol analysis, packet capture, command-line tools, traffic filtering
- **Tools Mastered:** Windows CLI, Wireshark, Tcpdump, nslookup, whois, telnet, FTP client
