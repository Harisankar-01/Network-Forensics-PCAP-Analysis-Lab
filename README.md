# Network Forensics — PCAP Analysis Lab

Hands-on investigations of real-world sample PCAP files from malware-traffic-analysis.net, using Wireshark to identify infected hosts, extract IOCs, and trace command-and-control traffic. Each investigation is solved independently, without any answer keys, then verified against the published answer key afterward.

**Tool:** Wireshark
**Source:** malware-traffic-analysis.net (real-world captured malicious traffic, used for training exercises)

---

## Why This Lab

I built this lab to learn how to properly investigate a raw packet capture from scratch,and to get genuinely comfortable reading and reasoning through PCAP files. My other projects relied on tools like Wazuh and Suricata to surface suspicious activity automatically; this lab strips that away entirely, so the investigation itself, identifying a host, tracing a user, finding malicious traffic, has to come purely from manually analyzing the capture in Wireshark.

---

## Investigations

### 01 — Kongtuke Rebuke! (ClickFix Infection)
A Kongtuke ClickFix infection against a domain-joined Windows host. Identified the infected endpoint (IP, MAC, hostname), traced the compromised user account and full name via Kerberos and SAMR traffic, extracted the delivered payload and verified it via VirusTotal, and identified C2 communication.

[Full write-up →](01-kongtuke-clickfix/)

### 02 —Natureforece-malicious-exe-delivery
**Natureforce Incident — Brief Summary**

A Windows client on the `natureforce.com` Active Directory domain was compromised through direct delivery of a malicious executable, `runner_ilove.exe`, sent from an external server at `107.175.82.242` over port `9000`, a non-standard delivery port, unlike typical HTTP/HTTPS-based infections. The affected host (`DESKTOP-4VGYQX7`, user account `bdiaz`) was identified through standard host-fingerprinting techniques (SMB for hostname, Kerberos AS-REQ for the user account), and the delivered payload was extracted directly from the packet capture and verified via its SHA-256 hash. Unlike social-engineering-driven infections (such as ClickFix), this case centered on straightforward binary delivery to the endpoint, reinforcing the importance of monitoring unusual outbound/inbound ports and verifying any transferred executable against its hash rather than trusting its filename or source.

[Full write-up →](02-Natureforce-malicious-exe-delivery/)

### 03 — [Investigation name TBD]
*Coming soon*

---

## Methodology (applied to each investigation)

1. Identify the infected host — IP, MAC address, hostname
2. Identify the compromised user — account name and full identity where traceable (e.g., via Kerberos, SAMR, SMB)
3. Extract any transferred files and verify them via hash lookup (VirusTotal)
4. Identify command-and-control (C2) traffic and distinguish it from normal browsing behavior
5. Document IOCs, build a timeline, and map findings to MITRE ATT&CK
6. Compare findings against the published answer key to verify accuracy

---

## Skills Demonstrated

- Packet-level traffic analysis using Wireshark (protocol filtering, stream following, object export)
- Active Directory traffic analysis (Kerberos AS-REQ, SAMR) to trace user identity from raw packets
- Malware/file verification workflow (hash extraction, VirusTotal lookup)
- C2 traffic identification and pattern recognition
- IOC extraction and MITRE ATT&CK mapping
- Independent investigative methodology, verified against published answer keys for accuracy


## What I Learned

- How to investigate an incident from a raw packet capture.
- How to identify an affected endpoint and pull its core identifying details directly from traffic: IP address, MAC address, hostname, and the Associated User Account
- How to trace a user's full identity from protocol-level traffic (Kerberos AS-REQ, SAMR), not just surface-level metadata
- How to recognize malicious command-and-control (C2) traffic and distinguish it from normal browsing behavior
- How to extract suspicious files from a capture, calculate their hash values, and verify them against VirusTotal to confirm malicious intent
<div>
  <img src="https://img.shields.io/badge/-Wireshark-1679A7?&style=for-the-badge&logo=wireshark&logoColor=white" />
  <img src="https://img.shields.io/badge/-MITRE%20ATT%26CK-ED1C24?&style=for-the-badge&logo=mitre&logoColor=white" />
  <img src="https://img.shields.io/badge/-VirusTotal-394EFF?&style=for-the-badge&logo=virustotal&logoColor=white" />
</div>

