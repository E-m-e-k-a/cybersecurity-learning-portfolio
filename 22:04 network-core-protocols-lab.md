# Networking Core Protocols - TryHackMe

## Date Completed: February 17, 2026

## Module Overview
This module covered essential application layer protocols that power everyday internet services including DNS, HTTP/HTTPS, FTP, and email protocols (SMTP, POP3, IMAP), with hands-on command-line interaction and practical security applications.

## Learning Objectives
- Understand DNS structure and record types
- Master HTTP/HTTPS methods and operations
- Use FTP for file transfer operations
- Understand email protocol workflows
- Practice protocol interaction via command line

---

## Protocols Learned & Mastered

### DNS (Domain Name System)

#### What it does
Translates human-readable domain names (example.com) into IP addresses that computers use to communicate.

**Default Port:** 53 (UDP/TCP)

#### DNS Record Types

**A Record** - Maps domain to IPv4 address
```
example.com -> 192.0.2.1
```

**AAAA Record** - Maps domain to IPv6 address
```
example.com -> 2001:0db8:85a3::8a2e:0370:7334
```

**MX Record** - Specifies mail servers for domain
```
example.com -> mail.example.com (Priority: 10)
```

**CNAME Record** - Creates alias for another domain
```
www.example.com -> example.com
```

**TXT Record** - Holds text information (SPF, DKIM, verification)
```
example.com -> "v=spf1 include:_spf.google.com ~all"
```

**NS Record** - Specifies authoritative name servers
```
example.com -> ns1.example.com
```

**PTR Record** - Reverse DNS lookup (IP to domain)
```
192.0.2.1 -> example.com
```

#### Tools Used

**nslookup** - Query DNS servers
```bash
nslookup example.com                    # Basic lookup
nslookup -type=MX example.com          # Query MX records
nslookup -type=TXT example.com         # Query TXT records
nslookup example.com 8.8.8.8           # Query specific DNS server
```

**whois** - Domain registration information
```bash
whois example.com
```

**Output includes:**
- Domain registrar
- Registration/expiration dates
- Name servers
- Registrant contact information (if not privacy-protected)

#### Security Relevance

**DNS Reconnaissance:**
- Attackers use DNS enumeration to map targets
- MX records reveal mail infrastructure
- TXT records may leak SPF/DKIM configurations
- Subdomains discovered through DNS queries

**DNS-based Attacks:**
- DNS spoofing/cache poisoning
- DNS tunneling for data exfiltration
- DDoS amplification attacks
- Typosquatting domains

**SOC Detection Opportunities:**
- Unusual DNS query volumes
- Queries to newly registered domains (NRDs)
- DNS tunneling patterns (large TXT record queries)
- Queries to known malicious domains

---

### HTTP/HTTPS (HyperText Transfer Protocol)

#### What it does
Protocol for transferring web pages and data between browsers and web servers.

**Default Ports:**
- HTTP: Port 80 (unencrypted)
- HTTPS: Port 443 (encrypted with TLS/SSL)

**Transport Protocol:** TCP

#### HTTP Methods

**GET** - Retrieve data from server
```http
GET /index.html HTTP/1.1
Host: example.com
```
- Most common method
- Parameters visible in URL
- Can be cached
- Should not modify server data

**POST** - Send data to server
```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=user&password=pass
```
- Used for forms, file uploads
- Data in request body (not URL)
- Not cached
- Modifies server state

**PUT** - Upload/update resource
```http
PUT /api/user/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{"name": "John Doe", "email": "john@example.com"}
```
- Creates or replaces resource
- Idempotent (same result if repeated)

**DELETE** - Remove resource
```http
DELETE /api/user/123 HTTP/1.1
Host: example.com
```
- Removes specified resource
- Idempotent

#### Manual HTTP Request with Telnet
```bash
telnet example.com 80

GET / HTTP/1.1
Host: example.com

[Press Enter twice]
```

**What happens:**
1. Connect to web server on port 80
2. Send GET request for homepage
3. Server responds with HTML and headers
4. Connection closes

**Example response:**
```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<html>
...
</html>
```

#### HTTP Status Codes

**2xx - Success:**
- 200 OK - Request succeeded
- 201 Created - Resource created
- 204 No Content - Success but no data returned

**3xx - Redirection:**
- 301 Moved Permanently
- 302 Found (temporary redirect)
- 304 Not Modified (cached)

