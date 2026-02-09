# Active Directory Basics - TryHackMe

## Date Completed:08/02/2026

## Module Overview
This module covered Windows Active Directory fundamentals, exploring how enterprise networks manage users, computers, and security policies at scale through centralized domain services.

## Learning Objectives
- Understand Active Directory architecture and components
- Learn domain controller functionality and management
- Explore user and computer account administration
- Study Group Policy implementation
- Analyze authentication methods (Kerberos vs NTLM)
- Understand organizational structures (Forests, Trees, Trusts)

---

## Key Concepts Learned

### What is Active Directory?
Active Directory (AD) is Microsoft's centralized directory service that manages users, computers, and resources in Windows enterprise environments. Instead of managing accounts on each individual computer, AD provides a single source of truth for an entire organization.

**Real-world application:** A company with 10,000 employees and 15,000 computers can manage all access, permissions, and policies from one central location.

### Domain Controllers (DC)
The servers that run Active Directory Domain Services (AD DS) and manage all domain operations.

**Key functions:**
- Store user credentials and account information
- Authenticate users when they log in
- Enforce security policies
- Replicate directory data across multiple DCs for redundancy

**Security relevance:** Compromising a Domain Controller gives an attacker control over the entire domain. This is why DCs are prime targets in cyber attacks.

### Active Directory Users and Computers
The administrative tool for managing domain objects (users, computers, groups).

**What I learned:**
- Creating and managing user accounts
- Organizing objects into Organizational Units (OUs)
- Assigning permissions and group memberships
- Managing computer accounts in the domain

**Practical skill:** Understanding user/computer management is essential for SOC analysts investigating compromised accounts or unusual access patterns.

### Group Policy
Centralized configuration management that allows administrators to enforce settings across domain computers.

**Examples of Group Policies:**
- Password requirements (complexity, length, expiration)
- Software installation/restrictions
- Security settings (firewall rules, audit policies)
- Desktop configurations

**Why this matters:** Misconfigurations in Group Policy can create security vulnerabilities. Attackers exploit weak policies (like disabled antivirus or weak passwords) to maintain persistence.

### Authentication Methods

#### Kerberos (Modern, Secure)
- Ticket-based authentication system
- Uses encryption
- Default authentication protocol in modern AD environments
- More secure than NTLM

**How it works (simplified):**
1. User requests ticket from Key Distribution Center (KDC)
2. KDC verifies credentials and issues Ticket Granting Ticket (TGT)
3. User presents TGT to access resources
4. Service-specific tickets are granted

**Attack vector:** Kerberoasting attacks target service accounts to extract password hashes.

#### NTLM (Older, Legacy)
- Challenge-response authentication
- Still used for backward compatibility
- Less secure than Kerberos
- Vulnerable to pass-the-hash attacks

**Security implication:** Many attacks target NTLM because it's weaker. Understanding both protocols helps detect authentication-based attacks.

### Forests, Trees, and Trusts

#### Domain Tree
A hierarchy of domains sharing a contiguous namespace.

**Example:**
```
company.com (root)
├── sales.company.com
├── hr.company.com
└── it.company.com
```

#### Forest
A collection of one or more domain trees that share a common schema and global catalog.

**Think of it as:** The forest is the security boundary. Everything inside trusts each other by default.

#### Trusts
Relationships between domains or forests that allow users in one domain to access resources in another.

**Types:**
- **One-way trust:** Domain A trusts Domain B (but not vice versa)
- **Two-way trust:** Mutual trust
- **Transitive trust:** If A trusts B, and B trusts C, then A trusts C
- **Non-transitive trust:** Trust doesn't extend beyond the direct relationship

**Security concern:** Trusts can be exploited for lateral movement. Attackers compromise one domain and pivot through trust relationships to reach valuable targets.

---

## What I Found Challenging

**Forests and Trusts:**
- The relationship between forests, trees, and domains was abstract at first
- Understanding transitive vs non-transitive trusts required multiple examples
- Visualizing how trust relationships enable access took time

**Authentication Flow:**
- Kerberos ticket-granting process has many steps
- Distinguishing when NTLM is used vs Kerberos
- Understanding the security implications of each protocol

