# Network Discovery - Phase 2

## Date: 07/02/2026

## Objective
Discover what services are running on Metasploitable using port scanning - applying networking fundamentals from TryHackMe Pre Security path.

## Pre Security Concepts Applied
✅ **TCP/IP Model** - Understanding Transport Layer (ports and protocols)
✅ **Common Protocols** - HTTP, FTP, SSH, Telnet, SMTP, DNS
✅ **Port Numbers** - Well-known ports (0-1023) for standard services
✅ **OSI Model** - Layer 4 (Transport) port identification
✅ **Linux Command Line** - Running network tools from terminal

## Target Information
- **IP Address:** 192.168.56.103
- **Operating System:** Linux (Ubuntu)

---

## Scanning Methodology

### Scan 1: Basic Port Scan

**Command Used:**
```bash
nmap 192.168.56.103
```

**What This Command Does:**
- Scans the top 1000 most common TCP ports
- Checks if each port is OPEN, CLOSED, or FILTERED
- Uses TCP SYN packets (from TCP three-way handshake - Pre Security!)

**Results:**
Found 23 open ports

**Screenshot:**
<img width="827" height="396" alt="Screenshot 2026-02-07 120359" src="https://github.com/user-attachments/assets/86ba7ca5-0f5c-4cc8-8296-4f2b3f5f811b" />


**Explanation:**
This scan uses the TCP/IP model knowledge from Pre Security. Nmap sends TCP SYN packets to each port to check if a service is listening. If it gets a SYN-ACK back, the port is OPEN.

---

### Scan 2: Service Version Detection

**Command Used:**
```bash
nmap -sV 192.168.56.103
```

**What This Command Does:**
- `-sV` flag enables service/version detection
- Connects to each open port
- Identifies the specific service and version running
- More detailed than basic scan

**Screenshot:**
<img width="1029" height="576" alt="Screenshot 2026-02-07 120621" src="https://github.com/user-attachments/assets/4ccc1d3b-f4a3-4905-b9f8-5aa70ed959b6" />


---

## Findings

### Open Ports and Services Identified

21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login       OpenBSD or Solaris rlogind
514/tcp  open  shell       Netkit rshd
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1


---

## Analysis & Understanding

### Web Services
**Port 80 (HTTP):**
- Running a web server (Apache)
- This is how websites are served (from Pre Security web fundamentals)
- Could host web applications

### Remote Access Services
**Port 22 (SSH):**
- ✅ Secure - uses encryption
- Modern way to remotely access Linux systems

**Port 23 (Telnet):**
- ⚠️ INSECURE - no encryption!
- Sends usernames and passwords in plain text
- Should not be used (replaced by SSH)

### File Transfer
**Port 21 (FTP):**
- File Transfer Protocol
- Used to upload/download files
- May allow anonymous access (need to test)

### Email
**Port 25 (SMTP):**
- Mail server for sending emails
- Part of email infrastructure

---

## Security Observations

### From Pre Security Knowledge:

1. **Telnet is a Security Risk:**
   - Learned in Pre Security that unencrypted protocols are dangerous
   - Port 23 (Telnet) sends everything in plain text
   - Anyone on the network can see passwords

2. **Multiple Services = Larger Attack Surface:**
   - Each open port is a potential entry point
   - More services = more things that could be vulnerable

3. **Well-Known Ports:**
   - All these ports (21, 22, 23, 25, 80) are "well-known" ports
   - Standardized by IANA (learned in Pre Security)
   - Ports 0-1023 require root/admin privileges

---

## What I Learned

### Practical Application of Pre Security:
- **Port scanning** uses TCP three-way handshake knowledge
- **Service identification** applies protocol understanding
- **Network reconnaissance** is about discovery, not exploitation
- **Command line proficiency** from Linux Fundamentals modules

### Technical Skills:
- Used Nmap for network scanning
- Interpreted scan results
- Identified services by port number
- Connected theoretical knowledge to practical results

### Commands Mastered:
```bash
nmap [target]              # Basic port scan
nmap -sV [target]          # Service version detection
```

---

## Connection to Pre Security Path

This phase directly applies concepts from:
- ✅ **Intro to Networking** - Understanding ports and services
- ✅ **TCP/IP Model** - Transport Layer (ports) and protocols
- ✅ **OSI Model** - Layer 4 (Transport Layer)
- ✅ **Common Protocols** - HTTP, FTP, SSH, Telnet, SMTP
- ✅ **Linux Fundamentals** - Command line usage

---

## Next Steps - Phase 3

Now that I know what services are running, Phase 3 will:
1. Test FTP for anonymous access (security testing)
2. Explore the web server on port 80
3. Check for default credentials
4. Document any vulnerabilities found

This continues applying Pre Security fundamentals to hands-on security analysis.