**4xx - Client Errors:**
- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found

**5xx - Server Errors:**
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable

#### Security Relevance

**HTTP vs HTTPS:**
- HTTP transmits in plaintext (credentials visible)
- HTTPS encrypts with TLS (secure)
- Man-in-the-middle attacks exploit HTTP
- Always verify HTTPS is used for sensitive data

**Attack Vectors:**
- SQL injection via GET/POST parameters
- Cross-Site Scripting (XSS) in user input
- Cross-Site Request Forgery (CSRF)
- HTTP smuggling attacks
- Unauthorized PUT/DELETE if improperly secured

**SOC Detection:**
- Unusual HTTP methods (PUT/DELETE on unexpected resources)
- HTTP on ports other than 80
- Suspicious user agents
- Large POST requests (potential data exfiltration)
- Non-standard status code patterns

---

### FTP (File Transfer Protocol)

#### What it does
Transfers files between client and server over a network.

**Default Port:** 21 (control connection)
**Data Port:** 20 (data transfer in active mode)

**Transport Protocol:** TCP

#### FTP Commands

**Connection:**
```bash
ftp 10.10.10.10
```

**Authentication:**
```
Name: username
Password: password
```

**Common anonymous access:**
```
Name: anonymous
Password: [blank or email]
```

**Navigation:**
```ftp
ls                  # List files
pwd                 # Print working directory
cd directory        # Change directory
cd ..               # Go up one level
```

**File Transfer:**
```ftp
get filename        # Download file
put filename        # Upload file
mget *.txt          # Download multiple files
mput *.txt          # Upload multiple files
```

**File Management:**
```ftp
delete filename     # Delete remote file
mkdir dirname       # Create directory
rmdir dirname       # Remove directory
rename old new      # Rename file
```

**Transfer Modes:**
```ftp
binary              # Binary mode (for executables, images)
ascii               # ASCII mode (for text files)
```

**Exit:**
```ftp
bye                 # Close connection and exit
quit                # Same as bye
```

#### FTP Modes

**Active Mode:**
- Client opens random port
- Server initiates data connection from port 20
- Firewall issues (server connects to client)

**Passive Mode:**
```ftp
passive             # Enable passive mode
```
- Client initiates both connections
- Better for firewalls/NAT

#### Security Relevance

**FTP Vulnerabilities:**
- Credentials transmitted in plaintext
- Data transmitted unencrypted
- Anonymous access often misconfigured
- Bounce attacks possible

**Secure Alternatives:**
- SFTP (SSH File Transfer Protocol) - Port 22
- FTPS (FTP over TLS/SSL) - Ports 989/990
- SCP (Secure Copy)

**SOC Detection:**
- Unauthorized FTP connections
- FTP on non-standard ports
- Large file transfers (data exfiltration)
- Anonymous FTP access attempts
- FTP brute-force login attempts

---

### SMTP (Simple Mail Transfer Protocol)

#### What it does
Sends email from client to server and between mail servers.

**Default Port:** 25 (unencrypted)
**Secure Ports:**
- 587 (STARTTLS - submission)
- 465 (SMTP over SSL - deprecated but still used)

**Transport Protocol:** TCP

#### How SMTP Works

**Email sending process:**
```
User's Email Client
    ↓ SMTP (port 587)
Sender's Mail Server
    ↓ SMTP (port 25)
Recipient's Mail Server
    ↓ POP3/IMAP
Recipient's Email Client
```

#### SMTP Commands

**Connection:**
```bash
telnet mail.example.com 25
```

**Basic SMTP session:**
```smtp
HELO client.example.com         # Identify yourself
MAIL FROM: <sender@example.com> # Sender address
RCPT TO: <recipient@example.com># Recipient address
DATA                             # Begin message content
Subject: Test Email

This is the email body.
.                                # End with period on new line
QUIT                             # Close connection
```

**Response codes:**
- 220 - Service ready
- 250 - OK
- 354 - Start mail input
- 550 - Mailbox unavailable
- 554 - Transaction failed

#### Security Relevance

**SMTP Vulnerabilities:**
- Open relay (forwards mail for anyone - spam tool)
- Email spoofing (MAIL FROM can be forged)
- No built-in authentication in basic SMTP
- Plaintext transmission

**Email Security:**
- SPF (Sender Policy Framework) - TXT record validation
- DKIM (DomainKeys Identified Mail) - Email signing
- DMARC (Domain-based Message Authentication) - Policy enforcement

