
This led to the discovery of an exposed Grafana 11.1.1 instance at `grafana.hifixie.com` (IP: `34.141.12.169`), sharing the same Google Cloud host as the primary API. Full findings documented in Finding 7.

---

## 7. Attribution Assessment

| Assessment | Confidence |
|------------|------------|
| `com.premium_vpn.mobile` is active spyware | **High** — live bot verification confirms exfiltration |
| Developer identity confirmed (withheld from public version) | **High** — confirmed across Dart binary, Telegram Bot API (3 endpoints) |
| Exfiltration is intentional, not accidental | **High** — rate limiting, MarkdownV2 formatting, and bypass rules are deliberate design decisions |
| Operator utilises Russian-hosted infrastructure | **Medium** — two domains on Russian hosting, Russian language throughout codebase. Motivation for hosting choice not confirmed. |

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
| Live verification | Telegram Bot API calls | Direct HTTP (hardcoded token) — token recovered from publicly distributed APK via static analysis; calls were limited to read-only verification (`getMe`, `getChat`, `getChatAdministrators`) and did not interact with user data |
| Infrastructure | Domain/IP enumeration | TLS certificate SAN, Shodan |
| Attribution | Cross-source correlation | Dart binary + Telegram API |

Blutter successfully decompiled the Dart AOT snapshot (`libapp.so`, 9.8MB, Dart 3.10.7), producing 59,576 lines of class/method pool data, 36,310 object pool entries, and 97 assembly files with decompiled pseudo-code.

---

## 10. Confidence Assessment

**Overall Confidence: High**

Attribution and exfiltration findings are corroborated across multiple independent sources: the compiled binary (build path), the Telegram Bot API (`getMe`, `getChat`, `getChatAdministrators`, `getMyShortDescription`), and the live unauthenticated settings API. The exfiltration bot was verified active at the time of analysis. The overall High confidence rating is qualified only by the inability to directly observe the rate-limited exfiltration mechanism in network transit; all other primary findings are corroborated across multiple independent sources.

---

## Responsible Disclosure

| Date | Action |
|------|--------|
| 2026-02-22 | Analysis completed and findings documented |
| 2026-02-22 | Reported to Google Play via the in-app report mechanism |
| 2026-02-22 | Hardcoded Firebase API key identified — separate disclosure to Google Firebase VRP not submitted; key was reported as part of the Google Play abuse report and has since been redacted in the published version of this report |
| 2026-03-09 | Report published — no response received from Google at time of publication |
| 2026-03-09 | Takedown request received from identified operator via personal email — bot token and Firebase key redacted as courtesy; operator attribution details withheld from public version on legal advice; report remains public |

## Post-Publication Update (13 March 2026)

**Developer Contact Received**  
The identified operator emailed the analyst claiming the exfiltration features and VPN bypass rules were implemented “by mistake.” He stated he has removed the malicious code and provided a revised APK for independent review.

**Verification Status**  
Confirmation of removal is **pending** full static and dynamic analysis of the new APK.  
The findings and IOCs in this report remain accurate for the originally analysed version (v1.5.5 build 2026020601).  
Users of the original app should still follow the remediation guidance in Section 8 until the new version is independently verified clean.

This report will be updated with re-analysis results once complete.

---

*SDI-2026-001 — Sam Dalgleish (CyberFrenchie) — February 22, 2026 (updated 13 March 2026)*  
*Research conducted for educational and public interest purposes. All analysis performed on a publicly distributed application using open-source tools.*
</DOCUMENT>
