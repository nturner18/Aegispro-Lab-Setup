# Phase 10 — Documentation & Screenshot Collection
## AegisPro CyberShield TX — Lab Build Capstone
### Lab Build Complete

---

## Objective

Consolidate all lab work into a complete, organized, and publicly accessible portfolio. This phase covers the full screenshot inventory across all phases, configuration documentation, lab maintenance procedures, and a consolidated skills summary demonstrating the breadth of hands-on experience developed throughout the lab build.

---

## Lab Build — Complete

All 10 phases of the AegisPro CyberShield TX home lab build are now complete. The lab represents a fully operational enterprise-realistic cybersecurity practice environment covering network architecture, firewall implementation, Active Directory administration, vulnerability assessment, web application testing, and Active Directory attack simulation.

| Phase | Title | Status |
|-------|-------|--------|
| [Phase 1](./phase-1-vmware-setup.md) | VMware Setup & VM Installation | ✅ Complete |
| [Phase 2](./phase-2-pfsense-network.md) | pfSense Network Configuration | ✅ Complete |
| [Phase 3](./phase-3-kali-tools.md) | Kali Linux Tool Verification | ✅ Complete |
| [Phase 4](./phase-4-target-machines.md) | Linux & Web Application Target Setup | ✅ Complete |
| [Phase 5](./phase-5-active-directory.md) | Active Directory Environment Build | ✅ Complete |
| [Phase 6](./phase-6-vulnerability-assessment.md) | Vulnerability Assessment Workflow | ✅ Complete |
| [Phase 7](./phase-7-web-application-testing.md) | Web Application Testing Workflow | ✅ Complete |
| [Phase 8](./phase-8-firewall-rule-testing.md) | Firewall Rule Testing & pfSense Upgrade | ✅ Complete |
| [Phase 9](./phase-9-active-directory-attacks.md) | Active Directory Attack Workflow | ✅ Complete |
| Phase 10 | Documentation & Screenshot Collection | ✅ Complete |

---

## Screenshot Inventory

All screenshots are stored in `/build/images/`. The following table documents every screenshot referenced across all phase write-ups.

### Phase 1 — VMware Setup

| Filename | Content |
|----------|---------|
| `vmware-setup.png` | VMware library showing all VMs imported and ready |

### Phase 2 — pfSense Network

| Filename | Content |
|----------|---------|
| `pfsense-dashboard.png` | pfSense dashboard showing interface overview |
| `host-discovery.png` | Kali terminal showing nmap host discovery results |

### Phase 3 — Kali Tools

| Filename | Content |
|----------|---------|
| `openvas-dashboard.png` | OpenVAS GVM dashboard showing services running |

### Phase 4 — Target Machines

| Filename | Content |
|----------|---------|
| `nmap-metasploitable-2.png` | Nmap full service scan results against Metasploitable 2 |

### Phase 5 — Active Directory

| Filename | Content |
|----------|---------|
| `ad-ou-structure.png` | ADUC showing complete OU structure |
| `dc01-server-manager.png` | DC01 Server Manager dashboard |
| `workstation-domain-joined.png` | Workstation logged in with domain account |
| `kerberoastable-accounts.png` | impacket-GetUserSPNs showing svc_sql and svc_backup |

### Phase 6 — Vulnerability Assessment

| Filename | Content |
|----------|---------|
| `openvas-report.png` | OpenVAS scan report summary showing findings by severity |
| `nikto-results.png` | Nikto web server scan output |
| `metasploit-vsftpd.png` | Metasploit vsftpd exploitation — root shell confirmed |

### Phase 7 — Web Application Testing

| Filename | Content |
|----------|---------|
| `sqli-all-records.png` | SQL injection returning all database user records |
| `sqli-burp-repeater.png` | Burp Suite Repeater showing injection request and response |
| `xss-reflected-alert.png` | XSS Reflected alert popup |
| `xss-stored-alert.png` | XSS Stored alert popup firing from database |
| `command-injection-whoami.png` | Command injection whoami output — www-data |
| `file-upload-low-success.png` | Successful PHP webshell upload on Low security |
| `file-upload-rce-whoami.png` | Webshell RCE showing whoami in browser |
| `file-upload-burp-bypass.png` | Burp showing Content-Type header modification |
| `file-upload-medium-rce.png` | RCE confirmed at Medium security after bypass |

### Phase 8 — Firewall Rule Testing

| Filename | Content |
|----------|---------|
| `pfsense-rules-before.png` | pfSense LAN rules before default-deny implementation |
| `pfsense-rules-after.png` | pfSense LAN rules after default-deny implementation |
| `pfsense-upgrade.png` | pfSense upgrade to 2.8.1 confirmation |
| `icmp-block-test.png` | Kali ping blocked by firewall rule |
| `firewall-logs.png` | pfSense firewall logs showing blocked traffic |