**SOC Detection:**
- Phishing email analysis
- Email header examination
- SPF/DKIM/DMARC validation failures
- Mass email sending (spam/malware distribution)
- Email account compromise

---

### POP3 (Post Office Protocol v3)

#### What it does
Downloads email from server to client, typically deleting from server.

**Default Port:** 110 (unencrypted)
**Secure Port:** 995 (POP3S - SSL/TLS)

**Transport Protocol:** TCP

#### How POP3 Works

**Email retrieval process:**
```
Mail Server (stores email)
    ↓ POP3 download
Client Device (email stored locally)
```

#### POP3 Commands

**Connection:**
```bash
telnet mail.example.com 110
```

**Authentication:**
```pop3
USER username
PASS password
```

**Email operations:**
```pop3
STAT                # Get mailbox statistics
LIST                # List messages
RETR 1              # Retrieve message 1
DELE 1              # Delete message 1
QUIT                # Close and commit changes
```

#### Characteristics

**Advantages:**
- Simple protocol
- Emails stored locally (offline access)
- Less server storage used

**Disadvantages:**
- Email deleted from server after download
- No synchronization across devices
- No folder management on server

---

### IMAP (Internet Message Access Protocol)

#### What it does
Accesses and manages email on the server with synchronization across multiple devices.

**Default Port:** 143 (unencrypted)
**Secure Port:** 993 (IMAPS - SSL/TLS)

**Transport Protocol:** TCP

#### How IMAP Works

**Email synchronization:**
```
Mail Server (stores all email)
    ↑↓ IMAP sync
Client Device 1 (mirrors server)
    ↑↓ IMAP sync
Client Device 2 (mirrors server)
```

#### IMAP vs POP3

| Feature | POP3 | IMAP |
|---------|------|------|
| Email location | Downloaded locally | Stays on server |
| Multi-device | No sync | Full sync |
| Folders | Local only | Server-side |
| Offline access | Yes | Partial (cached) |
| Server storage | Low usage | High usage |

#### IMAP Features

**Advanced capabilities:**
- Multiple folder management
- Server-side search
- Partial message download
- Flag messages (read/unread)
- Shared mailboxes

#### Security Relevance

**Email Protocol Security:**
- POP3/IMAP credentials sent in plaintext (ports 110/143)
- Always use secure versions (995/993)
- Email account compromise common attack vector

**SOC Detection:**
- Unusual login locations (geo-IP analysis)
- Multiple failed authentication attempts
- Large email downloads (data exfiltration)
- Email forwarding rule creation
- IMAP on non-standard ports

---

## What I Learned

### Technical Skills Developed

**1. DNS Investigation:**
- Query different record types for reconnaissance
- Use whois for domain ownership research
- Understand DNS resolution process

**2. HTTP Protocol Mastery:**
- Manually craft HTTP requests
- Understand request/response structure
- Recognize different HTTP methods and their purposes

**3. File Transfer Operations:**
- Navigate FTP servers via command line
- Upload/download files programmatically
- Understand FTP security limitations

**4. Email Protocol Understanding:**
- Differentiate between SMTP, POP3, and IMAP
- Understand email flow from sender to recipient
- Recognize email-based attack vectors

### Security Perspective

**How attackers use these protocols:**
1. **DNS:** Enumeration, subdomain discovery, DNS tunneling
2. **HTTP:** Parameter tampering, injection attacks, unauthorized methods
3. **FTP:** Credential theft, data exfiltration, malware distribution
4. **Email:** Phishing, spoofing, account compromise, malware delivery

**How defenders (SOC analysts) use protocol knowledge:**
1. **Traffic analysis:** Identify unusual protocol usage
2. **Threat hunting:** Detect malicious DNS queries, suspicious HTTP requests
3. **Incident response:** Investigate email compromise, FTP data theft
4. **Forensics:** Reconstruct attacker actions from protocol logs

---

## Practical Applications

### Scenario 1: Investigating Phishing Email
```bash
# Check email headers
# Verify SPF/DKIM/DMARC

# Investigate sender domain
nslookup -type=MX suspicious-domain.com
whois suspicious-domain.com

# Check for recently registered domain (red flag)
# Verify actual mail server IP matches MX record
```

