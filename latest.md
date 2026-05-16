<!-- Acentry Daily Threat Intelligence Feed -->

<h1 align="center">ACENTRY SECURITY</h1>
<h3 align="center">Daily Threat Intelligence Feed</h3>

---

**Date:** 2026-05-14  
**Classification:** TLP:CLEAR — For Acentry Clients  
**Analyst:** Acentry Threat Intelligence Team

> **Daily Brief.** The dominant story 24 hours after May Patch Tuesday is a wave of fresh edge/identity disclosures landing on top of unfinished patch work: OPNsense shipped 26.1.8 last night to close an unauthenticated DHCP RCE (CVE-2026-45158, CVSS 9.1), Fortinet pushed five advisories the same evening — including a critical unauthenticated authorization bypass in FortiSandbox (CVE-2026-26083) and a separate FortiAuthenticator unauthenticated command-execution flaw (CVE-2026-44277) — and Composer 2.9.8 / 2.2.28 emergency releases address a credential-disclosure regression (GHSA-f9f8-rm49-7jv2) that was actively leaking live `GITHUB_TOKEN` values into GitHub Actions stderr until GitHub temporarily rolled back the new token format on the 13th. Meanwhile the **CVE-2026-31431 "Copy Fail" Linux KEV deadline is tomorrow (Friday, May 15)**, and the **CVE-2026-0300 PAN-OS** phased patch window opened yesterday and runs through May 28 — internet-facing PA-Series and VM-Series remain the top action item for the week. On the actor front, ANY.RUN/Quetzal Team published a full technical breakdown of DPRK Lazarus's new macOS-focused "Mach-O Man" kit (Telegram-delivered ClickFix lure, Go-based four-stage chain, Keychain and browser-credential theft), and BlueVoyant disclosed a separate, SEO-poisoning Microsoft Teams installer campaign delivering a new loader/backdoor codenamed "Lorem Ipsum" — both worth Windows and macOS hunt cycles today.

## Critical CVEs (Last 24–72h)

| CVE ID | CVSS | Vendor / Product | Exploited |
|--------|------|------------------|-----------|
| CVE-2026-45158 | 9.1 | Deciso / OPNsense (< 26.1.8) | No — public PoC, weaponization expected |
| CVE-2026-26083 | Critical | Fortinet / FortiSandbox (FG-IR-26-136) | No |
| CVE-2026-44277 | High–Critical | Fortinet / FortiAuthenticator | No |
| CVE-2026-0300 | 9.3 | Palo Alto Networks / PAN-OS (PA / VM-Series) | **Yes — limited, state-sponsored; CISA KEV** |
| CVE-2026-41089 | 9.8 | Microsoft / Windows Netlogon (DC) | No |
| CVE-2026-41096 | 9.8 | Microsoft / Windows DNS Client | No |
| CVE-2026-31431 | 7.8 | Linux Kernel / `algif_aead` | **Yes — CISA KEV (deadline May 15)** |
| CVE-2026-20127 | 10.0 | Cisco / Catalyst SD-WAN Controller & Manager | **Yes — UAT-8616; CISA ED 26-03; KEV** |
| GHSA-f9f8-rm49-7jv2 | Token disclosure | Composer (< 2.9.8 / 2.2.28 / 1.10.28) | **Yes — passive disclosure in CI logs** |

## APT / Threat Actor Activity

### Lazarus Group / "Mach-O Man" (DPRK)

- **Targets:** Fintech and crypto sector executives, developers, and decision-makers operating macOS; crypto exchange and DeFi infrastructure personnel. Lure abuses compromised Telegram contacts impersonating routine business meetings.
- **TTPs:** ClickFix Telegram lure (`T1566.003`) → user paste-and-execute (`T1204.002`) → ad-hoc `codesign` of fake Zoom/Teams/Meet bundles (`T1553.002`) → Go/Mach-O four-stage chain: `teamsSDK.bin` stager → `D1*.bin` profiler (`T1082`, `T1057`) → `minst2.bin` LaunchAgent persistence at `~/Library/LaunchAgents/com.onedrive.launcher.plist` (`T1543.001`) → `macrasv2` Keychain/browser stealer (`T1555.003`, `T1539`). Exfiltration over Telegram Bot API (`T1567.002`); C2 over HTTP on ports 8888 / 9999.

### UAT-8616 (Cisco SD-WAN cluster, Talos-tracked)

