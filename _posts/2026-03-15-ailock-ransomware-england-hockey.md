---
layout: single
title: "Threat Intelligence Report: AiLock Ransomware — Double-Extortion Attack on England Hockey"
date: 2026-03-15
categories: [threat-intelligence]
tags: [Ransomware, DoubleExtortion, UK, AiLock, MembershipOrganisations, PostQuantum, DataBreach]
author_profile: true
---

<span style="background-color:#28a745;color:white;padding:4px 10px;border-radius:4px;font-weight:bold;">TLP:CLEAR</span> — This report may be freely shared without restriction.

---

## Report Metadata

| Field | Details |
|-------|---------|
| **Report ID** | SDI-2026-002 |
| **Date** | March 15, 2026 |
| **Analyst** | Sam Dalgleish, Cairn Intelligence |
| **Threat Category** | Ransomware / Double-Extortion |
| **Threat Actor** | AiLock |
| **Victim** | England Hockey (englandhockey.co.uk) |
| **Data Volume Claimed** | ~129GB |
| **Confidence Level** | **Medium** |
| **Classification** | TLP:CLEAR |

---

## 1. Executive Summary

On 12 March 2026, England Hockey — the national governing body for field hockey in England, overseeing 800+ affiliated clubs, approximately 150,000 registered members, 15,000 coaches, umpires, and officials — was listed as a victim on the AiLock ransomware group's Tor-based data leak site (DLS).

The threat actor claims to have exfiltrated approximately 129GB of data from England Hockey's systems prior to deploying ransomware. Under AiLock's standard double-extortion model, victims are given 72 hours to make initial contact and approximately five days to pay before stolen data is published publicly. England Hockey has confirmed it is investigating and is working with external specialists and law enforcement.

This is the second UK victim attributed to AiLock identified by Cairn Intelligence. The group has been active since at least April 2025 and has claimed 23 victims across multiple countries and sectors as of the date of this report. The use of post-quantum cryptography (NTRUEncrypt) in this ransomware variant is a notable technical development that warrants monitoring.

---

## 2. Threat Background

AiLock is a ransomware-as-a-service (RaaS) or single-operator ransomware group first documented by Zscaler researchers in 2025. The group operates a Tor-based data leak site and employs a double-extortion model — exfiltrating data before deploying encryption, then threatening to publish if ransom is not paid.

**Key characteristics:**
- Active since approximately April 2025
- 23 known victims as of 15 March 2026
- Average delay between attack and public claim: ~404 days, suggesting extended dwell times on victim networks before disclosure (Cairn Intelligence calculation from ransomware.live victim records, 15 March 2026)
- Aggressive negotiation timeline: 72-hour initial response window, ~5 days to payment before data release
- Targets organisations across Technology, Consumer Services, Manufacturing, Construction, and Public Sector

**Geographic targeting:**

| Country | Known Victims |
|---------|--------------|
| United States | 11 |
| United Kingdom | 2 |
| Germany | 1 |
| South Korea | 1 |
| Japan | 1 |
| Other | 7 |

**Initial access vectors (confirmed):**
- Compromised credentials
- Phishing campaigns
- Exploitation of unpatched software vulnerabilities

---

## 3. Indicators of Compromise (IOCs)

| Indicator | Type | Description | Confidence |
|-----------|------|-------------|------------|
| `.AILock` | File Extension | Appended to all encrypted files | High |
| `ReadMe[1].txt` | Ransom Note Filename | Dropped in all affected directories | High |
| `dhnsppqjaaa22lsqxl2tfhji4ca43743kubltnodvsft3hkvai77p6ad.onion` | Tor Onion Address | AiLock data leak site (DLS) | High |
| `AILock.yar` | YARA Rule | Available via ransomware.live — not reproduced here | Medium |
| MD5 / SHA256 hashes | File Hashes | Available via ransomware.live — not verified independently | Low |

> **Note:** Specific file hashes have not been independently verified by Cairn Intelligence at time of publication. Treat hash IOCs from third-party sources with medium confidence until corroborated against a confirmed sample.

---

## 4. Infrastructure Analysis

### 4.1 Data Leak Site

| Field | Value |
|-------|-------|
| **Onion Address** | `dhnsppqjaaa22lsqxl2tfhji4ca43743kubltnodvsft3hkvai77p6ad.onion` |
| **Server** | NGINX |
| **Network** | Tor |
| **Status** | Active as of 15 March 2026 |
| **Last victim listed** | 12 March 2026 |

The DLS follows a standard ransomware leak site pattern — victims are listed with a countdown timer and escalating data release threats. The use of NGINX on Tor infrastructure is consistent with other mid-tier ransomware operations.

### 4.2 Infrastructure Assessment

Standard passive enumeration techniques (WHOIS, passive DNS, certificate transparency logs) are not applicable to Tor onion addresses — there is no registrant data, upstream hosting provider, or DNS record to query. Cairn Intelligence searched for clearnet infrastructure associated with AiLock across VirusTotal, Shodan, and ransomware.live; no clearnet domains, IPs, or associated infrastructure were identified at time of publication.

The onion address has been cross-referenced against ransomware.live and confirmed as the active AiLock DLS. The NGINX server identification is sourced from ransomware.live metadata. No independent direct access to the DLS was performed.

AiLock's Tor-only posture represents a deliberate operational security decision consistent with more established ransomware operations and limits passive infrastructure analysis significantly. This is noted as an analytical limitation of this report.

