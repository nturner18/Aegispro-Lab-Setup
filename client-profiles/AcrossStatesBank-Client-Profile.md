# Fictional Client Profile: AcrossStatesBank
## AegisPro CyberShield TX — Engagement Reference Document

> **Notice:** AcrossStatesBank is a fictional organization used as a representative target environment for AegisPro CyberShield TX's internal security assessment practice and skill development. This document establishes the operational context for all assessments, attack simulations, and defensive engineering performed against the lab Active Directory domain. No real organization, financial institution, or individual is depicted.

---

## 1. Client Overview

**Organization:** AcrossStatesBank
**Industry:** Community/Regional Banking — Financial Services
**Size:** ~150 employees
**Locations:** 3 branch offices across the southwestern United States, with a primary administrative headquarters
**Headquarters (fictional):** Fort Worth, Texas
**Business profile:** A privately-held community bank serving retail customers, small business depositors, and providing commercial loan services across a multi-state regional footprint

AcrossStatesBank represents a realistic small-to-mid-sized financial institution — large enough to have meaningful IT infrastructure and regulatory exposure, small enough that security responsibilities are concentrated in a small team without dedicated SOC staffing. This profile mirrors the kind of organization that would engage AegisPro CyberShield TX for assessment, remediation, and ongoing managed security services.

---

## 2. Engagement Context

**Engagement type:** Internal network security assessment focused on Active Directory and Windows endpoint security
**Authorization:** All activity authorized in writing by AcrossStatesBank executive leadership (fictional)
**Scope:** Internal `corp.acrossstatesbank.local` Active Directory domain, including the domain controller, member workstations, and simulated user population
**Objective:** Identify exploitable security weaknesses, demonstrate realistic attack paths, and produce actionable remediation recommendations aligned to applicable regulatory frameworks
**Out of scope:** Production banking applications, customer-facing systems, payment networks, third-party processors

> This scoping reflects how a real AegisPro engagement would be structured — bounded, authorized, and documented in writing before any technical work begins.

---

## 3. Regulatory & Compliance Context

As a fictional financial institution, AcrossStatesBank would operate under several overlapping regulatory regimes. Understanding these frameworks shapes both the assessment approach and the framing of findings and remediation recommendations.

