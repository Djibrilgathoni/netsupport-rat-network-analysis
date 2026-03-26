# Wireshark Filters & Analysis Methodology

**Exercise:** Malware-Traffic-Analysis.net – 2026-02-28 "Easy As 123"  
**Tool:** Wireshark (running on Kali Linux VM)  
**PCAP File:** 2026-02-28-traffic-analysis-exercise.pcap

---

## Display Filters

### 1. `ip.addr == 45.131.214.85`
**Purpose:** Isolate all traffic to/from the suspected C2 server.  
**What it revealed:** Heavy outbound communication from internal host `10.2.28.88` 
to external IP `45.131.214.85` over TCP port 443, confirming active C2 sessions.

---

### 2. `kerberos.CNameString`
**Purpose:** Extract user account names from Kerberos authentication packets.  
**What it revealed:** The compromised user account `brolf` (full name: Becka Rolf) 
actively authenticating from the infected host.

---

### 3. `nbns`
**Purpose:** Filter NetBIOS Name Service traffic to identify hostnames on the network.  
**What it revealed:** The infected host broadcasting its hostname as `DESKTOP-TEYQ2NR` 
from IP `10.2.28.88`.

---

### 4. Ethernet II Header Inspection
**Purpose:** Inspect the layer 2 source field of packets sent by the infected host.  
**What it revealed:** MAC address `00:19:d1:b2:4d:ad` belonging to the infected 
machine (manufacturer identified as Intel).

---

## Statistics Tools

### 5. Statistics → Conversations (IPv4)
**Purpose:** Get a ranked overview of all IP-to-IP communication volumes in the PCAP.  
**What it revealed:** `10.2.28.88` had the highest volume of traffic to `45.131.214.85`, 
confirming it as the primary infected host.

---

### 6. Statistics → Protocol Hierarchy
**Purpose:** View a breakdown of all protocols present in the capture.  
**What it revealed:** Anomalous HTTP traffic encapsulated within TCP 443 — a 
red flag since port 443 should carry HTTPS/TLS, not plaintext HTTP. This is a 
known NetSupport RAT behavior.

---

### 7. Statistics → IO Graph
**Purpose:** Visualize traffic volume over time to spot unusual spikes or patterns.  
**What it revealed:** Sustained, periodic traffic bursts consistent with C2 
beaconing behavior rather than normal user browsing patterns.

---

## Key Takeaways

| Filter / Method | What It Found |
|---|---|
| `ip.addr == 45.131.214.85` | Infected host IP: `10.2.28.88` |
| Ethernet II header | MAC address: `00:19:d1:b2:4d:ad` |
| `nbns` | Hostname: `DESKTOP-TEYQ2NR` |
| `kerberos.CNameString` | User account: `brolf` (Becka Rolf) |
| Protocol Hierarchy | HTTP over TCP 443 — anomalous |
| IO Graph | Periodic beaconing pattern confirmed |
