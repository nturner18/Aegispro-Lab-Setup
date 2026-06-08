# Lab Build Documentation
## AegisPro CyberShield TX — Home Lab Setup

This section documents the complete build process for the AegisPro CyberShield TX cybersecurity practice lab. Each phase is documented individually with objectives, steps taken, troubleshooting notes, and screenshots.

**This lab build is complete at Phase 10.** Ongoing lab practice and advanced security work is documented in **[AegisPro-Home-Lab](https://github.com/nturner18/AegisPro-Home-Lab)**.

> **Disclaimer:** All testing performed in this lab targets owned, intentionally vulnerable machines on an isolated private network. No unauthorized systems are ever targeted. The AcrossStatesBank AD environment is entirely fictional.

---

## Build Phases

| Phase | Title | Status |
|-------|-------|--------|
| [Phase 1](./phase-1-vmware-setup.md) | VMware Setup & VM Installation | ✅ Complete |
| [Phase 2](./phase-2-pfsense-network.md) | pfSense Network Configuration | ✅ Complete |
| [Phase 3](./phase-3-kali-tools.md) | Kali Linux Tool Verification | ✅ Complete |
| [Phase 4](./phase-4-target-machines.md) | Linux & Web Application Target Setup | ✅ Complete |
| [Phase 5](./phase-5-active-directory.md) | Active Directory Environment Build | ✅ Complete |
| [Phase 6](./phase-6-vulnerability-assessment.md) | Vulnerability Assessment Workflow | ✅ Complete |
| [Phase 7](./phase-7-web-application-testing.md) | Web Application Testing Workflow | ✅ Complete |
| [Phase 8](./phase-8-firewall-rule-testing.md) | Firewall Rule Testing | ✅ Complete |
| [Phase 9](./phase-9-active-directory-attacks.md) | Active Directory Attack Workflow | ✅ Complete |
| [Phase 10](./phase-10-documentation.md) | Documentation & Screenshot Collection | ✅ Complete |

---

## Lab Summary

### Network Architecture

| Component | Details |
|-----------|---------|
| Hypervisor | VMware Workstation Pro 25H2 |
| Firewall / Router | pfSense (LAN: 10.0.1.1) |
| Attacker | Kali Linux 2025.2 |
| Domain Controller | ASBankDC1 — Windows Server 2019 (10.0.1.201) |
| Domain | ASBank.com |
| Workstations | ASB-CB-WS01, ASB-PB-WS02 (Windows 10 Pro) |
| Linux Target | Metasploitable 2 |
| Web Target | DVWA |
| AD Users | 15 simulated users across 4 departments + 2 service accounts |

### Tools

| Category | Tools |
|----------|-------|
| Vulnerability scanning | OpenVAS / GVM, Nmap, Nikto |
| Web application testing | Burp Suite, DVWA |
| Exploitation | Metasploit Framework |
| AD assessment | Impacket, CrackMapExec, Responder, BloodHound, Hashcat |
| Network analysis | Wireshark |
| Firewall / routing | pfSense |

### Fictional Client Context

All Active Directory work is performed against a fictional client environment — **AcrossStatesBank** — a representative community bank scenario. See [client-profiles/AcrossStatesBank.md](../client-profiles/AcrossStatesBank.md) for full engagement context.

---

*AegisPro CyberShield TX | www.aegisprotx.com*