### Scenario 2: Web Application Reconnaissance
```bash
# Manual HTTP request to analyze response
telnet target.com 80
GET / HTTP/1.1
Host: target.com

# Check for information disclosure in headers
# Identify web server version
# Test for available HTTP methods
```

### Scenario 3: DNS Analysis
```bash
# Enumerate DNS records
nslookup target.com
nslookup -type=MX target.com
nslookup -type=TXT target.com

# Check for subdomains in DNS
# Identify mail infrastructure
# Look for misconfigurations in TXT records
```

### Scenario 4: FTP Security Assessment
```bash
# Test for anonymous FTP access
ftp target-server.com
Name: anonymous
Password: 

# Check for sensitive files
ls -la
get config.txt

# Document findings for remediation
```

---

## Connection to Previous Learning

### From Networking Essentials:
- **TCP/IP:** All these protocols use TCP as transport
- **Ports:** Understanding port numbers critical for service identification
- **Packet analysis:** Protocols build on networking fundamentals

### From Command Line:
- **telnet:** Practical application for protocol testing
- **Terminal proficiency:** Essential for FTP, DNS queries
- **Command syntax:** Transferred to protocol-specific commands

---

## Key Takeaways

**Protocol Security Hierarchy:**
- Unencrypted protocols (HTTP, FTP, SMTP-25, POP3-110, IMAP-143) = security risk
- Always prefer encrypted versions (HTTPS, SFTP, SMTP-587, POP3S, IMAPS)
- Understanding plaintext protocols helps identify when security is compromised

**SOC Analyst Perspective:**
- Every protocol generates logs that tell a story
- Unusual protocol behavior indicates potential compromise
- Protocol knowledge enables faster incident triage and investigation

**Hands-On Command Line:**
- Theory is not enough — actually using telnet, ftp, nslookup made concepts real
- Manual protocol interaction reveals how automation tools work underneath
- Command-line proficiency speeds up security investigations

---

## Tools Summary

| Tool | Purpose | Example Usage |
|------|---------|---------------|
| nslookup | DNS queries | `nslookup -type=MX example.com` |
| whois | Domain registration info | `whois example.com` |
| telnet | Manual protocol testing | `telnet example.com 80` |
| ftp | File transfer | `ftp server-ip` |
| dig | Advanced DNS queries | `dig example.com ANY` |

---

## Next Steps

### Immediate Practice:
1. Investigate domains using nslookup/whois daily
2. Practice manual HTTP requests with telnet
3. Set up personal FTP server for testing
4. Analyze email headers from inbox

### Security-Specific Learning:
1. Study protocol-based attacks in depth
2. Learn Wireshark for protocol analysis
3. Practice email header analysis
4. Research DNS security (DNSSEC)

### Career Development:
1. Build protocol reference cheat sheet
2. Document protocol-based IOCs (Indicators of Compromise)
3. Practice incident scenarios involving these protocols
4. Learn SIEM rules for protocol anomaly detection

---

## SOC Analyst Relevance

### Why Protocol Knowledge Matters:

**Daily Operations:**
- Analyzing suspicious DNS queries in SIEM
- Investigating HTTP-based attacks (SQL injection, XSS)
- Detecting FTP data exfiltration
- Triaging phishing emails via header analysis

**Incident Response:**
```
Alert: Suspicious DNS query volume

Investigation:
1. nslookup to verify domain legitimacy
2. whois to check registration date
3. Check for DNS tunneling patterns
4. Correlate with HTTP/HTTPS traffic
```

**Threat Hunting:**
- Hunt for DNS queries to newly registered domains
- Identify unusual HTTP user agents
- Detect cleartext protocol usage (FTP, HTTP, Telnet)
- Find email account compromise indicators

---

## Real-World Impact

Understanding these protocols at the command-line level changed my perspective entirely. Before, I knew these services existed. Now I understand:

- How they communicate
- Where they're vulnerable
- How attackers exploit them
- How to investigate incidents involving them

For SOC work, this knowledge is foundational. Every alert, every investigation, every security control involves at least one of these protocols. Understanding them deeply means faster triage, better detection, and more effective response.

**Key insight:** Security tools (SIEM, IDS, firewalls) monitor these protocols. Understanding what they're monitoring and why makes me a better analyst.

---

## Resources
- TryHackMe - Networking Core Protocols Room
- RFC documents (protocol specifications)
- Wireshark for protocol analysis
- Linux/Windows command-line tools
