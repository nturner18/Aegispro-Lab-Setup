# Kali Linux Home Lab — AegisPro CyberShield TX

> A personal cybersecurity practice environment built to develop and maintain hands-on offensive and defensive security skills, support vulnerability assessment engagements, and document practical experience aligned with CISSP domains.

---

## Objective

This home lab serves as the technical foundation for AegisPro CyberShield TX's security assessment practice. It provides an isolated, controlled environment for:

- Conducting vulnerability assessments using industry-standard tools
- Practicing penetration testing techniques against intentionally vulnerable targets
- Developing and refining client-facing reporting workflows
- Maintaining active, demonstrable cybersecurity skills

> **Disclaimer:** All testing conducted in this lab is performed exclusively against owned, intentionally vulnerable machines on an isolated private network. No unauthorized systems are ever targeted. The AcrossStatesBank Active Directory environment is entirely fictional.

---

## Lab Architecture

### Network Topology

```
┌────────────────────────────────────────────────────────────────────┐
│                       VMware Host Machine                          │
│                                                                    │
│   ┌─────────────┐         ┌──────────────────────────────┐        │
│   │  Kali Linux │         │         pfSense VM           │        │
│   │  (Attacker) │◄───────►│   Router / Firewall / Switch │        │
│   │  10.0.1.x   │         │   WAN: VMnet8 (NAT)          │        │
│   └─────────────┘         │   LAN: 10.0.1.1              │        │
│                           └──────────────┬───────────────┘        │
│                                          │                         │
│     ┌────────────────────────────────────┴──────────────────┐     │
│     │                  Lab LAN (10.0.1.0/24)                │     │
│     │                                                        │     │
│  ASBankDC1 (10.0.1.201)   ASB-CB-WS01   ASB-PB-WS02        │     │
│  Metasploitable 2         DVWA VM        VulnHub Targets    │     │
│     └────────────────────────────────────────────────────── ┘     │
└────────────────────────────────────────────────────────────────────┘
```

### Network Segments

| Segment | Interface | Subnet | Purpose |
|---------|-----------|--------|---------|
| WAN | pfSense WAN (VMnet8) | NAT via VMware | Simulated external / internet access |
| LAN | pfSense LAN (VMnet2) | 10.0.1.0/24 | Internal lab network — all target VMs |
| Attacker | Kali Linux | 10.0.1.x | Attack and assessment platform |

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
| Hypervisor | VMware Workstation Pro 25H2 (25.0.0.24995812) |
| Attacker OS | Kali Linux 2025.2 (Rolling Release) |
| Firewall / Router | pfSense 2.8.1 — network segmentation, firewall rules, DHCP, DNS |
| Network Architecture | pfSense-routed LAN (10.0.1.0/24) with simulated WAN segment |
| Domain Controller | ASBankDC1 — Windows Server 2019 (10.0.1.201) — `ASBank.com` domain |
| Domain Workstations | ASB-CB-WS01, ASB-PB-WS02 (Windows 10 Pro) — domain-joined |
| AD Configuration | 15 simulated users, OU structure, security groups, GPOs |
| Linux Target | Metasploitable 2 — intentionally vulnerable Linux VM |
| Web App Target | DVWA (Damn Vulnerable Web App) |
| Additional Targets | VulnHub machines — rotating CTF-style vulnerable VMs |

---

## Active Directory Environment

A dedicated Windows Active Directory domain has been built within the lab to support enterprise-realistic security assessment, attack simulation, and defensive practice scenarios.

### Domain Configuration

| Component | Details |
|-----------|---------|
| Domain Name | `ASBank.com` |
| Domain FQDN | `ASBankDC1.ASBank.com` |
| Domain Controller | ASBankDC1 — Windows Server 2019 (10.0.1.201) |
| Forest Functional Level | Windows Server 2016 |
| DNS | Active Directory-integrated DNS on ASBankDC1 |
| Member Workstations | ASB-CB-WS01, ASB-PB-WS02 (Windows 10 Pro) |
| User Accounts | 15 simulated users across 4 department OUs + 2 service accounts |
| Group Policy | Password policy, workstation lockdown, audit policy |

### Fictional Client Context

All Active Directory work is performed against a fictional client environment — **AcrossStatesBank** — a representative community bank scenario. See [client-profiles/AcrossStatesBank.md](./client-profiles/AcrossStatesBank.md) for full engagement context.

### Why an AD Domain in the Lab

A working Active Directory environment provides realistic conditions for practicing the attacks, enumeration, and defensive controls that dominate real-world enterprise security work. Most small and mid-sized businesses run domain-joined Windows environments — meaning AD assessment skills directly apply to the kind of consulting engagements AegisPro CyberShield TX performs.