- **Targets:** Internet-facing Cisco Catalyst SD-WAN Manager and Controller instances. Talos characterizes victims as "high-value and critical infrastructure"; majority of exposed instances observed in the United States.
- **TTPs:** Pre-auth exploitation of CVE-2026-20127 peering-auth bypass (`T1190`) → deliberate firmware **downgrade** of the appliance (`T1562.001`) to expose CVE-2022-20775 LPE → root → restoration of original firmware version to mask the activity; admin-plane tooling injection (`T1059`, `T1505`); lateral movement into SD-WAN data plane and downstream enterprise environments.

### "Lorem Ipsum" Operator Cluster (BlueVoyant-tracked)

- **Targets:** Opportunistic — users searching for "Microsoft Teams" via search engines across at least six countries; confirmed US healthcare-sector victim, ongoing global SEO-poisoning operations.
- **TTPs:** SEO poisoning of trojanized Teams installers (`T1583.008`); MSI Run-key persistence under value `Microsoft Revo Health` (`T1547.001`); DLL sideloading via `Microsoft Teams Revo Helper ZnrAq.exe` loading malicious `msvcp140.dll` (`T1574.002`); substitution-cipher decoding and XOR-encrypted shellcode stubs (`T1027.013`); JFIF-disguised C2 traffic (`T1001`); per-victim UUID callback architecture.

### Salt Typhoon (PRC MSS — aliases: GhostEmperor, RedMike)

- **Targets:** Telecommunications providers, ISPs, internet exchange points, maritime/subsea infrastructure. Persistent compromise of US telecoms remains unresolved.
- **TTPs:** Initial access via unpatched edge appliances — Cisco IOS XE, Palo Alto NGFW, Fortinet FortiGate (`T1190`); living-off-the-land via PowerShell (`T1059.001`), WMIC (`T1047`), registry tunneling (`T1112`); custom Go-based SFTP client for staged exfiltration (`T1048.002`); persistent modification of provider-edge routers (`T1542`); lawful-intercept infrastructure abuse for metadata harvest (`T1119`).

## Indicators of Compromise

> Scoped to the critical and high CVEs above. Most listed CVEs are patch advisories without published in-the-wild atomic IOCs — indicators below are sourced from vendor advisories (Cisco, Palo Alto, CISA) and are predominantly behavioral. CVEs with no published indicators (FortiSandbox, FortiAuthenticator, Netlogon, DNS Client) are intentionally omitted.

| CVE | Type | Indicator |
|-----|------|-----------|
| CVE-2026-20127 | Auth-log | SD-WAN Manager/Controller log entry: `Accepted publickey for vmanage-admin` from unknown or unauthorized IP |
| CVE-2026-20127 | Auth-log | New / unrecognized SSH key in `vmanage-admin` `authorized_keys` |
| CVE-2026-20127 | Network | Inbound DTLS traffic to UDP/12346 (`vdaemon`) from non-peer hosts |
| CVE-2026-20127 | Network | NETCONF session over TCP/830 from non-management source IP |
| CVE-2026-20127 | Behavioral | Unsolicited firmware downgrade on SD-WAN Manager / Controller, followed by re-upgrade (UAT-8616 anti-forensics) |
| CVE-2026-0300 | Network | Oversized POST / abnormal `Content-Length` to PAN-OS User-ID Authentication Portal from untrusted zones |
| CVE-2026-0300 | Behavioral | Unexpected crash or restart of the User-ID Authentication Portal process |
| CVE-2026-0300 | Behavioral | New admin session on PA-Series / VM-Series management plane from unknown source |
| CVE-2026-31431 | Audit-log | `AF_ALG` socket creation by non-privileged process (`syscall=41 AND a0=AF_ALG`) |
| CVE-2026-31431 | Audit-log | `splice()` on an `AF_ALG` socket fd by unprivileged user |
| CVE-2026-31431 | Behavioral | Unexpected modification of cached image of a setuid binary (page-cache integrity drift) |
| CVE-2026-45158 | Network | Unauthenticated HTTP POST to OPNsense DHCP configuration endpoint from external / non-management IPs |
| GHSA-f9f8-rm49-7jv2 | CI log | GitHub Actions stderr containing plaintext `ghs_…` or `github_pat_…` token after a Composer command failure |
| GHSA-f9f8-rm49-7jv2 | CI log | Composer regex-validation exception message echoing a token-shaped string |

## Recommended Actions

