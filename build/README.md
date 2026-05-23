# Kali Linux Home Lab — AegisPro CyberShield TX

> A personal cybersecurity practice environment built to develop and maintain hands-on offensive and defensive security skills, support vulnerability assessment engagements, and document practical experience aligned with CISSP domains.

---

## Objective

This home lab serves as the technical foundation for AegisPro CyberShield TX's security assessment practice. It provides an isolated, controlled environment for:

- Conducting vulnerability assessments using industry-standard tools
- Practicing penetration testing techniques against intentionally vulnerable targets
- Developing and refining client-facing reporting workflows
- Maintaining active, demonstrable cybersecurity skills

> **Disclaimer:** All testing conducted in this lab is performed exclusively against owned, intentionally vulnerable machines on an isolated private network. No unauthorized systems are ever targeted.

---

## Lab Architecture

### Network Topology

```
┌────────────────────────────────────────────────────────────────────┐
│                       VMware Host Machine                          │
│                                                                    │
│   ┌─────────────┐         ┌──────────────────────────────┐         │
│   │  Kali Linux │         │         pfSense VM           │         │
│   │  (Attacker) │◄───────►│   Router / Firewall / Switch │         │
│   │  10.0.1.x   │         │   WAN: 192.168.x.1 (NAT)     │         │
│   └─────────────┘         │   LAN: 10.0.1.1              │         │
│                           └──────────────┬───────────────┘         │
│                                          │                          │
│        ┌─────────────────────────────────┼─────────────────────┐   │
│        │                                 │                     │   │
│   ┌────▼─────┐  ┌──────────┐  ┌──────────▼──────────────┐  ┌──▼──┐ │
│   │  Linux   │  │   Web    │  │   AD Domain (corp.      │  │DVWA │ │
│   │  Targets │  │  Targets │  │   aegispro.lab)         │  │ VM  │ │
│   ├──────────┤  ├──────────┤  │                         │  └─────┘ │
│   │Meta-     │  │ DVWA VM  │  │  ┌──────────────┐       │          │
│   │sploitable│  │          │  │  │ DC01 (Server │       │          │
│   │    2     │  │          │  │  │     2019)    │       │          │
│   └──────────┘  └──────────┘  │  └──────────────┘       │          │
│   ┌──────────┐                │  ┌────┐ ┌────┐ ┌────┐   │          │
│   │ VulnHub  │                │  │WS01│ │WS02│ │WS03│   │          │
│   │ Machines │                │  │Win10│ │Win10│ │Win10│  │          │
│   └──────────┘                │  └────┘ └────┘ └────┘   │          │
│                                └─────────────────────────┘          │
└────────────────────────────────────────────────────────────────────┘
```

### Network Segments

| Segment | Interface | Subnet | Purpose |
|---------|-----------|--------|---------|
| WAN | pfSense WAN | 192.168.x.0/24 | Simulated external network / NAT to host |
| LAN | pfSense LAN | 10.0.1.0/24 | Internal lab network — all target VMs |
| Attacker | Kali Linux | 10.0.1.x | Connects to LAN segment for testing |

### Isolation Design

Traffic in this lab is controlled and segmented by pfSense, which acts as the single choke point between network segments. This design mirrors a real-world enterprise network where:

- All inbound and outbound traffic passes through the firewall
- Firewall rules can be created, tested, and validated against live attack traffic
- Kali Linux attack traffic is observable and blockable at the pfSense layer
- Internet access for target VMs can be selectively enabled or disabled per engagement scenario

---

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VMware Workstation / VMware Player |
| Attacker OS | Kali Linux 2024.x (Rolling Release) |
| Firewall / Router | pfSense — network segmentation, firewall rules, DHCP, DNS |
| Network Architecture | pfSense-routed LAN (10.0.1.0/24) with simulated WAN segment |
| Domain Controller | Windows Server 2019 (DC01) — `corp.aegispro.lab` domain |
| Domain Workstations | 3x Windows 10 Pro (WS01, WS02, WS03) — domain-joined |
| AD Configuration | Simulated user accounts, OU structure, group policies |
| Linux Target | Metasploitable 2 — intentionally vulnerable Linux VM |
| Web App Target | DVWA (Damn Vulnerable Web App) |
| Additional Targets | VulnHub machines — rotating CTF-style vulnerable VMs |