This environment supports practice across:

- Active Directory enumeration (BloodHound, CrackMapExec, ldapsearch)
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

### Active Directory Tools
| Tool | Purpose | Usage |
|------|---------|-------|
| **BloodHound / SharpHound** | AD relationship visualization | Mapping attack paths from any user to Domain Admin, identifying privilege escalation routes |
| **CrackMapExec** | AD post-exploitation Swiss Army knife | Enumeration, credential validation, lateral movement, share enumeration |
| **Impacket suite** | Python AD attack toolkit | Kerberoasting (`GetUserSPNs.py`), AS-REP Roasting (`GetNPUsers.py`), Pass-the-Hash, DCSync |
| **Responder** | Network credential harvester | LLMNR/NBT-NS/mDNS poisoning to capture NTLM hashes |
| **Hashcat** | Password cracking | Offline cracking of captured Kerberos tickets and NTLM hashes |

### Network Infrastructure
| Tool | Purpose | Usage |
|------|---------|-------|
| **pfSense** | Router / Firewall / Switch | Network segmentation, firewall rule creation and testing, DHCP/DNS management, traffic logging |

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
   └── nmap -sn 10.0.1.0/24

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
   ├── File Upload
   └── CSRF
4. Document findings and payloads used
5. Research remediation for each vulnerability class
```

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

### Active Directory Domain (`ASBank.com`)

**ASBankDC1 — Domain Controller (Windows Server 2019)**
- Hosts AD DS, DNS, and Kerberos for the `ASBank.com` domain
- Holds all FSMO roles
- IP: 10.0.1.201 (static)
- Primary target for enumeration and high-value attacks (DCSync, Kerberoasting, NTDS.dit dumping)

**ASB-CB-WS01, ASB-PB-WS02 — Domain-Joined Workstations (Windows 10 Pro)**
- ASB-CB-WS01: Commercial Banking department workstation
- ASB-PB-WS02: Personal Banking department workstation
- Used to practice initial access, credential dumping, and lateral movement

**Simulated Users and Groups**
- 15 user accounts across Commercial Banking, Personal Banking, Corp, and IT Department OUs
- Service accounts (svc_sql, svc_backup) configured with SPNs for Kerberoasting practice
- svc_backup configured with pre-authentication disabled for AS-REP Roasting practice
- Marcus Webb (m.webb) — Domain Admin account for lateral movement/privilege escalation scenarios

**Primary use:** Active Directory attack simulation, lateral movement practice, credential access scenarios, and AD defensive control validation.

### VulnHub Machines

Rotating selection of CTF-style vulnerable VMs from [VulnHub](https://www.vulnhub.com). Each machine is documented in a separate write-up within this repository.

**Completed machines:** *(see `/vulnhub-writeups/` folder)*

---

## Repository Structure

```
Aegispro-Lab-Setup/
├── README.md                        ← this file
├── build/                           ← phase-by-phase lab build documentation
│   ├── README.md
│   ├── phase-1-vmware-setup.md
│   ├── phase-2-pfsense-network.md
│   ├── phase-3-kali-tools.md
│   ├── phase-4-target-machines.md
│   ├── phase-5-active-directory.md
│   ├── phase-6-vulnerability-assessment.md
│   ├── phase-7-web-application-testing.md
│   ├── phase-8-firewall-rule-testing.md
│   ├── phase-9-active-directory-attacks.md
│   ├── phase-10-documentation.md
│   └── images/
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
├── client-profiles/
│   └── AcrossStatesBank.md
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
| Firewall rule design & testing | pfSense default-deny policy, Kali attack traffic validation |
| Active Directory administration | Server 2019 DC, OU/GPO design, user management |
| AD security assessment | BloodHound CE, CrackMapExec, Impacket, Responder |
| Kerberos attack techniques | Kerberoasting (mode 13100), AS-REP Roasting (mode 18200) |
| Credential capture & cracking | Responder NTLMv2, Hashcat modes 5600/13100/18200 |
| Lateral movement | evil-winrm, Domain Admin shell on DC01 |
| AD attack path visualization | BloodHound CE, Cypher queries, Domain Admin mapping |
| MITRE ATT&CK alignment | T1557.001, T1558.003, T1558.004, T1021.006 |
| Lab design & network isolation | VMware Workstation Pro + pfSense multi-segment networking |

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
- **Website:** [aegisprotx.com](https://www.aegisprotx.com)
- **Contact:** nturner@aegisprotx.com | [LinkedIn](https://linkedin.com/in/nturner18)

---

*Last updated: June 2026*