### Phase 9 — Active Directory Attacks

| Filename | Content |
|----------|---------|
| `ad-recon-nmap.png` | Nmap service scan showing AD port profile on DC01 |
| `responder-capture.png` | Responder output showing captured NTLMv2 hash |
| `hashcat-ntlmv2-cracked.png` | Hashcat mode 5600 cracked NTLMv2 output |
| `kerberoastable-accounts.png` | impacket-GetUserSPNs showing Kerberoastable accounts |
| `hashcat-kerberoast-cracked.png` | Hashcat mode 13100 cracked TGS hashes |
| `asrep-hash-captured.png` | GetNPUsers output showing svc_backup AS-REP hash |
| `hashcat-asrep-cracked.png` | Hashcat mode 18200 cracked AS-REP hash |
| `bloodhound-domain-admins.png` | BloodHound Domain Admins group members |
| `bloodhound-kerberoastable.png` | BloodHound Cypher query — Kerberoastable accounts |
| `bloodhound-mwebb-node.png` | BloodHound m.webb node showing Domain Admin properties |
| `lateral-movement-shell.png` | evil-winrm shell on DC01 as asbank\m.webb |

---

## Configuration Documentation

Key configuration notes are maintained in the `/configs/` directory for reference and repeatability.

### pfSense Firewall Rules Summary

Current working ruleset — default-deny with explicit allow rules:

| Rule | Protocol | Source | Destination | Port | Description |
|------|----------|--------|-------------|------|-------------|
| 1 | Any | Any | LAN Address | 443/80 | Anti-Lockout Rule |
| 2 | TCP | LAN net | Any | 443 | Allow HTTPS outbound |
| 3 | TCP | LAN net | Any | 80 | Allow HTTP outbound |
| 4 | TCP | 10.0.1.201 | Any | 443/80 | Allow DC01 outbound web |
| 5 | TCP/UDP | 10.0.1.201 | Any | 53 | Allow DC01 outbound DNS |
| 6 | TCP/UDP | LAN net | 10.0.1.201 | AD_Ports | Allow AD ports to DC01 |
| 7 | TCP/UDP | LAN net | LAN address | 53 | Allow DNS to pfSense |
| 8 | TCP/UDP | LAN net | 10.0.1.201 | 53 | Allow DNS to DC01 |
| 9 | ICMP | LAN net | Any | Any | Allow ICMP |
| 10 | TCP/UDP | LAN net | Any | 123 | Allow NTP |
| 11-13 | — | — | — | — | Default allow — DISABLED |

**AD_Ports alias:** 53, 88, 135, 139, 389, 445, 464, 636, 3268, 3269, 49152-65535

### IP Address Inventory

| VM | IP | Assignment | DNS |
|----|-----|------------|-----|
| pfSense LAN | 10.0.1.1 | Static | — |
| Kali Linux | 10.0.1.10 | Static DHCP reservation | 10.0.1.1 |
| DC01 (ASBankDC1) | 10.0.1.201 | Static | 127.0.0.1 (itself) |
| ASB-CB-WS01 | DHCP pool | 10.0.1.100-200 | 10.0.1.201 |
| ASB-PB-WS02 | DHCP pool | 10.0.1.100-200 | 10.0.1.201 |
| Metasploitable 2 | 10.0.1.104 | DHCP | 10.0.1.1 |
| DVWA | 10.0.1.101 | DHCP | 10.0.1.1 |

---

## Lab Maintenance Procedures

### Weekly

```bash
# Update Kali packages
sudo apt update && sudo apt full-upgrade -y

# Update OpenVAS vulnerability feeds
sudo gvm-feed-update

# Check pfSense for updates
# System → Update → System Update
```

### Monthly

- Take VMware snapshots of all VMs labeled with the date
- Review pfSense firewall logs for anomalies
- Verify all VM network connectivity with the checklist below
- Add a new VulnHub machine to the lab rotation

### Connectivity verification checklist

```bash
# Run after any network change or restart
ping -c 2 10.0.1.1          # pfSense reachable
ping -c 2 10.0.1.201        # DC01 reachable
nslookup google.com          # External DNS working
curl -I https://google.com   # Internet working
nmap -sn 10.0.1.0/24        # All lab hosts visible
```

### Common troubleshooting

| Symptom | Fix |
|---------|-----|
| Kali DNS not resolving | `sudo service dnsmasq restart` on pfSense console (Option 8) |
| DC01 DNS broken | `Restart-Service DNS` on DC01 PowerShell |
| pfSense web GUI unreachable | Console Option 11 (Restart webConfigurator) or Option 16 (Restart PHP-FPM) |
| Internet down on workstations | Restart DC01 — DNS forwarder service may have stopped |
| BloodHound won't start | `bloodhound-start` — CE version; login at `127.0.0.1:8080` with `admin/admin` |
| Hashcat potfile hit | Add `--show` flag to display previously cracked results |
| zsh `!` syntax error | Use single quotes around passwords containing `!` |