---



---

## Active Directory Environment

A dedicated Windows Active Directory domain has been built within the lab to support enterprise-realistic security assessment, attack simulation, and defensive practice scenarios.

### Domain Configuration

| Component | Details |
|-----------|---------|
| Domain Name | `corp.aegispro.lab` |
| Domain Controller | DC01 — Windows Server 2019 |
| Forest Functional Level | Windows Server 2016 / 2019 |
| DNS | Integrated AD DNS on DC01 |
| Member Workstations | WS01, WS02, WS03 (Windows 10 Pro) |
| User Accounts | Simulated user population across multiple OUs |
| Group Policy | Baseline GPOs configured for testing |

### Why an AD Domain in the Lab

A working Active Directory environment provides realistic conditions for practicing the attacks, enumeration, and defensive controls that dominate real-world enterprise security work. Most small and mid-sized businesses run domain-joined Windows environments — meaning AD assessment skills directly apply to the kind of consulting engagements AegisPro CyberShield TX performs.

This environment supports practice across:

- Active Directory enumeration (BloodHound, PowerView, ldapsearch)
- Kerberos attacks (Kerberoasting, AS-REP Roasting)
- Credential access and lateral movement (Pass-the-Hash, Pass-the-Ticket)
- Network credential capture (LLMNR/NBT-NS poisoning, SMB relay)
- Group Policy enumeration and abuse
- Domain privilege escalation paths
- AD hardening recommendations and detection engineering

---

## Tools & Capabilities

### Vulnerability Assessment
| Tool | Purpose | Usage |
|------|---------|-------|
| **OpenVAS / GVM** | Network vulnerability scanner | Authenticated and unauthenticated scans, CVSS scoring, client report generation |
| **Nmap** | Network discovery & port scanning | Host discovery, service enumeration, OS fingerprinting |
| **Nikto** | Web server scanner | Identifies misconfigurations, outdated software, and known web vulnerabilities |

### Exploitation & Testing
| Tool | Purpose | Usage |
|------|---------|-------|
| **Metasploit Framework** | Exploitation framework | Validating vulnerabilities found during scanning, post-exploitation practice |
| **Burp Suite** | Web application security testing | Intercepting HTTP/S traffic, identifying injection flaws, web app vulnerability testing |

### Active Directory Tools (planned)
| Tool | Purpose | Usage |
|------|---------|-------|
| **BloodHound / SharpHound** | AD relationship visualization | Mapping attack paths from any user to Domain Admin, identifying privilege escalation routes |
| **CrackMapExec** | AD post-exploitation Swiss Army knife | Enumeration, credential validation, lateral movement, share enumeration |
| **Impacket suite** | Python AD attack toolkit | Kerberoasting (`GetUserSPNs.py`), AS-REP Roasting (`GetNPUsers.py`), Pass-the-Hash, DCSync |
| **Responder** | Network credential harvester | LLMNR/NBT-NS/mDNS poisoning to capture NTLM hashes |
| **Hashcat** | Password cracking | Offline cracking of captured Kerberos tickets and NTLM hashes |
| **Mimikatz / Rubeus** | Windows credential dumping & Kerberos manipulation | LSASS dumping, ticket extraction, Pass-the-Ticket attacks |

### Network Infrastructure
| Tool | Purpose | Usage |
|------|---------|-------|
| **pfSense** | Router / Firewall / Switch | Network segmentation, firewall rule creation and testing, DHCP/DNS management, traffic logging, IDS/IPS integration |

### Analysis & Monitoring
| Tool | Purpose | Usage |
|------|---------|-------|
| **Wireshark** | Packet capture & analysis | Network traffic analysis, protocol inspection, detecting anomalous behavior |

---

## Lab Workflows

### Vulnerability Assessment Workflow