**Group Policy Scope:**
- When policies apply to users vs computers
- Order of policy application (local, site, domain, OU)
- Precedence and inheritance rules

---

## What Made Sense

**Domain Controllers as Central Authority:**
- The concept of centralized management clicked immediately
- Understood why enterprises need this at scale
- Clear parallel to how schools/universities manage student accounts

**Group Policy Value:**
- Obvious benefits of setting rules once vs configuring 1000s of computers individually
- Recognized how this prevents users from installing malware or changing security settings

**Why AD is Targeted:**
- Makes perfect sense why attackers focus on AD
- Compromise one domain admin = control everything
- Lateral movement through trust relationships is a clear attack path

---

## SOC Analyst Relevance

### Why This Matters for Security Operations:

**1. Attack Detection:**
Understanding AD helps identify suspicious activity:
- Unusual Kerberos ticket requests (possible Kerberoasting)
- NTLM authentication from unexpected locations
- Failed login attempts targeting admin accounts
- Lateral movement between domains

**2. Incident Investigation:**
When investigating incidents, SOC analysts need to:
- Trace user activity across domain resources
- Identify compromised accounts through AD logs
- Understand privilege escalation paths
- Recognize abnormal Group Policy changes

**3. Threat Hunting:**
- Monitor for Golden Ticket attacks (forged Kerberos tickets)
- Detect DCSync attacks (unauthorized domain replication)
- Identify pass-the-hash attempts using NTLM
- Hunt for anomalous trust relationship exploitation

**4. Common Attack Patterns:**
- **Kerberoasting:** Extracting service account credentials
- **Pass-the-Hash:** Using NTLM hashes without cracking passwords
- **Golden Ticket:** Forging Kerberos TGTs to impersonate any user
- **Silver Ticket:** Forging service tickets for specific resources
- **DCSync:** Replicating AD data to extract credentials

---

## Practical Applications

### How I'll Use This Knowledge:

**As a future SOC Analyst:**
1. **Log Analysis:** Identify suspicious Windows Event IDs related to authentication failures, account changes, or privilege escalation
2. **Alert Triage:** Understand context when security tools flag Kerberos anomalies or unusual domain controller access
3. **Threat Detection:** Recognize attack patterns targeting AD infrastructure
4. **Incident Response:** Investigate account compromises by tracing activity through AD logs

**Building on This:**
- Next: Learn how attackers exploit AD (offensive perspective)
- Future: Practice AD attack detection in lab environment
- Goal: Become proficient at defending AD in enterprise SOC operations

---

## Connection to Previous Learning

### From TryHackMe Pre Security:
- **Windows Fundamentals:** Built foundation for understanding Windows architecture
- **Networking:** AD relies on DNS, DHCP, and network protocols I already learned
- **Authentication concepts:** Extended my knowledge of how systems verify identity

### How This Fits Career Path:
- **Short-term:** Understanding AD prepares me for SOC analyst roles in enterprise environments
- **Long-term:** Knowing both how AD works (defensive) and how it's attacked (offensive) makes me more valuable when transitioning to penetration testing

---

## Resources & References

- TryHackMe Active Directory Basics Room
- Microsoft Active Directory Documentation
- MITRE ATT&CK Framework (AD-related techniques)

---

## Next Steps

1. **Continue offensive security learning:** Understand how attackers exploit AD
2. **Practice in labs:** Set up Windows Server AD environment for hands-on practice (future project)
3. **Learn detection:** Study Windows Event logs and SIEM rules for AD monitoring
4. **Explore attack techniques:** Research Kerberoasting, Pass-the-Hash, Golden Tickets in detail

---

## Reflection

While I didn't fully grasp every detail (especially the complexity of trusts and forests), I understand the core concepts and why Active Directory is critical in enterprise security. The connection between AD management and security operations is clear - attackers target it because it's the keys to the kingdom, and defenders must monitor it continuously.

This knowledge directly supports my goal of becoming a SOC analyst, as most enterprise environments I'll work in will rely heavily on Active Directory for identity and access management.
