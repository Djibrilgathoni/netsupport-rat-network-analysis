# NetSupport Manager RAT — Network Forensics Analysis

A practical blue team network forensics investigation identifying a 
NetSupport Manager Remote Access Trojan (RAT) Command & Control infection 
using Wireshark packet analysis.

**Author:** Djibril  
**Date:** 26th March 2026  
**Exercise Source:** [Malware-Traffic-Analysis.net](https://malware-traffic-analysis.net) 
— 2026-02-28 "Easy As 123"  
**Context:** IBM SkillsBuild – Cybersecurity Fundamentals (Practical Application)

---

## Overview

A SOC SIEM system generated alerts for NetSupport Manager RAT activity 
originating from external IP `45.131.214.85` over TCP port 443, disguised 
as legitimate HTTPS traffic. Using Wireshark on a Kali Linux VM, I performed 
full packet-level analysis to identify the compromised host, user account, 
and indicators of compromise.

---

## Key Findings

| Indicator | Value |
|---|---|
| C2 Server IP | `45.131.214.85` |
| C2 Port | TCP `443` (disguised as HTTPS) |
| Infected Host IP | `10.2.28.88` |
| Infected Host MAC | `00:19:d1:b2:4d:ad` |
| Hostname | `DESKTOP-TEYQ2NR` |
| Compromised User | `brolf` (Becka Rolf) |
| Incident Time | 2026-02-28 ~19:55 UTC |

---

## Repository Structure
```
├── README.md
├── report.md                  # Full forensics report
├── iocs.md                    # Indicators of Compromise
├── wireshark-filters.md       # Filters & methodology used
├── runbook.md                 # Incident response playbook
└── screenshots/               # Wireshark evidence screenshots
```

---

## Tools Used

- **Wireshark** — Packet capture analysis
- **Kali Linux** — Analysis environment
- **IBM SkillsBuild** — Cybersecurity Fundamentals course

---

## Skills Demonstrated

- Network traffic analysis and anomaly detection
- C2 traffic identification
- Host and user attribution from packet data
- Incident response and containment planning
- Blue team documentation and reporting
