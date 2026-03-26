# Indicators of Compromise (IOCs)

**Incident Date:** 2026-02-28  
**Detection Time:** ~19:55 UTC  
**Threat:** NetSupport Manager RAT (Command & Control)

---

## Network IOCs

| Indicator | Type | Value | Notes |
|---|---|---|---|
| C2 Server | IP Address | `45.131.214.85` | External C2 server |
| C2 Port | TCP Port | `443` | Disguised as HTTPS traffic |
| Protocol Abuse | HTTP over TCP 443 | `application/x-www-form-urlencoded` | NetSupport RAT signature |

---

## Host IOCs

| Indicator | Type | Value | Notes |
|---|---|---|---|
| Infected Host | IP Address | `10.2.28.88` | Internal network: 10.2.28.0/24 |
| Infected Host | MAC Address | `00:19:d1:b2:4d:ad` | Intel NIC |
| Infected Host | Hostname | `DESKTOP-TEYQ2NR` | Identified via NBNS |
| Compromised User | Account Name | `brolf` | Extracted via kerberos.CNameString |
| Compromised User | Full Name | `Becka Rolf` | Extracted via SAMR query |

---

## Detection Source

- **SIEM Alert:** NetSupport Manager RAT signature hit
- **Confirmed via:** Manual Wireshark packet analysis
- **PCAP File:** `2026-02-28-traffic-analysis-exercise.pcap`
