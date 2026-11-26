# 03 - Lumma in the Room-ah (Lumma Stealer Infection)

**Source:** malware-traffic-analysis.net — "2026-01-31: Traffic Analysis Exercise: Lumma in the Room-ah!"
**Scenario:** A SOC alert fired for "ET MALWARE Lumma Stealer Victim Fingerprinting Activity" on traffic from `153.92.1.49` over TCP port 80. Task: use the pcap to identify the infected host, the compromised user, and the C2 infrastructure.

---

## Findings

**Infected host:**
- IP address: `10.1.28.58`
- Hostname: `DESKTOP-ES9F3ML`
- MAC address: `00:21:5d:c8:0e:f2`
- Windows user account: `gwyatt`
- Full name of user: `Gabriel Wyatt`

**Lumma Stealer C2 traffic:**
- Destination: `153.92.1.49:80`
- Domain: `whitepepper.su`

---

## Investigation Method

- **Host identification:** Isolated traffic to/from `153.92.1.49`, the IP named in the originating SOC alert, and confirmed the internal source address
- **MAC address:** Expanded the Ethernet II layer on a frame from the identified host
- **Hostname:** Identified via SMB/NetBIOS traffic
- **Username:** Identified via Kerberos AS-REQ traffic, reading the `cname` field
- **Full name:** Identified via SAMR `QueryUserInfoResponse` traffic
- **C2 identification:** Filtered for HTTP/TLS traffic to `153.92.1.49`, observed a pattern of `Client Hello` (TLS) traffic on port 443 alternating with plaintext HTTP requests on port 80, including `GET /api/set_agent?id=...` and `POST /api/set_agent?id=...`, consistent with Lumma Stealer's fingerprinting/check-in behavior. The `Host` header on the HTTP requests revealed the domain `whitepepper.su`

---

## Indicators of Compromise (IOC)

| Type | Indicator |
|---|---|
| Infected host IP | 10.1.28.58 |
| Infected host MAC | 00:21:5d:c8:0e:f2 |
| Infected hostname | DESKTOP-ES9F3ML |
| Compromised account | gwyatt (Gabriel Wyatt) |
| C2 IP | 153.92.1.49 |
| C2 domain | whitepepper.su |

---

## MITRE ATT&CK Mapping

- **T1082 — System Information Discovery:** The `/api/set_agent?id=...` requests are consistent with Lumma Stealer's victim fingerprinting behavior, profiling the infected host before further action
- **T1071.001 — Application Layer Protocol: Web Protocols:** C2 communication conducted over standard HTTP/HTTPS (ports 80/443), blending in with normal web traffic
- **T1555 — Credentials from Password Stores:** Consistent with Lumma Stealer's known behavior as an information-stealing malware family targeting browser-stored credentials

---

## What I Learned

This case started from the analyst's perspective of a SOC alert (a named signature hit on a specific IP/port), closer to how a real investigation actually begins, rather than open-ended "find the infection" exercises. Recognizing the alternating pattern of TLS `Client Hello` and plaintext HTTP `GET`/`POST` requests to the same destination was the key to confirming genuine C2 check-in behavior, rather than normal background traffic. This reinforced that malware C2 traffic often doesn't stand out through encryption alone, plaintext HTTP requests carrying suspicious API-style paths (`/api/set_agent?id=`) were actually the clearer signal here.
