# Incident Response Runbook — NetSupport Manager RAT

**Applies to:** NetSupport Manager RAT C2 infections detected via network traffic analysis  
**Based on:** 2026-02-28 SOC investigation

---

## Phase 1 — Immediate Containment

- [ ] Isolate the infected host (`10.2.28.88`) from the network immediately
- [ ] Do NOT power off the machine — preserve volatile memory if possible
- [ ] Notify the SOC team and escalate to IR lead
- [ ] Document the time of containment action

---

## Phase 2 — Block at the Perimeter

- [ ] Create a firewall rule blocking all outbound traffic to `45.131.214.85`
- [ ] Block TCP port 443 traffic to this IP specifically
- [ ] Check firewall logs for any other internal hosts communicating with the same IP
- [ ] Search proxy/DNS logs for any related domains resolved by the infected host

---

## Phase 3 — Credential Reset

- [ ] Immediately reset the password for account `brolf` (Becka Rolf)
- [ ] Audit all systems `brolf` had access to during and before the incident window
- [ ] Check for any lateral movement from `10.2.28.88` to other internal hosts
- [ ] Disable the account temporarily if lateral movement is suspected

---

## Phase 4 — Persistence Scan (on the infected endpoint)

- [ ] Check scheduled tasks: `schtasks /query /fo LIST /v`
- [ ] Check registry run keys:
  - `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
  - `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- [ ] Check startup folders:
  - `C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`
- [ ] Search for NetSupport Manager binaries (`client32.exe`, `PCICAPI.dll`)
- [ ] Review recently installed software and recently modified files

---

## Phase 5 — Evidence Preservation

- [ ] Export the PCAP file and store securely
- [ ] Collect Windows Event Logs (System, Security, Application)
- [ ] Document all Wireshark findings with screenshots
- [ ] Record all IOCs in `iocs.md`
- [ ] Log all response actions with timestamps for the incident report

---

## Phase 6 — Long-Term Prevention

- [ ] Implement network segmentation to limit C2 reachability
- [ ] Enable TLS inspection or encrypted traffic analytics
- [ ] Deploy an EDR solution to detect RAT behavior at the host level
- [ ] Add NetSupport Manager binaries to application blocklist
- [ ] Conduct phishing awareness training for all users
- [ ] Set up alerts for HTTP traffic masquerading over TCP 443
