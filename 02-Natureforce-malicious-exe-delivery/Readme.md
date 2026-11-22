# 02 - Natureforce (Malicious EXE Delivery)

**Source:** malware-traffic-analysis.net — "2026-10-01: Traffic Analysis Exercise: Natureforce"
**Environment:** Active Directory domain `natureforce.com` (NATUREFORCE), DC at `10.10.1.10`, LAN gateway `10.10.1.1`

---

## Findings

**Infected host:**
- IP address: `10.10.1.128`
- Hostname: `DESKTOP-4VGYQX7`
- MAC address: `00:22:fb:ec:7e:e7`
- Windows user account: `bdiaz`

**Malicious file delivered:**
- Delivered from: `107.175.82[.]242:9000`
- File name: `runner_ilove.exe`
- SHA-256 hash: `b789adce70c028a6d17be45ec34667aa95586350ee0c4057e75b138b1ad35061`

---

## Investigation Method

- **Host identification:** Filtered traffic to isolate the infected endpoint's IP and confirmed it fell within the documented LAN segment (10.10.1.0/24)
- **MAC address:** Expanded the Ethernet II layer on a frame from the identified host
- **Hostname:** Identified via SMB traffic from the host
- **Username:** Identified via Kerberos AS-REQ traffic, reading the `cname` field
- **Malicious file:** Located the TCP stream from `107.175.82.242:9000` to the infected client, exported the transferred object via File > Export Objects, and computed its SHA-256 hash directly for verification

---

## Indicators of Compromise (IOC)

| Type | Indicator |
|---|---|
| Infected host IP | 10.10.1.128 |
| Infected host MAC | 00:22:fb:ec:7e:e7 |
| Infected hostname | DESKTOP-4VGYQX7 |
| Compromised account | bdiaz |
| Payload delivery server | 107.175.82.242:9000 |
| Malicious file | runner_ilove.exe |
| SHA-256 hash | b789adce70c028a6d17be45ec34667aa95586350ee0c4057e75b138b1ad35061 |

---

## MITRE ATT&CK Mapping

- **T1105 — Ingress Tool Transfer:** Malicious executable (`runner_ilove.exe`) delivered directly to the host over a non-standard port (9000)
- **T1204 — User Execution:** Delivery pattern consistent with a user-triggered download/execution chain

---

## What I Learned

Unlike the Kongtuke case (which centered on social engineering via ClickFix), this exercise centered on direct executable delivery over a non-standard port, a reminder that malicious payloads aren't always disguised behind legitimate-looking protocols. Exporting the object directly from the TCP stream and independently computing its hash, rather than trusting a filename or extension, reinforced that file verification should always be done at the binary level, not assumed from surface details.
