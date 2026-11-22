# PCAP Analysis — Kongtuke ClickFix Infection (Traffic Analysis Exercise)

Independent packet-level forensic analysis of a real-world Kongtuke ClickFix infection, using a public PCAP from malware-traffic-analysis.net. This project involved raw packet analysis with no pre-built alerts, identifying the infected host, user, and malicious infrastructure directly from traffic using Wireshark.

**Source:** malware-traffic-analysis.net — "2026-09-11: Traffic Analysis Exercise: Kongtuke Rebuke!"
**Scenario:** Kongtuke ClickFix activity against a domain-joined Windows host in an Active Directory environment
**Tool:** Wireshark

---

## Environment (provided by the exercise)

| Field | Value |
|---|---|
| LAN segment | 10.9.11.0/24 |
| Domain | overhands.org |
| AD environment name | OVERHANDS |
| Domain controller | 10.9.11.2 — WIN-GTWXC9UYSE4 |
| LAN gateway | 10.9.11.1 |

---

## Investigation & Findings

**1. Identifying the infected host**
Filtered for HTTP traffic and identified a connection to a suspicious external site, the pcap was scoped to only this host's traffic, confirming it as the infected endpoint.
- **IP address:** `10.9.11.135`

**2. MAC address**
Selected a packet from the identified IP and expanded the Ethernet II layer to read the source MAC address directly.
- **MAC address:** `08:d4:0c:7a:29:1e`

**3. Hostname**
Filtered for SMB traffic from the host; SMB session setup/negotiation packets revealed the machine's NetBIOS/host name.
- **Hostname:** `DESKTOP-6T17ZFM`

**4. Username**
Filtered for Kerberos traffic (`kerberos`), located the AS-REQ (Authentication Service Request) packet, and expanded it to read the `cname` (client name) field, the account requesting authentication.
- **Username:** `gmcdowell`

**5. Full name of the user**
Filtered for SAMR traffic (Security Account Manager Remote protocol), located a `QueryUserInfoResponse` packet, and expanded it to find the full display name associated with the account.
- **Full name:** `Gabriel McDowell`

**6. C2 traffic**
Identified the destination(s) the infected host communicated with following the initial infection, distinguishing beaconing/C2 behavior from normal browsing traffic.
- **C2 IP/domain:** `IP:86.106.87.134 DOMAIN:known[.]mom-nower[.]com`
- **Indicators used to identify it as C2:** `repeated connections, suspicious domain`

---

## Indicators of Compromise (IOC)

| Type | Indicator |
|---|---|
| Infected host IP | 10.9.11.135 |
| Infected host MAC | 08:d4:0c:7a:29:1e |
| Infected hostname | DESKTOP-6T17ZFM |
| Compromised account | gmcdowell (Gabriel McDowell) |
| C2 destination | 86.106.87.134 |

---

## MITRE ATT&CK Mapping

- **T1204 — User Execution:** Victim was socially engineered via a fake CAPTCHA/verification page (ClickFix technique) into manually executing attacker-provided instructions
- **T1059 — Command and Scripting Interpreter:** Execution carried out via a pasted script/command following the ClickFix lure
- **T1071 — Application Layer Protocol:** C2 communication observed over HTTP, blending in with normal web traffic

---

## What I Learned

This was my first project in my portfolio built entirely from raw packet capture, with no SIEM alert, no pre-built detection rule, just traffic and manual investigation. Key takeaways:

- **Host identification doesn't always come from the obvious protocols.** While IP, MAC, and hostname were found through straightforward means (Ethernet headers, SMB), identifying the actual user and their full name required digging into Active Directory authentication traffic itself, Kerberos AS-REQ for the username, and SAMR for the full display name. These are protocols rarely covered in beginner material, but are exactly what's present in real enterprise environments.
- **C2 traffic requires pattern recognition, not just signature matching.** Unlike my SOC Lab (where Wazuh/Suricata flagged things automatically), this exercise required manually reasoning about which traffic looked like genuine command-and-control behavior.
- This project reinforced a skill directly transferable to real SOC work: investigating a single IP from a raw capture through to full identification of host, user, and malicious infrastructure.

---

## Evidence

Screenshots (Ethernet/MAC view, SMB hostname packet, Kerberos AS-REQ cname field, SAMR QueryUserInfoResponse, exported files, C2 traffic view) are in [`screenshots/`](screenshots/).
