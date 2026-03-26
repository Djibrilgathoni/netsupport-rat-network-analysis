# Blue Team Network Forensics Report
**Detecting and Analyzing NetSupport Manager RAT Command & Control Traffic Using Wireshark**

**Author:** Djibril  
**Date:** 26th March 2026  
**Exercise Source:** Malware-Traffic-Analysis.net – 2026-02-28 "Easy As 123"  
**Context:** IBM SkillsBuild – Cybersecurity Fundamentals (Practical Application)

---

## Executive Summary

A Security Operations Center (SOC) SIEM system generated multiple signature alerts for 
NetSupport Manager Remote Access Trojan (RAT) activity originating from external IP 
`45.131.214.85` over TCP port 443, disguised as legitimate HTTPS traffic. The suspicious 
activity began on **2026-02-28 at approximately 19:55 UTC**.

Using Wireshark, I performed packet-level analysis on the provided PCAP to identify the 
compromised Windows endpoint, associated user account, and indicators of persistence. 
This hands-on exercise demonstrates core blue team skills in network traffic analysis, 
anomaly detection, and incident response — directly applying concepts from the IBM 
Cybersecurity Fundamentals course.

> **Key Outcome:** The infected host was fully identified, enabling rapid containment recommendations.

---

## Incident Background

| Field | Details |
|---|---|
| Threat | NetSupport Manager (legitimate remote admin software abused as a RAT) |
| C2 IP | `45.131.214.85` |
| Protocol / Port | TCP 443 (blending with normal HTTPS traffic to evade detection) |
| Internal Network | `10.2.28.0/24` |
| Detection Trigger | SIEM signature hits for NetSupport Manager RAT behavior |

---

## Methodology

1. Downloaded and opened the exercise PCAP in Wireshark on a Kali Linux VM
2. Applied targeted display filters to isolate suspicious traffic
3. Used protocol-specific analysis (IP conversations, Ethernet headers, Kerberos, NBNS) 
to extract host and user details
4. Reviewed statistics (Conversations, Protocol Hierarchy, IO Graph) for traffic patterns
5. Mapped findings to IBM Cybersecurity Fundamentals concepts

For the full filter reference, see [`wireshark-filters.md`](./wireshark-filters.md).

---

## Analysis & Findings

The analysis confirmed a compromised Windows client actively communicating with the 
external C2 server. Below are all extracted indicators.

### 1. Infected Host IP Address
**Value:** `10.2.28.88`  
**Method:** Identified as the internal host with the highest volume of traffic to 
`45.131.214.85` on port 443, discovered using Statistics → Conversations (IPv4).

![C2 traffic filter](./screenshots/c2-traffic-filter.png)
*Figure 1: Filtered view (`ip.addr == 45.131.214.85`) showing heavy C2 traffic from 10.2.28.88*

---

### 2. MAC Address
**Value:** `00:19:d1:b2:4d:ad`  
**Method:** Extracted from the Ethernet II → Source field in packets sent by the 
infected host.

![MAC address extraction](./screenshots/mac-address.png)
*Figure 2: Ethernet II header with MAC address of the infected host*

---

### 3. Hostname
**Value:** `DESKTOP-TEYQ2NR`  
**Method:** Discovered through NBNS (NetBIOS Name Service) queries broadcast by 
the infected host to `10.2.28.255`.

![NBNS hostname](./screenshots/nbns-hostname.png)
*Figure 3: NBNS traffic displaying hostname "DESKTOP-TEYQ2NR"*

---

### 4. User Account Name
**Value:** `brolf`  
**Method:** Extracted using the `kerberos.CNameString` filter in Kerberos 
authentication packets between the infected host and the domain controller.

![Kerberos user](./screenshots/kerberos-user.png)
*Figure 4: `kerberos.CNameString` filter revealing user account "brolf"*

---

### 5. Full Name of the User
**Value:** `Becka Rolf`  
**Method:** Appeared in SAMR (Security Account Manager Remote Protocol) 
QueryUserInfo response packets.

![Full name](./screenshots/full-name.png)
*Figure 5: SAMR response revealing full name "Becka Rolf"*

---

## Evidence of Compromise

- Persistent outbound C2 sessions from `10.2.28.88` to `45.131.214.85` over TCP port 443
- Anomalous traffic volume visible in IO Graphs and Conversations statistics
- HTTP traffic (`application/x-www-form-urlencoded`) tunnelled over TCP 443 — 
a known NetSupport RAT communication pattern
- Use of living-off-the-land techniques (abusing a legitimate remote access tool 
to blend in with normal network activity)

---

## Mapping to IBM Cybersecurity Fundamentals

| Course Topic | Application in This Investigation |
|---|---|
| Types of Attacks | Network-based attack via RAT; attacker used port 443 to masquerade C2 traffic as normal HTTPS |
| Detection | Manual Wireshark analysis used to validate SIEM alerts; protocol filters enabled rapid IOC extraction |
| Incident Response | Rapid host identification (IP, MAC, hostname, user) directly supports containment and eradication |
| Risk Management | Highlights risks of legitimate tool abuse and limitations of relying solely on encryption-based trust |
| Prevention & Best Practices | Reinforces layered defense — segmentation, egress filtering, EDR, and user awareness |

---

## Recommendations

### Immediate Actions
- Isolate infected host (`10.2.28.88`) from the network
- Block all outbound connections to `45.131.214.85` at the firewall
- Reset credentials for user account `brolf`
- Scan the endpoint for persistence mechanisms

### Long-Term Prevention
- Implement network segmentation to limit lateral movement and outbound C2
- Enable TLS inspection or encrypted traffic analytics where feasible
- Deploy Endpoint Detection and Response (EDR) to detect RAT behavior at host level
- Monitor for abuse of legitimate remote access tools (NetSupport Manager, TeamViewer, etc.)
- Conduct regular phishing awareness training for users

For the full step-by-step response playbook, see [`runbook.md`](./runbook.md).

---

## Lessons Learned

- Wireshark is an essential blue team tool for extracting granular details beyond 
what SIEM alerts surface
- Protocol-specific filters (`kerberos.CNameString`, `nbns`) dramatically accelerate 
triage in time-sensitive incidents
- Attackers frequently abuse legitimate software — behavioral analysis and traffic 
baselining are critical detection layers
- Documenting filters and findings builds strong, repeatable incident response playbooks

---

## Conclusion

This Wireshark analysis successfully identified a NetSupport Manager RAT infection in 
a realistic SOC scenario. It demonstrates practical application of network forensics 
skills gained from IBM SkillsBuild Cybersecurity Fundamentals training and provides 
concrete evidence of blue team capability in threat detection and response.

---

## References

- IBM SkillsBuild: Cybersecurity Fundamentals
- [Malware-Traffic-Analysis.net](https://malware-traffic-analysis.net) – 2026-02-28 Exercise
- [Wireshark Official Documentation](https://www.wireshark.org/docs/)

---

## Related Files

| File | Description |
|---|---|
| [`iocs.md`](./iocs.md) | All extracted Indicators of Compromise |
| [`wireshark-filters.md`](./wireshark-filters.md) | Filters and methodology reference |
| [`runbook.md`](./runbook.md) | Incident response playbook |
| [`screenshots/`](./screenshots/) | All Wireshark evidence screenshots |