---

## Skills Demonstrated

### Technical skills across all 10 phases

| Skill Area | Tools / Evidence | Phase |
|------------|-----------------|-------|
| Hypervisor administration | VMware Workstation Pro 25H2 — 7 VM lab | 1 |
| Network architecture & segmentation | pfSense multi-segment lab design | 2 |
| Firewall rule design & validation | Default-deny policy, explicit allow rules | 2, 8 |
| Linux administration | Kali 2025.2 — tool installation, config, troubleshooting | 3 |
| Vulnerability scanning | OpenVAS/GVM, Nmap, Nikto | 6 |
| Exploitation & post-exploitation | Metasploit, vsftpd backdoor, root shell | 6 |
| Web application security testing | Burp Suite, DVWA, OWASP Top 10 | 7 |
| Proxy-based testing & bypass | Burp Intercept, Repeater, MIME type bypass | 7 |
| Active Directory administration | Server 2019 DC, OU design, GPO, 15 users | 5 |
| AD security assessment | Impacket, CrackMapExec, enum4linux-ng | 9 |
| Credential capture & cracking | Responder, Hashcat modes 5600/13100/18200 | 9 |
| Kerberos attack techniques | Kerberoasting, AS-REP Roasting | 9 |
| AD attack path visualization | BloodHound CE, Cypher queries | 9 |
| Lateral movement | evil-winrm, Domain Admin shell on DC | 9 |
| Security reporting & documentation | Phase write-ups, assessment reports, GitHub | All |
| Version control | Git — repo management, commits, branching | All |

### CISSP Domain Coverage

| Domain | Lab Activities |
|--------|---------------|
| Domain 1 — Security & Risk Management | Risk identification, vulnerability prioritization, defensive recommendations |
| Domain 4 — Communication & Network Security | pfSense architecture, firewall rules, DNS, network segmentation |
| Domain 5 — Identity & Access Management | AD domain design, Kerberos, GPO access controls, privilege management |
| Domain 6 — Security Assessment & Testing | Vulnerability scanning, web app testing, AD attack simulation, reporting |
| Domain 7 — Security Operations | Detection engineering, Event ID mapping, MITRE ATT&CK alignment, tool operation |

---

## What's Next

This repository is now complete at Phase 10. The lab build, configuration, and initial attack simulation work is fully documented across all 10 phases.

Ongoing lab practice, advanced tooling integration, and future security work will be documented in a dedicated repository:

**[AegisPro-Home-Lab](https://github.com/nturner18/AegisPro-Home-Lab)**

That repo uses a labs-based structure rather than phases, organized by activity type. Planned work includes:

| Lab | Description | Priority |
|-----|-------------|---------|
| Security Onion SIEM Integration | Deploy Security Onion on D: drive, integrate with pfSense traffic mirroring | High — pending 64GB RAM |
| Wazuh EDR Deployment | Endpoint detection and response across domain workstations | Medium |
| VLAN Segmentation | Resolve architectural finding from Phase 8 — segment lab network | Medium |
| AD CS Attacks | Active Directory Certificate Services attack simulation | Medium |
| Additional VulnHub Machines | Rotating CTF-style targets | Ongoing |
| AD Hardening | GPO hardening, LAPS, tiered admin model | Medium |
| Cloud Integration | Azure AD hybrid join, Entra ID Conditional Access | Low |

---

## Repository Structure — Final State

```
Aegispro-Lab-Setup/
├── README.md                              ← Root overview with host machine specs
├── build/
│   ├── README.md                          ← Phase index
│   ├── phase-1-vmware-setup.md            ✅
│   ├── phase-2-pfsense-network.md         ✅
│   ├── phase-3-kali-tools.md              ✅
│   ├── phase-4-target-machines.md         ✅
│   ├── phase-5-active-directory.md        ✅
│   ├── phase-6-vulnerability-assessment.md ✅
│   ├── phase-7-web-application-testing.md ✅
│   ├── phase-8-firewall-rule-testing.md   ✅
│   ├── phase-9-active-directory-attacks.md ✅
│   ├── phase-10-documentation.md          ✅
│   └── images/                            ← All phase screenshots
└── client-profiles/
    └── AcrossStatesBank.md
```

> **Note:** This repository is complete. Ongoing lab work, advanced tooling, and future security practice is documented in **[AegisPro-Home-Lab](https://github.com/nturner18/AegisPro-Home-Lab)**.

---

*Previous: [Phase 9 — Active Directory Attack Workflow](./phase-9-active-directory-attacks.md)*

---

*AegisPro CyberShield TX — Phase 10 Lab Documentation — June 2026*
*Lead Assessor: Nicholas Turner, CISSP*