```
1. Scope Definition
   └── Define target IP range, assessment type (authenticated/unauthenticated)

2. Host Discovery
   └── nmap -sn 192.168.x.0/24

3. Port & Service Enumeration
   └── nmap -sV -sC -O <target IP>

4. Vulnerability Scanning
   └── OpenVAS authenticated scan against target
   └── Nikto web server scan (if web services present)

5. Manual Validation
   └── Verify scanner findings manually
   └── Metasploit — confirm exploitability where appropriate

6. Reporting
   └── Document findings with CVE references and CVSS scores
   └── Write remediation recommendations
   └── Generate client-ready report
```

### Web Application Testing Workflow (DVWA)

```
1. Configure DVWA security level (Low / Medium / High)
2. Proxy traffic through Burp Suite
3. Test OWASP Top 10 categories:
   ├── SQL Injection
   ├── Cross-Site Scripting (XSS)
   ├── Command Injection
   ├── File Inclusion
   └── CSRF
4. Document findings and payloads used
5. Research remediation for each vulnerability class
```

---

## Target Machines

### Metasploitable 2
A deliberately insecure Linux VM designed for practicing penetration testing. Contains numerous unpatched services and misconfigurations across multiple vulnerability classes.

**Key vulnerability categories present:**
- Outdated and unpatched services (FTP, SSH, Telnet, HTTP)
- Weak credentials and default passwords
- Misconfigured network services
- Known CVEs across multiple service versions

**Primary use:** Full vulnerability assessment practice, OpenVAS scan validation, Metasploit exploitation practice.

---

### DVWA (Damn Vulnerable Web App)
A PHP/MySQL web application designed to practice common web vulnerabilities at adjustable difficulty levels.

**Vulnerability classes covered:**
- SQL Injection (classic and blind)
- Reflected and Stored XSS
- Command Injection
- File Upload bypass
- Insecure CAPTCHA
- CSRF

**Primary use:** Burp Suite interception practice, web application security testing, OWASP Top 10 skill development.

---

### Windows VM
A Windows endpoint used to simulate real-world enterprise targets. Useful for practicing:
- Windows-specific enumeration and exploitation
- Active Directory concepts (if domain configured)
- Wireshark traffic analysis against Windows services
- Antivirus and EDR evasion concepts

---



### Active Directory Domain (`corp.aegispro.lab`)

**DC01 — Domain Controller (Windows Server 2019)**
- Hosts AD DS, DNS, and Kerberos for the `corp.aegispro.lab` domain
- Holds all FSMO roles
- Stores user accounts, computer accounts, group memberships, and GPOs
- Primary target for enumeration and high-value attacks (DCSync, Kerberoasting, NTDS.dit dumping)

**WS01, WS02, WS03 — Domain-Joined Workstations (Windows 10 Pro)**
- Standard domain user logon experience for simulated users
- Used to practice initial access, credential dumping, and lateral movement
- Targets for client-side attack scenarios and Group Policy enumeration

**Simulated Users and Groups**
- Multiple test user accounts across organizational units
- Group memberships designed to allow practice with privilege escalation paths
- Service accounts configured to enable SPN/Kerberoasting practice

**Primary use:** Active Directory attack simulation, lateral movement practice, credential access scenarios, and AD defensive control validation.

