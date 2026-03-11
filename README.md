# Cairn Intelligence — Threat Intelligence Research

Published threat intelligence research from **[Cairn Intelligence](https://cairnintelligence.com)** — a cybersecurity intelligence consultancy based in Paisley, Scotland. Research is conducted by Sam Dalgleish ([@CyberFrenchie](https://github.com/CyberFrenchie)), founder of Cairn Intelligence.

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

## About Cairn Intelligence

Cairn Intelligence provides threat intelligence services to Scottish SMEs — including monthly briefings (Cairn Watch), phishing triage, and infrastructure investigation. This research portfolio demonstrates the analytical capability behind those services.

Visit [cairnintelligence.com](https://cairnintelligence.com) to learn more.

---

## Contact

- **Website:** [cairnintelligence.com](https://cairnintelligence.com)
- **Email:** [sam@cairnintelligence.com](mailto:sam@cairnintelligence.com)
- **GitHub:** [@CyberFrenchie](https://github.com/CyberFrenchie)
- **LinkedIn:** [Sam Dalgleish](https://linkedin.com/in/sam-dalgleish-334250306)