---

## 5. Technical Analysis

### 5.1 Encryption Implementation

| Field | Value |
|-------|-------|
| **Symmetric Encryption** | ChaCha20 |
| **Key Encapsulation** | NTRUEncrypt (post-quantum) |
| **File Extension** | .AILock |
| **Ransom Note** | ReadMe[1].txt |

AiLock uses a hybrid encryption scheme combining ChaCha20 for fast file encryption with NTRUEncrypt for key protection. NTRUEncrypt is a post-quantum cryptographic algorithm designed to resist attacks from quantum computing. Its use in this ransomware variant is technically significant — it means decryption without the operator's private key is computationally infeasible using current methods, and is designed to remain so even against future quantum-capable adversaries.

**Practical implication:** Victims cannot rely on future decryption tools (as seen with some older ransomware families) — clean backups are the only reliable recovery path.

### 5.2 Attack Chain

> **Note:** The following attack chain represents the assessed methodology based on AiLock group TTPs documented across known victims. Steps confirmed specifically in the England Hockey incident are limited to data exfiltration (claimed) and DLS listing. All other steps are inferred from documented group behaviour and should be treated as assessed rather than confirmed.

1. **Initial Access** — assessed via compromised credentials, phishing, or unpatched vulnerabilities (group TTP — not confirmed for this incident)
2. **Lateral Movement** — assessed traversal of internal network to identify high-value data stores (group TTP — not confirmed for this incident)
3. **Data Exfiltration** — ~129GB claimed exfiltrated prior to encryption deployment (threat actor claim — unverified)
4. **Encryption Deployment** — ChaCha20+NTRUEncrypt encryption applied, .AILock extension appended, ReadMe[1].txt dropped (confirmed group TTP)
5. **Extortion** — victim listed on DLS with payment timeline; dual threat of operational disruption and data publication (confirmed — DLS listing observed 12 March 2026)

### 5.3 Double-Extortion Model

AiLock employs double-extortion: data is stolen before encryption is deployed. This means:

- **Backups do not fully mitigate the attack** — even if systems are restored, the stolen data remains in the threat actor's possession
- **Two separate harms** — operational disruption (encryption) and data exposure (publication threat)
- **Regulatory exposure persists** — a UK GDPR notification obligation may be triggered regardless of whether a ransom is paid or systems are restored

---

## 6. Relevance to UK Organisations

England Hockey is the second UK victim attributed to AiLock identified by Cairn Intelligence. The targeting of a UK membership organisation is notable — these bodies typically hold high volumes of personal data (names, addresses, dates of birth, contact details, payment records) across a large member base, creating significant ICO exposure in the event of a breach.

**Organisations at elevated risk share these characteristics:**
- Large member or customer databases
- Federated structures (clubs, branches, regional bodies) with varying IT maturity
- Dependence on a small central IT team or outsourced IT support
- Public-facing web portals or online registration systems
- UK GDPR obligations for member data

Under UK GDPR, a ransomware attack involving personal data exfiltration is almost certainly a reportable breach — organisations have 72 hours from becoming aware to notify the ICO. Failure to notify can result in enforcement action independent of the ransomware incident itself.

---

## 7. Recommended Actions

- **Enforce MFA on all remote access** — VPN, RDP, and web-based admin portals are primary initial access vectors. Multi-factor authentication significantly raises the cost of credential-based attacks.
- **Patch internet-facing systems promptly** — VPN appliances, remote desktop gateways, and web servers should be on current firmware and software versions. Prioritise CVEs rated Critical or High.
- **Implement offline or immutable backups** — backups must be isolated from the network and tested regularly. A backup that is reachable from an infected host is not a recovery path.
- **Deploy endpoint detection for ransomware indicators** — monitor for mass file extension changes, ReadMe[1].txt creation events, and unusual volume encryption activity.
- **Know your UK GDPR obligations** — if personal data is held, document your breach response process before an incident occurs. The 72-hour ICO notification window starts from the moment you become aware, not from the moment you complete your investigation.
- **Review what data you hold and where** — organisations that cannot answer "what personal data do we hold and where does it live?" are not positioned to respond effectively to a data exfiltration event.

---

## 8. Confidence Assessment

**Overall Confidence: Medium**

This report is based on open-source intelligence from published security research, ransomware tracking services, and media reporting. Cairn Intelligence has not independently analysed an AiLock malware sample or verified the specific file hashes listed on third-party platforms. Technical details (encryption algorithms, file extensions, ransom note filenames) are corroborated across multiple independent sources and are assessed as reliable.

The 129GB data volume figure is based solely on the threat actor's own claim and has not been independently verified.

---

## 9. Sources

- BleepingComputer — *England Hockey investigating ransomware data breach* (bleepingcomputer.com)
- eSecurity Planet — *AiLock Ransomware Claims England Hockey Data Breach* (esecurityplanet.com)
- ransomware.live — *AiLock group profile* (ransomware.live/group/AiLock)
- The Hockey Paper — *England Hockey investigating ransomware data attack* (thehockeypaper.co.uk)
- Rankiteo — *England Hockey Investigates Possible Data Breach by AiLock Ransomware Group* (blog.rankiteo.com)

---

*Cairn Intelligence — Paisley, Scotland — cairnintelligence.com — sam@cairnintelligence.com*