- [ ] **SOC — Hunt:** Sweep macOS endpoints (fintech / crypto / executive cohort first) for Mach-O Man IOCs above; DNS resolutions to `update-teams.live` / `livemicrosft.com`; outbound HTTP to 172.86.113.102 or 144.172.114.220 (especially ports 8888/9999); Telegram API traffic (`api.telegram.org`) from non-developer endpoints.
- [ ] **SOC — Hunt:** On Windows, search for the Lorem Ipsum Run-key value `Microsoft Revo Health`, presence of `Microsoft Teams Revo Helper ZnrAq.exe`, and DLL-sideload patterns where `msvcp140.dll` loads from a non-system path.
- [ ] **SOC — Detect:** SIEM rules for AF_ALG socket creation by non-privileged processes, Cisco SD-WAN Manager admin-API requests from non-management networks, and PAN-OS User-ID Authentication Portal POSTs with unusually large `Content-Length` from untrusted zones.
- [ ] **IT / Patch — Today:** **OPNsense 26.1.8** on all internet-facing OPNsense; **Fortinet May 12** advisory train across FortiSandbox, FortiAuthenticator, FortiAP, FortiAnalyzer, FortiManager; **Composer 2.9.8 / 2.2.28 / 1.10.28** on every CI runner and developer workstation, then **rotate** any `GITHUB_TOKEN` / GitHub App installation token / PAT processed through Composer since the token-format rollout.
- [ ] **IT / Patch — This week:** Continue **PAN-OS CVE-2026-0300** phased rollout (through May 28). Push **May 2026 Windows Patch Tuesday** prioritizing Netlogon (CVE-2026-41089) and DNS Client (CVE-2026-41096). Verify **Linux Copy Fail (CVE-2026-31431)** patches before the **CISA KEV deadline tomorrow (May 15)**.
- [ ] **IT / Network — Today:** Audit external exposure of Cisco Catalyst SD-WAN Manager and Controller; restrict TCP 22 and 830 to administrative networks; verify CVE-2026-20127 patch level.
- [ ] **Leadership:** Two unauthenticated edge-product RCEs (OPNsense, FortiSandbox/FortiAuthenticator) shipped the same day as PAN-OS phased patch — extend change-management exception windows for firewall and identity-broker patching through end of May. Composer regression is a supply-chain credential-disclosure issue affecting build pipelines; track rotation as a hard-deadline remediation task.
- [ ] **Client Comms:** Crypto / fintech clients with macOS developer or executive populations should receive a targeted Mach-O Man advisory — pasted-terminal "fix" prompts from any meeting platform should be treated as compromise indicators, not technical glitches.

## Sources

1. [CISA — Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
2. [Microsoft — May 2026 Patch Tuesday (BleepingComputer)](https://www.bleepingcomputer.com/news/microsoft/microsoft-may-2026-patch-tuesday-fixes-120-flaws-no-zero-days/)
3. [Palo Alto Networks PSIRT — CVE-2026-0300](https://security.paloaltonetworks.com/CVE-2026-0300)
4. [Cisco Talos — UAT-8616 SD-WAN active exploitation](https://blog.talosintelligence.com/uat-8616-sd-wan/)
5. [Cisco Security Advisory — `cisco-sa-sdwan-rpa-EHchtZk`](https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-sdwan-rpa-EHchtZk)
6. [Tenable — Copy Fail (CVE-2026-31431) FAQ](https://www.tenable.com/blog/copy-fail-cve-2026-31431-frequently-asked-questions-about-linux-kernel-privilege-escalation)
7. [Packagist — Composer 2.9.8 / 2.2.28 advisory (GitHub Actions token disclosure)](https://blog.packagist.com/composer-2-9-8-and-2-2-28-fix-github-actions-token-disclosure-in-error-messages/)
8. [FortiGuard PSIRT (advisory index)](https://www.fortiguard.com/psirt)
9. [OPNsense 26.1.8 patch coverage — wz-it](https://wz-it.com/en/blog/opnsense-26-1-8-cve-2026-44194-45158-rce-patch-management/)
10. [Quetzal Team / Bitso — Mach-O Man primary report](https://quetzal.bitso.com/p/north-koreas-safari-hunting-for-rats)
11. [ANY.RUN — Lazarus "Mach-O Man" technical writeup](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/)
12. [BlueVoyant — Lorem Ipsum trojanized Microsoft Teams installers](https://www.bluevoyant.com/blog/lorem-ipsum-trojanized-microsoft-teams-installers-multi-stage-loader-backdoor)
13. [CISA AA25-239a — Salt Typhoon / PRC telecom advisory](https://www.cisa.gov/news-events/cybersecurity-advisories/aa25-239a)

---

<sub>© Acentry Security · Daily Threat Intelligence Feed · 2026-05-14 · Generated automatically — verify before operational use.</sub>
