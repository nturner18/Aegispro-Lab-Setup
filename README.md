# AegisPro CyberShield TX — Home Lab Setup

> A fully operational cybersecurity practice environment built to develop hands-on offensive and defensive security skills, support vulnerability assessment engagements, and document practical experience aligned with CISSP domains.

**Operator:** Nick Turner | AegisPro CyberShield TX  
**Credential:** CISSP  
**Website:** [aegisprotx.com](https://www.aegisprotx.com)  
**Location:** Fort Worth, Texas

> **Disclaimer:** All testing in this lab is performed exclusively against owned, intentionally vulnerable machines on an isolated private network. The AcrossStatesBank Active Directory environment is entirely fictional. No real organization, financial institution, or individual is depicted or targeted.

---

## Lab Overview

This lab is built on VMware Workstation Pro and covers the full stack of a small-business cybersecurity assessment environment — from network architecture and firewall configuration through to Active Directory attack simulation and web application testing.

### Network Architecture

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
│  DC01 (10.0.1.201)   ASB-CB-WS01   ASB-PB-WS02             │     │
│  Metasploitable 2    DVWA VM        VulnHub Targets         │     │
│     └────────────────────────────────────────────────────── ┘     │
└────────────────────────────────────────────────────────────────────┘
```

### Host Machine

This lab runs on a custom-built desktop PC purpose-built to handle multiple simultaneous virtual machines. All VMs run on this single host via VMware Workstation Pro.

| Component | Details |
|-----------|---------|
| **CPU** | Intel Core i9-10900KF — 10 cores / 20 threads @ 3.70GHz |
| **RAM** | 64GB DDR4 (V-Color Technology — 2x 32GB) |
| **Motherboard** | MSI MPG Z590 Gaming Carbon WiFi (MS-7D06) |
| **GPU** | MSI GeForce RTX 5060 Ti Gaming Trio OC 16GB |
| **Storage (Primary)** | WD Black SN7100 2TB NVMe SSD — OS and primary VM storage |
| **Storage (Secondary)** | WDC WDBNCE5000PNC 500GB — supplemental storage |
| **Cooling** | Lian Li Galahad II LCD AIO Liquid Cooler |
| **Case** | Phanteks XT Pro Mid-Tower — High Airflow Mesh, Tempered Glass |
| **OS** | Microsoft Windows 11 Pro (Build 26200) |
| **Hypervisor** | VMware Workstation Pro 25H2 (25.0.0.24995812) |

**Why this hardware matters for the lab:**

The i9-10900KF's 10-core/20-thread configuration allows 7+ VMs to run simultaneously without contention. 64GB RAM provides generous allocation across all VMs including pfSense (4GB), DC01 (8GB), Kali (8GB), two workstations, and future Security Onion integration (24GB). The NVMe primary drive ensures fast VM disk I/O — critical for OpenVAS scanning and AD operations that are disk-intensive.

### Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VMware Workstation Pro 25H2 |
| Attacker OS | Kali Linux 2025.2 (Rolling) |
| Firewall / Router | pfSense (LAN: 10.0.1.1) |
| Domain Controller | ASBankDC1 — Windows Server 2019 (10.0.1.201) |
| Domain | ASBank.com (fictional — AcrossStatesBank) |
| Workstations | ASB-CB-WS01, ASB-PB-WS02 (Windows 10 Pro, domain-joined) |
| Linux Target | Metasploitable 2 |
| Web App Target | DVWA (Damn Vulnerable Web Application) |
| Additional Targets | VulnHub machines (rotating) |

### Tools

| Category | Tools |
|----------|-------|
| Vulnerability scanning | OpenVAS / GVM, Nmap, Nikto |
| Web application testing | Burp Suite Community, DVWA |
| Exploitation | Metasploit Framework |
| AD assessment | Impacket, CrackMapExec, Responder, BloodHound, Hashcat |
| Network analysis | Wireshark |
| Firewall / routing | pfSense |

---

## Active Directory Environment

A fully operational Windows Active Directory domain simulates the IT environment of a fictional community bank — **AcrossStatesBank**. The domain includes:

- **Domain Controller:** ASBankDC1 (Windows Server 2019)
- **Domain:** ASBank.com
- **15 domain users** across Commercial Banking, Personal Banking, Corp, and IT Department OUs
- **Service accounts** with SPNs configured for Kerberoasting practice
- **AS-REP Roastable account** for AS-REP Roasting practice
- **GPO baseline** including audit policy, workstation lockdown, and password policy
- **Groups OU** with department security groups

See the [client profile](./client-profiles/AcrossStatesBank.md) for full AcrossStatesBank engagement context.

---

## Repository Structure

```
Aegispro-Lab-Setup/
├── README.md                          ← this file
├── build/                             ← lab build documentation (phases 1-10)
│   ├── README.md                      ← build overview and phase index
│   ├── phase-1-vmware-setup.md
│   ├── phase-2-pfsense-network.md
│   ├── phase-3-kali-tools.md
│   ├── phase-4-target-machines.md
│   ├── phase-5-active-directory.md
│   └── images/                        ← screenshots for all phases
├── assessments/                       ← vulnerability assessment write-ups
│   ├── metasploitable-2/
│   └── active-directory/
│       ├── kerberoasting/
│       ├── bloodhound-enumeration/
│       ├── lateral-movement/
│       └── ad-hardening-recommendations/
├── vulnhub-writeups/                  ← individual VulnHub machine write-ups
├── configs/                           ← configuration notes
│   ├── openvas-scan-config-notes.md
│   ├── pfsense-firewall-rules-notes.md
│   └── active-directory-build-notes.md
└── client-profiles/                   ← fictional client engagement context
    └── AcrossStatesBank.md
```

---

## Build Documentation

The lab build is documented phase by phase. Each write-up covers objectives, steps taken, commands used, troubleshooting encountered, and screenshots.

| Phase | Title | Status |
|-------|-------|--------|
| [Phase 1](./build/phase-1-vmware-setup.md) | VMware Setup & VM Installation | ✅ Complete |
| [Phase 2](./build/phase-2-pfsense-network.md) | pfSense Network Configuration | ✅ Complete |
| [Phase 3](./build/phase-3-kali-tools.md) | Kali Linux Tool Verification | ✅ Complete |
| [Phase 4](./build/phase-4-target-machines.md) | Linux & Web Application Target Setup | ✅ Complete |
| [Phase 5](./build/phase-5-active-directory.md) | Active Directory Environment Build | ✅ Complete |
| [Phase 6](./build/phase-6-vulnerability-assessment.md) | Vulnerability Assessment Workflow | ✅ Complete |
| [Phase 7](./build/phase-7-web-application-testing.md) | Web Application Testing Workflow | ✅ Complete |
| Phase 8 | Firewall Rule Testing | 🔄 In Progress |
| Phase 9 | Active Directory Attack Workflow | 🔄 In Progress |
| Phase 10 | Documentation & Screenshot Collection | 🔄 In Progress |

---

## Skills Demonstrated

| Skill Area | Tools / Evidence |
|------------|-----------------|
| Vulnerability scanning & analysis | OpenVAS, Nmap, Nikto |
| Web application security testing | Burp Suite, DVWA |
| Exploitation & post-exploitation | Metasploit Framework |
| Network traffic analysis | Wireshark |
| Network architecture & segmentation | pfSense multi-segment design |
| Firewall rule design & validation | pfSense default-deny policy |
| Active Directory administration | Server 2019 DC, OU design, GPO, user management |
| AD security assessment | Impacket, CrackMapExec, Responder, BloodHound |
| Kerberos attack techniques | Kerberoasting, AS-REP Roasting |
| Security reporting & documentation | Phase write-ups, assessment reports |

---

## CISSP Domain Alignment

| Domain | Lab Activities |
|--------|---------------|
| Domain 1 — Security & Risk Management | Risk-based lab design, intentional weakness documentation |
| Domain 4 — Communication & Network Security | pfSense architecture, AD DNS, network segmentation |
| Domain 5 — Identity & Access Management | AD domain design, Kerberos, GPO access controls |
| Domain 6 — Security Assessment & Testing | Vulnerability scanning, penetration testing, AD attack simulation |
| Domain 7 — Security Operations | Audit logging, event analysis, detection engineering |

---

## About AegisPro CyberShield TX

AegisPro CyberShield TX is an independent cybersecurity consulting practice based in Fort Worth, Texas, providing vulnerability assessment and security consulting services to small and medium-sized businesses.

- **Services:** Vulnerability assessment, network security, risk & compliance, endpoint security, security awareness training
- **Focus:** Small business and specialty retail security
- **Credential:** CISSP
- **Website:** [aegisprotx.com](https://www.aegisprotx.com)
- **Contact:** nturner@aegisprotx.com

---

*Last updated: May 2026*