### VulnHub Machines
Rotating selection of CTF-style vulnerable VMs from [VulnHub](https://www.vulnhub.com). Each machine is documented in a separate write-up within this repository.

**Completed machines:** *(see `/vulnhub-writeups/` folder)*

---

### Firewall Rule Testing Workflow (pfSense)

```
1. Baseline configuration
   └── Set up pfSense LAN/WAN interfaces, DHCP, and DNS resolver

2. Define test scenario
   └── Example: block ICMP from Kali to target, allow only TCP/443

3. Execute attack/test traffic from Kali
   └── nmap, Metasploit, or custom traffic toward target

4. Observe pfSense firewall logs
   └── Confirm blocked/allowed traffic matches rule intent

5. Iterate rules
   └── Tighten or loosen rules, re-test, document behavior

6. Document outcome
   └── Record rule logic, traffic behavior, and security takeaways
```

### Active Directory Attack Workflow

```
1. Reconnaissance
   └── Network discovery of domain hosts
   └── Identify domain controllers via DNS / SMB / LDAP

2. Unauthenticated enumeration
   └── ldapsearch, enum4linux-ng for anonymous LDAP/SMB info
   └── Responder for LLMNR/NBT-NS poisoning to capture NTLM hashes

3. Credential acquisition
   └── Crack captured hashes with Hashcat
   └── Or use spray attacks against known username lists

4. Authenticated enumeration
   └── BloodHound (SharpHound collector) — map attack paths
   └── CrackMapExec for share/session/local-admin enumeration

5. Kerberos attacks
   └── Kerberoasting — extract & crack service account tickets
   └── AS-REP Roasting — target accounts with pre-auth disabled

6. Lateral movement
   └── Pass-the-Hash, Pass-the-Ticket, WinRM, PsExec, WMI

7. Privilege escalation to Domain Admin
   └── Walk the BloodHound path
   └── Abuse misconfigured ACLs, GPOs, or Kerberos delegation

8. Document findings & remediation
   └── Map every attack to a defensive control
   └── Detection engineering: what should the SOC have caught?
```

## Repository Structure

```
home-lab-setup/
├── README.md                   ← this file
├── images/
│   ├── network-topology.png
│   ├── openvas-dashboard.png
│   ├── vmware-setup.png
│   ├── pfsense-dashboard.png
│   └── ad-domain-overview.png
├── vulnhub-writeups/
│   └── [machine-name]/
│       ├── README.md
│       └── images/
├── assessments/
│   ├── metasploitable-2/
│   │   ├── README.md
│   │   └── images/
│   └── active-directory/
│       ├── README.md
│       ├── kerberoasting/
│       ├── bloodhound-enumeration/
│       ├── lateral-movement/
│       └── ad-hardening-recommendations/
└── configs/
    ├── openvas-scan-config-notes.md
    ├── pfsense-firewall-rules-notes.md
    └── active-directory-build-notes.md
```

---

## Skills Demonstrated

| Skill Area | Tools / Evidence |
|------------|-----------------|
| Vulnerability scanning & analysis | OpenVAS, Nmap, Nikto |
| Web application security testing | Burp Suite, DVWA |
| Exploitation & post-exploitation | Metasploit, Metasploitable 2 |
| Network traffic analysis | Wireshark |
| Security reporting & documentation | Assessment write-ups, client-style reports |
| Network architecture & segmentation | pfSense firewall, routing, DHCP/DNS |
| Active Directory administration | Server 2019 DC, domain configuration, OU & GPO design |
| AD security assessment (planned) | BloodHound, CrackMapExec, Impacket, Responder |
| Windows enterprise threat modeling | Domain-joined attack scenarios, lateral movement paths |
| Firewall rule design & testing | pfSense + Kali attack traffic validation |
| Lab design & network isolation | VMware + pfSense multi-segment networking |

---

## CISSP Domain Alignment

This lab environment directly supports the following CISSP domains:

| Domain | Relevant Activities |
|--------|-------------------|
| Domain 1 — Security & Risk Management | Risk identification, vulnerability prioritization |
| Domain 4 — Communication & Network Security | pfSense network segmentation, firewall rule design, traffic analysis, routing |
| Domain 5 — Identity & Access Management | AD user/group/role design, Kerberos, GPO-based access controls |
| Domain 6 — Security Assessment & Testing | Vulnerability scanning, penetration testing, AD attack simulation, reporting |
| Domain 7 — Security Operations | Incident detection, log analysis, tool operation |

---

## About AegisPro CyberShield TX

AegisPro CyberShield TX is an independent cybersecurity consulting practice based in Fort Worth, Texas, providing vulnerability assessment and security consulting services to small and medium-sized businesses.

- **Certifications:** CISSP
- **Focus areas:** Vulnerability assessment, security consulting, SMB security
- **Contact:** [LinkedIn](https://linkedin.com) | [Website](https://aegisprocybershield.com)

---

*Last updated: May 2026*