| Framework | Relevance to AcrossStatesBank |
|-----------|------------------------------|
| **GLBA (Gramm-Leach-Bliley Act)** | Federal law requiring financial institutions to protect customer financial information; mandates a written information security program |
| **FFIEC Cybersecurity Assessment Tool** | Federal Financial Institutions Examination Council guidance on cybersecurity maturity for banks |
| **FFIEC IT Examination Handbook** | Detailed guidance on Information Security, Authentication, and Outsourcing Technology Services |
| **PCI-DSS** | Applies to any environment touching payment card data (likely the bank's card-issuance and debit card operations) |
| **NIST Cybersecurity Framework (CSF)** | Voluntary but widely adopted; provides a structured approach to cybersecurity risk management |
| **State data breach notification laws** | Texas Business and Commerce Code §521 governs breach response and notification timelines |

Findings produced during this engagement should be mapped, where applicable, to the relevant control families in these frameworks. For example, weak Kerberos service account password practices would map to GLBA's "Safeguards Rule" and FFIEC authentication guidance.

---

## 4. Threat Profile

The threat profile for a regional community bank includes both opportunistic and targeted adversaries. AcrossStatesBank's posture should be evaluated against the threats that realistically apply to a financial institution of this size.

### Likely threat actors

- **Financially motivated cybercriminals** — Targeting wire transfer fraud, ACH fraud, customer credential theft, and ransomware extortion. The dominant real-world threat to community banks.
- **Ransomware operators** — Particularly groups targeting financial services for high-value extortion payouts. Recent industry threat reports consistently rank banks among top targets.
- **Insider threats** — Disgruntled or financially-pressured employees with legitimate domain access. Banks are statistically elevated for insider risk due to direct financial exposure.
- **Business email compromise (BEC) actors** — Targeting executives and finance staff to redirect wire transfers or trigger fraudulent payments.
- **Supply chain compromise** — Third-party vendors, MSPs, or core banking platform providers as initial access vectors.

### Likely attack vectors

- Phishing and credential harvesting against employees with privileged access
- Exploitation of internet-facing services (VPN, RDP, Outlook Web Access)
- Active Directory abuse following any initial foothold (Kerberoasting, credential dumping, lateral movement to domain controllers)
- Compromise of service accounts with elevated rights
- Group Policy abuse and AD misconfigurations

---

## 5. Simulated Environment Profile

The AcrossStatesBank lab environment mirrors a small-bank IT footprint at a representative scale. Specific technical details follow:

### Infrastructure

| Asset | Role | OS |
|-------|------|----|
| DC01 | Primary domain controller, DNS, FSMO role holder | Windows Server 2019 |
| WS01 | Administrative workstation (representative of a manager/lending officer endpoint) | Windows 10 Pro |
| WS02 | Standard employee workstation (representative of a teller or back-office endpoint) | Windows 10 Pro |

### Active Directory Configuration

- **Domain:** `corp.acrossstatesbank.local`
- **Forest functional level:** Windows Server 2016
- **Organizational Unit structure:**
  - OU=Workstations
  - OU=Servers
  - OU=Users
    - OU=Employees (standard domain users — tellers, loan officers, back-office)
    - OU=IT (privileged administrative accounts)
    - OU=Service Accounts (application and service identities, intentionally SPN-configured)
  - OU=Groups

### User Population

The simulated user population reflects roles typical of a community bank:

- **Tellers and customer service** — Front-line staff with standard user privileges
- **Loan officers and lending operations** — Standard user privileges with access to lending applications
- **Back-office and operations** — Accounts payable, treasury operations, back-office reconciliation
- **IT staff** — Domain administrators, helpdesk technicians (varying privilege levels)
- **Executive accounts** — Senior leadership with broad access to financial systems
- **Service accounts** — Application identities (database, monitoring, backup) — some intentionally configured with SPNs to enable Kerberoasting assessment practice

### Intentional Weaknesses (For Assessment Practice)

The lab environment is intentionally configured with weaknesses that mirror common findings in real small-business AD assessments. These represent the kind of issues AcrossStatesBank's leadership would expect AegisPro to identify and recommend remediation for:

- **Weak service account passwords** — Some service accounts have intentionally crackable passwords to enable Kerberoasting practice
- **Service Principal Names registered on user accounts** — Increases Kerberoasting attack surface
- **Pre-authentication disabled on at least one user account** — Enables AS-REP Roasting practice
- **Weak password policy baseline** — Minimum length and complexity set intentionally low
- **LLMNR/NBT-NS enabled** — Default Windows configuration; allows Responder-style poisoning
- **Local administrator passwords not randomized** — Common SMB finding in environments without LAPS
- **Audit logging baseline configured but no SOC monitoring** — Reflects the typical reality of community banks (events are generated but not analyzed in real time)

These weaknesses are documented here to make clear that the lab environment is intentionally designed to support assessment practice, and that findings produced from this environment are not novel discoveries — they represent the well-known issues that real community banks must address.

---

## 6. Engagement Approach

### Methodology

Assessments performed against the AcrossStatesBank environment follow the AegisPro CyberShield TX Vulnerability Management Methodology and standard penetration testing frameworks adapted for AD assessment:

1. **Reconnaissance** — Network discovery, identify domain hosts, enumerate services
2. **Unauthenticated enumeration** — SMB, LDAP, DNS, and Kerberos enumeration without credentials
3. **Credential acquisition** — Network credential capture (Responder), spray attacks, hash cracking
4. **Authenticated enumeration** — BloodHound collection, share enumeration, attack path analysis
5. **Kerberos attacks** — Kerberoasting, AS-REP Roasting
6. **Lateral movement** — Validated credential reuse across hosts, Pass-the-Hash, PsExec/WinRM
7. **Privilege escalation** — Domain Admin path execution, ACL abuse, GPO abuse
8. **Documentation and remediation mapping** — Every finding mapped to a defensive control, detection opportunity, and prioritized remediation

### Deliverables

For every assessment activity, AegisPro produces:

- **Technical write-up** — Step-by-step methodology and outcome
- **Findings register** — Tabular summary of vulnerabilities, severity, and affected assets
- **Remediation recommendations** — Specific, actionable hardening guidance
- **Detection engineering notes** — What event IDs, log sources, or SIEM rules would detect the same activity in production
- **Regulatory mapping** — Where applicable, findings cross-referenced to GLBA, FFIEC, NIST CSF, and PCI-DSS controls

---

## 7. Why This Profile Exists

This document serves several purposes simultaneously:

**For skill development:** It provides realistic business context for every lab attack. Running Kerberoasting against "the AcrossStatesBank lab DC" is mentally different from running it against "my home lab" — the former trains the engagement-thinking muscle that real consulting work requires.

**For portfolio quality:** Public write-ups that reference a fictional client engagement read substantially more professional than generic lab walkthroughs. They demonstrate the candidate thinks in terms of business value, scope, and regulatory context — not just technical execution.

**For interview narrative:** "I run a cybersecurity practice that performs Active Directory assessments for small businesses; I maintain a representative bank environment to validate techniques and develop new methodology" is a substantially stronger answer than "I have a home lab."

**For methodology refinement:** Treating the lab as a fictional client forces the practitioner to follow the same engagement structure (scope, authorization, methodology, deliverables) that a real client engagement requires. This is operational practice, not just technical practice.

---

## 8. Document Control

| Field | Value |
|-------|-------|
| Document type | Internal client profile reference |
| Prepared by | AegisPro CyberShield TX |
| Status | Active |
| Distribution | Internal use only |
| Related documents | Vulnerability Management Methodology, Home Lab README, AD Build Notes |

---

*AcrossStatesBank is a fictional organization. Any resemblance to real banks, financial institutions, or persons living or dead is coincidental. This document exists solely to provide engagement context for security skill development and portfolio work performed by AegisPro CyberShield TX.*

*AegisPro CyberShield TX | www.aegisprotx.com*
