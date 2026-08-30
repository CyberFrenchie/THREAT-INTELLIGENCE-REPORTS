# Sam Dalgleish — Threat Intelligence Research

Published threat intelligence research by **Sam Dalgleish** ([@CyberFrenchie](https://github.com/CyberFrenchie)), a cybersecurity researcher and threat intelligence analyst based in Paisley, Scotland.

Each report investigates malicious infrastructure, malware families, spyware attribution, and indicators of compromise (IOCs) using open-source tools and publicly available data.

**Live site:** [cyberfrenchie.github.io/THREAT-INTELLIGENCE-REPORTS](https://cyberfrenchie.github.io/THREAT-INTELLIGENCE-REPORTS/)

---

## Reports

| Report ID | Report | Threat | Date | Key IOC |
|-----------|--------|--------|------|---------|
| SDI-2026-001 | [Premium VPN Android — Confirmed Spyware](https://cyberfrenchie.github.io/THREAT-INTELLIGENCE-REPORTS/threat-intelligence/2026/02/22/premium-vpn-spyware-android.html) | Spyware / Active Data Exfiltration | Feb 2026 | `com.premium_vpn.mobile` |
| SDI-2025-002 | [Remcos RAT C2 Investigation](https://cyberfrenchie.github.io/THREAT-INTELLIGENCE-REPORTS/2025/03/05/45.74.46.39.html) | Remcos RAT C2 Infrastructure | Mar 2025 | `45.74.46.39` |
| SDI-2025-001 | [GHOST RAT C2 Investigation](https://cyberfrenchie.github.io/THREAT-INTELLIGENCE-REPORTS/2025/03/05/122.199.149.129.html) | GHOST RAT C2 Infrastructure | Mar 2025 | `122.199.149.129` |

---

## Methodology

Each report follows a structured intelligence workflow:

1. **Trigger** — IOC identified via threat feeds (ThreatFox, VirusTotal, etc.) or direct analysis
2. **Infrastructure Analysis** — WHOIS, Shodan, passive DNS, TLS certificate inspection
3. **Malware / Binary Analysis** — Static reverse engineering, behavioural indicators, sandbox output
4. **Attribution** — OSINT cross-referencing across multiple independent sources
5. **IOC Extraction** — IPs, domains, file hashes, Telegram artefacts
6. **Responsible Disclosure** — Reported to relevant platforms prior to or concurrent with publication

---

## Tools Used

- [Shodan](https://www.shodan.io) — Internet-wide port and service scanning
- [VirusTotal](https://www.virustotal.com) — Malware hash and URL analysis
- [ThreatFox](https://threatfox.abuse.ch) — IOC feed and malware intelligence
- [Blutter](https://github.com/worawit/blutter) — Dart AOT binary decompilation
- [jadx](https://github.com/skylot/jadx) — Android APK decompilation
- WHOIS / passive DNS — Infrastructure ownership and abuse contact lookup

---

## About Sam Dalgleish

Sam is a final-year BSc (Hons) Digital Security & Forensics student at Glasgow Caledonian University with practical experience in OSINT, vulnerability research, fuzzing, malware analysis, SIEM, and responsible disclosure. His published work includes CVE-2025-9548 and public investigations into spyware, RAT infrastructure, and live attack telemetry.

---

## Contact

- **LinkedIn:** [sam-dalgleish](https://linkedin.com/in/sam-dalgleish-334250306)
- **GitHub:** [@CyberFrenchie](https://github.com/CyberFrenchie)
- **LinkedIn:** [Sam Dalgleish](https://linkedin.com/in/sam-dalgleish-334250306)
