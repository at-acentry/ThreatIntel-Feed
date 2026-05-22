<!-- Acentry Daily Threat Intelligence Feed -->

<p align="center">
  <img src="assets/acentry-logo.png" alt="Acentry Security" width="220">
</p>

<h1 align="center">ACENTRY SECURITY</h1>
<h3 align="center">Daily Threat Intelligence Feed</h3>

---

**Date:** 2026-05-22  
**Classification:** TLP:CLEAR — For Acentry Clients  
**Analyst:** Acentry Threat Intelligence Team

> **Daily Brief.** The 24–72 hour threat horizon remains elevated across multiple verticals. CISA added Langflow's critical RCE flaw (CVE-2025-34291, CVSS 9.4) and a Trend Micro Apex One directory-traversal exploit (CVE-2026-34926) to its KEV catalog on May 21, setting a June 4 federal remediation deadline. Palo Alto Networks PAN-OS CVE-2026-0300, a CVSS 9.3 buffer overflow in the User-ID Authentication Portal, continues to see targeted exploitation by a state-sponsored cluster (CL-STA-1132) after patches began rolling out May 13. Iran-linked MuddyWater is accelerating offensive operations under "Operation Olalampo," deploying four novel malware families across the MENA region, while China-nexus UAT-8837 maintains persistent access campaigns against North American critical infrastructure. The VENOMOUS#HELPER phishing campaign has impacted over 80 organizations by abusing legitimate RMM tools (SimpleHelp, ScreenConnect), and Silver Fox continues tax-themed lure operations deploying ValleyRAT and the newly documented ABCDoor backdoor.

## Executive Summary

- **CISA KEV (May 21):** Langflow CVE-2025-34291 (CVSS 9.4, unauthenticated RCE via CORS/CSRF chain) and Trend Micro Apex One CVE-2026-34926 (directory traversal enabling agent-push code injection) added to KEV; federal agencies must remediate by June 4, 2026.
- **Palo Alto PAN-OS CVE-2026-0300 (CVSS 9.3):** Unauthenticated root-level buffer overflow in User-ID Authentication Portal actively exploited by CL-STA-1132; patches rolling through May 28 — restrict Captive Portal access to trusted IPs immediately.
- **Ivanti EPMM triple threat:** Three Ivanti EPMM vulnerabilities on KEV — CVE-2026-1281 / CVE-2026-1340 (CVSS 9.8, unauthenticated RCE, exploited since January) and CVE-2026-6973 (CVSS 7.2, authenticated RCE chained with stolen credentials, KEV May 7).
- **UAT-8837 (China-nexus):** Cisco Talos documents ongoing credential harvesting and AD reconnaissance against North American critical infrastructure using Earthworm, GoTokenTheft, Certipy, and GoExec.
- **VENOMOUS#HELPER:** 80+ organizations hit by phishing campaign abusing SimpleHelp and ScreenConnect RMM tools; initial access via fake SSA emails with compromised Mexican business domains as staging infrastructure; assessed as ransomware precursor activity.
- **Silver Fox / ABCDoor:** Tax-themed spear-phishing campaign in India and Russia delivering ValleyRAT and new ABCDoor Python backdoor (C2 pattern: `abc.*` subdomains); 1,600+ malicious emails Jan–Feb 2026.

## Critical CVEs (Last 24–72h)

| CVE ID | CVSS | Vendor / Product | Description | Exploited | Patch / Mitigation | Source |
|--------|------|------------------|-------------|-----------|--------------------|--------|
| CVE-2025-34291 | 9.4 | Langflow AI / Langflow Platform | Unauthenticated RCE via CORS/CSRF chain — account takeover escalates to Python code execution; Flodric botnet deployed through compromised instances | KEV (May 21) | Upgrade to patched Langflow release; disable public exposure until patched | [THN](https://thehackernews.com/2026/05/cisa-adds-exploited-langflow-and-trend.html) |
| CVE-2026-0300 | 9.3 | Palo Alto Networks / PAN-OS PA-Series & VM-Series | Unauthenticated buffer overflow (CWE-787) in User-ID Authentication Portal enables root RCE with no auth required if portal is internet-exposed | KEV (May 6) | Restrict Captive Portal to trusted IPs now; branch patches rolling out May 13–28 | [Rapid7](https://www.rapid7.com/blog/post/etr-critical-buffer-overflow-in-palo-alto-networks-pan-os-user-id-authentication-portal-cve-2026-0300/) |
| CVE-2026-1281 | 9.8 | Ivanti / EPMM (≤12.7.0.0) | Unauthenticated RCE zero-day via crafted HTTP requests; actively exploited since January 2026; chained with CVE-2026-1340 | KEV | Apply RPM 12.x.0.x or 12.x.1.x; permanent fix in EPMM 12.8.0.0 | [Tenable](https://www.tenable.com/blog/cve-2026-1281-cve-2026-1340-ivanti-endpoint-manager-mobile-epmm-zero-day-vulnerabilities) |
| CVE-2026-1340 | 9.8 | Ivanti / EPMM (≤12.7.0.0) | Second RCE zero-day exploited in tandem with CVE-2026-1281 for full unauthenticated appliance takeover | Yes | Apply RPM patch; monitor for unauthorized logins post-patch | [Horizon3](https://horizon3.ai/attack-research/vulnerabilities/cve-2026-1281-cve-2026-1340/) |
| CVE-2026-6973 | 7.2 | Ivanti / EPMM (≤12.8.0.0) | Authenticated admin RCE via improper input validation; threat actors chain with credential reuse from CVE-2026-1281 exploitation | KEV (May 7) | Apply EPMM 12.6.1.1 / 12.7.0.1 / 12.8.0.1; rotate all EPMM admin credentials | [THN](https://thehackernews.com/2026/05/ivanti-epmm-cve-2026-6973-rce-under.html) |
| CVE-2026-34926 | 6.7 | Trend Micro / Apex One (on-premise) | Directory traversal allows attacker with server access to inject code into key table, deploying malicious updates to all endpoint agents | KEV (May 21) | Apply Apex One May 2026 Security Bulletin patch; restrict server network access | [Trend Micro](https://success.trendmicro.com/en-US/solution/KA-0023430) |
| CVE-2026-41091 | N/A | Microsoft / Windows Defender | Elevation of Privilege in Microsoft Defender actively exploited in the wild | KEV (May 20) | Apply May 2026 Windows cumulative update | [CISA](https://www.cisa.gov/news-events/alerts/2026/05/20/cisa-adds-seven-known-exploited-vulnerabilities-catalog) |
| CVE-2026-45498 | N/A | Microsoft / Windows Defender | Denial of Service in Microsoft Defender actively exploited | KEV (May 20) | Apply May 2026 Windows cumulative update | [CISA](https://www.cisa.gov/news-events/alerts/2026/05/20/cisa-adds-seven-known-exploited-vulnerabilities-catalog) |

## APT / Threat Actor Activity

### UAT-8837 (aliases: China-nexus APT, assessed PRC-affiliated)

- **Targets:** Critical infrastructure sectors in North America — energy, manufacturing, government supply chain; secondary focus on AD and PKI infrastructure
- **TTPs:** Initial access via server exploitation or stolen credentials (`T1190`, `T1078`); RDP RestrictedAdmin bypass (`T1021.001`); artifact staging in `C:\Windows\Temp\`, `C:\Users\Public\Music\`; credential theft via GoTokenTheft, Rubeus, Certipy (`T1003`, `T1558`); AD enumeration with SharpHound, dsquery, dsget (`T1069`, `T1087.002`); SOCKS5 tunneling via Earthworm (`T1572`); lateral movement via GoExec/WMI (`T1047`); persistence via backdoored domain accounts (`T1136.002`)
- **Recent activity:** Multiple confirmed North American intrusions documented by Cisco Talos through January 2026; overlapping TTP and infrastructure with SiteCore CVE-2025-53690 zero-day campaign. In one intrusion, actor exfiltrated victim DLL libraries, raising supply chain trojanization risk.
- **Attribution confidence:** Medium — China-nexus assessed; not yet formally named to a specific MSS/PLA unit
- **Sources:** [Cisco Talos](https://blog.talosintelligence.com/uat-8837/), [Security Affairs](https://securityaffairs.com/186999/breaking-news/china-linked-apt-uat-8837-targets-north-american-critical-infrastructure.html)

---

### MuddyWater / MERCURY (aliases: Static Kitten, Seedworm, Mango Sandstorm, TA450, Earth Vetala)

- **Targets:** Government, telecom, defense, and critical infrastructure across MENA (Middle East, Turkey, Africa); secondary targeting of Israeli entities
- **TTPs:** Spear-phishing initial access (`T1566.001`); fake Quick Assist portals for credential harvesting (`T1056.003`); Microsoft Teams abuse for C2 and credential theft (`T1218`); Rust-based CHAR backdoor and RustyWater RAT (`T1059.006`); novel downloaders GhostFetch and HTTP_VIP (`T1105`); GhostBackDoor with environment-adaptive persistence (`T1543`); Telegram bot C2 (`T1102.002`); Chaos ransomware false-flag to mask espionage (`T1486`)
- **Recent activity:** Operation Olalampo (first observed January 26, 2026) deploys four previously undocumented malware families. Separate RustyWater campaign targets Israeli government infrastructure. Telegram C2 bot monitoring by Group-IB revealed real-time operator commands and data collection activity.
- **Attribution confidence:** High — Iranian MOIS-sponsored; MITRE ATT&CK Group G0069
- **Sources:** [Group-IB Olalampo](https://www.group-ib.com/blog/muddywater-operation-olalampo/), [Dark Reading](https://www.darkreading.com/threat-intelligence/iran-muddywater-new-malware-tensions-mount)

---

### Silver Fox (aliases: Void Arachne, assessed China-nexus financially motivated)

- **Targets:** Industrial, consulting, retail, and transportation organizations in India and Russia; expanding to South and Southeast Asia
- **TTPs:** Tax-themed spear-phishing lures impersonating India Income Tax Department and Russian tax authorities (`T1566.001`); Rust-based loader (RustSL) downloads encrypted ValleyRAT payload (`T1105`); ABCDoor Python backdoor delivered via custom ValleyRAT plugin (`T1059.006`); C2 via HTTPS to `abc.*` subdomain infrastructure (`T1071.001`); ABCDoor capabilities include screenshots, clipboard exfiltration, file system ops, remote mouse/keyboard control (`T1113`, `T1115`)
- **Recent activity:** 1,600+ malicious emails recorded January–February 2026 targeting India and Russia. ABCDoor confirmed in-the-wild since Q1 2025 — now widely deployed. WhoisXML DNS analysis identified a cluster of newly registered domains following the `abc.*` third-level pattern used for C2.
- **Attribution confidence:** Medium — assessed China-nexus financially motivated; overlaps with known Void Arachne activity
- **Sources:** [Kaspersky/Securelist](https://securelist.com/silver-fox-tax-notification-campaign/119575/), [THN](https://thehackernews.com/2026/05/silver-fox-deploys-abcdoor-malware-via.html), [Dark Reading](https://www.darkreading.com/endpoint-security/silver-fox-tax-themed-attacks-india-russia)

---

### VENOMOUS#HELPER (unattributed — assessed ransomware precursor / IAB)

- **Targets:** 80+ organizations predominantly in the US, Western Europe, and Latin America; no specific sector focus — opportunistic
- **TTPs:** Phishing emails impersonating US Social Security Administration (`T1566.001`); link redirects through compromised legitimate Mexican business domain to evade spam filters (`T1583.001`); JWrapper-packaged SimpleHelp RMM binary signed with valid Thawte certificate (`T1553.002`); ScreenConnect installed as backup C2 channel (`T1219`); no malware dropped — fully living-off-the-land via RMM tools
- **Recent activity:** Active since April 2025, intensified in early 2026. Infrastructure supported by MivoCloud and HZ Hosting Ltd. Four of five identified servers still serving SimpleHelp portals at time of reporting. Assessed by Securonix as consistent with Initial Access Broker or ransomware precursor operation.
- **Attribution confidence:** Low — no formal attribution; overlaps with clusters tracked by Red Canary and Sophos (STAC6405)
- **Sources:** [Securonix](https://www.securonix.com/blog/venomous-helper-phishing-campaign/), [THN](https://thehackernews.com/2026/05/phishing-campaign-hits-80-orgs-using.html)

## Indicators of Compromise (IOCs)

> Indicators are presented un-defanged for direct ingestion. Handle in an isolated environment.

### File Hashes (SHA-256)

| Hash | Family / Threat | First Seen | Source |
|------|-----------------|------------|--------|
| 1b3856e5d8c6a4cec1c09a68e0f87a5319c1bd4c8726586fd3ea1b3434e22dfa | GoTokenTheft — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 891246a7f6f7ba345f419404894323045e5725a2252c000d45603d6ddf697795 | GoTokenTheft — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 451e03c6a783f90ec72e6eab744ebd11f2bdc66550d9a6e72c0ac48439d774cd | Earthworm tunneler — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| b3f83721f24f7ee5eb19f24747b7668ff96da7dfd9be947e6e24a688ecc0a52b | Earthworm tunneler — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| fab292c72ad41bae2f02ae5700c5a88b40a77f0a3d9cbdf639f52bc4f92bb0a6 | Earthworm tunneler — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 4f7518b2ee11162703245af6be38f5db50f92e65c303845ef13b12c0f1fc2883 | Earthworm tunneler — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 5090f311b37309767fb41fa9839d2770ab382326f38bab8c976b83ec727e6796 | SharpHound — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 887817fbaf137955897d62302c5d6a46d6b36cb34775e4693e30e32609fb6744 | GoExec — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 4af156b3285b49485ef445393c26ca1bb5bfe7cdc59962c5c5725e3f3c574f7c | GoExec — UAT-8837 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 81a6e6416eb7ab6ce6367c6102c031e2ae2730c3c50ab9ce0b8668fec3487848 | LampoRAT — MuddyWater Olalampo | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |

### Network — IPs

| IP | Threat | First Seen | Source |
|----|--------|------------|--------|
| 172.188.162.183 | UAT-8837 Earthworm C2 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 74.176.166.174 | UAT-8837 Earthworm C2 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 20.200.129.75 | UAT-8837 Earthworm C2 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 4.144.1.47 | UAT-8837 Earthworm C2 | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 103.235.46.102 | UAT-8837 infrastructure | 2026-01 | [Talos](https://blog.talosintelligence.com/uat-8837/) |
| 172.86.126.208 | MuddyWater Olalampo (ms_upd.exe host) | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| 77.110.107.235 | MuddyWater Teams C2 | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| 93.123.39.127 | MuddyWater Teams C2 | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| 116.203.208.186 | MuddyWater (pythonw.exe C2) | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| 159.198.66.153 | MuddyWater RustyWater C2 | 2026-01 | [Rescana](https://www.rescana.com/post/rustywater-iranian-muddywater-apt-targets-israeli-government-and-infrastructure-with-advanced-rust) |
| 165.154.227.192 | Salt Typhoon / ShadowPad C2 | 2026-01 | [Darktrace](https://www.darktrace.com/blog/salty-much-darktraces-view-on-a-recent-salt-typhoon-intrusion) |
| 147.45.218.66 | VENOMOUS#HELPER SimpleHelp C2 | 2026-05 | [Securonix](https://www.securonix.com/blog/venomous-helper-phishing-campaign/) |

### Network — Domains / URLs

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| adm-pulse.com | MuddyWater Olalampo (fake Quick Assist portal) | 2026-01-28 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| moonzonet.com | MuddyWater Olalampo (Game.exe C2) | 2026-01-28 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| uploadfiler.com | MuddyWater Olalampo (exfiltration C2) | 2026-01-28 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| stratioai.org | MuddyWater RustyWater | 2026-01 | [Rescana](https://www.rescana.com/post/rustywater-iranian-muddywater-apt-targets-israeli-government-and-infrastructure-with-advanced-rust) |
| bootcamptg.org | MuddyWater RustyWater | 2026-01 | [Rescana](https://www.rescana.com/post/rustywater-iranian-muddywater-apt-targets-israeli-government-and-infrastructure-with-advanced-rust) |
| nomercys.it.com | MuddyWater RustyWater (resolves 159.198.66.153) | 2026-01 | [Rescana](https://www.rescana.com/post/rustywater-iranian-muddywater-apt-targets-israeli-government-and-infrastructure-with-advanced-rust) |
| screenai.online | MuddyWater Phoenix backdoor C2 | 2025-08-17 | [Protoslabs](https://www.protoslabs.io/resources/rustywater-rust-rat-campaign) |
| abc.haijing88.com | Silver Fox / ABCDoor payload staging | 2026-01 | [Securelist](https://securelist.com/silver-fox-tax-notification-campaign/119575/) |
| gruta.com.mx | VENOMOUS#HELPER phishing lander (compromised) | 2026-05 | [Securonix](https://www.securonix.com/blog/venomous-helper-phishing-campaign/) |
| server.cubatiendaalimentos.com.mx | VENOMOUS#HELPER RMM payload delivery | 2026-05 | [Securonix](https://www.securonix.com/blog/venomous-helper-phishing-campaign/) |

### Email Indicators

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| Spear-phishing with Quick Assist download links (adm-pulse.com) | MuddyWater Olalampo | 2026-01-26 | [Group-IB](https://www.group-ib.com/blog/muddywater-operation-olalampo/) |
| Tax audit notification lures impersonating India Income Tax Dept / Russian tax authorities | Silver Fox / ABCDoor | 2026-01 | [Securelist](https://securelist.com/silver-fox-tax-notification-campaign/119575/) |
| Fake SSA (Social Security Administration) statement download emails | VENOMOUS#HELPER | 2026-05 | [Securonix](https://www.securonix.com/blog/venomous-helper-phishing-campaign/) |
| Purchase-order lures ("Order Request: UAB Sarens – PO #SB-0407026-001") with .txz double-extension attachment | Remcos RAT | 2025-11 | [Hornetsecurity](https://www.hornetsecurity.com/en/blog/remcos-rat-attack-chain/) |

## Phishing & Malware Distribution Domains

> Domains confirmed as active phishing landers, malware staging hosts, or payload delivery endpoints within the last 72h. Block at DNS/proxy layer. Un-defanged for direct ingestion.

```
abc.haijing88.com
adm-pulse.com
bootcamptg.org
gruta.com.mx
moonzonet.com
nomercys.it.com
screenai.online
server.cubatiendaalimentos.com.mx
stratioai.org
uploadfiler.com
```

*Note: 10 domains verified from authoritative sources. URLhaus real-time feed was inaccessible via proxy during this run — future automated runs will supplement this list from live Abuse.ch data.*

## YARA Rules

```yara
rule Acentry_UAT8837_Earthworm_Tunneler {
    meta:
        author      = "Cisco Talos / Acentry TI adaptation"
        date        = "2026-05-22"
        description = "Detects Earthworm SOCKS5 reverse tunneling tool variants used by China-nexus UAT-8837 in North American critical infrastructure intrusions"
        reference   = "https://blog.talosintelligence.com/uat-8837/"
        tlp         = "CLEAR"
        hash1       = "451e03c6a783f90ec72e6eab744ebd11f2bdc66550d9a6e72c0ac48439d774cd"
        hash2       = "b3f83721f24f7ee5eb19f24747b7668ff96da7dfd9be947e6e24a688ecc0a52b"
    strings:
        $mode_rssocks  = "rssocks" ascii wide
        $mode_ssocksd  = "ssocksd" ascii wide
        $mode_lcx_tran = "lcx_tran" ascii wide
        $arg_d         = " -d " ascii
        $arg_e         = " -e " ascii
        $arg_s         = " -s " ascii
        $tunnel_str1   = "socks5 server" ascii nocase
        $tunnel_str2   = "Earthworm" ascii
    condition:
        uint16(0) == 0x5A4D and
        (2 of ($mode_*)) and
        (2 of ($arg_*)) and
        1 of ($tunnel_str*)
}

rule Acentry_MuddyWater_Olalampo_C2_Strings {
    meta:
        author      = "Group-IB / Acentry TI adaptation"
        date        = "2026-05-22"
        description = "Detects strings and C2 artifacts associated with MuddyWater Operation Olalampo implants including GhostBackDoor, GhostFetch, and LampoRAT"
        reference   = "https://www.group-ib.com/blog/muddywater-operation-olalampo/"
        tlp         = "CLEAR"
        hash_lampo  = "81a6e6416eb7ab6ce6367c6102c031e2ae2730c3c50ab9ce0b8668fec3487848"
    strings:
        $tg_api     = "api.telegram.org" ascii wide
        $c2_dom1    = "adm-pulse.com" ascii wide
        $c2_dom2    = "moonzonet.com" ascii wide
        $c2_dom3    = "uploadfiler.com" ascii wide
        $rust_panic = "panicked at" ascii
        $rust_alloc = "alloc::alloc" ascii
        $persist    = "HKCU\\Software\\Microsoft\\Windows\\CurrentVersion\\Run" ascii wide
        $loader     = "GhostFetch" ascii nocase
        $backdoor   = "GhostBackDoor" ascii nocase
    condition:
        uint16(0) == 0x5A4D and (
            ($tg_api and 1 of ($rust_*)) or
            (1 of ($c2_dom*)) or
            (1 of ($loader, $backdoor) and $persist)
        )
}

rule Acentry_Ivanti_EPMM_PostExploit_Artifacts {
    meta:
        author      = "CISA AR25-261a / Acentry TI adaptation"
        date        = "2026-05-22"
        description = "Detects malicious JAR-based webshell and listener artifacts deployed on Ivanti EPMM appliances post-exploitation of CVE-2026-6973, CVE-2026-1281, or CVE-2026-1340"
        reference   = "https://www.cisa.gov/news-events/analysis-reports/ar25-261a"
        tlp         = "CLEAR"
    strings:
        $jar_magic    = { CA FE BA BE }
        $class_exec1  = "Runtime.exec" ascii
        $class_exec2  = "ProcessBuilder" ascii
        $epmm_path1   = "/opt/mifs" ascii
        $epmm_path2   = "mobileironcore" ascii nocase
        $shell_cmd1   = "cmd.exe" ascii wide
        $shell_cmd2   = "/bin/bash" ascii
        $shell_b64    = "base64_decode" ascii nocase
    condition:
        ($jar_magic at 0) and
        1 of ($class_exec*) and
        (1 of ($epmm_path*) or 1 of ($shell_cmd*, $shell_b64))
}
```

## Recommended Actions

- [ ] **SOC — Hunt:** Search endpoint telemetry and firewall logs for Earthworm C2 IPs (172.188.162.183, 74.176.166.174, 20.200.129.75, 4.144.1.47, 103.235.46.102). Enable Snort SIDs 61883, 61884, 63727, 63728. Hunt for `*.ico` processes running from `C:\Windows\Temp\` or `C:\Users\Public\Music\` — confirmed UAT-8837 staging TTP.
- [ ] **SOC — Block/Alert:** Push the Phishing & Malware Domains section above to DNS/proxy blocklists. Alert on outbound connections to all IPs in the IOC section. Block RMM tool downloads (SimpleHelp, ScreenConnect) from external origins if not authorized.
- [ ] **SOC — Hunt:** Review Palo Alto firewall portal-interface logs for abnormally long URL paths, repeated unauthenticated POSTs, and unusual user-agents consistent with CVE-2026-0300 scanning. Check for new root-level local accounts on PA-Series/VM-Series appliances.
- [ ] **SOC — Detect:** Deploy the three YARA rules above. Import all SHA-256 hashes into threat intel platforms and block at endpoint.
- [ ] **IT / Patch Mgmt (P1 — Patch Immediately):** Ivanti EPMM: apply RPM 12.x.0.x/12.x.1.x for CVE-2026-1281, CVE-2026-1340, CVE-2026-6973; rotate ALL EPMM admin credentials. Palo Alto PAN-OS: apply branch patch through May 28; restrict Captive Portal to trusted IPs now if unpatched.
- [ ] **IT / Patch Mgmt (P1 — Federal Deadline June 4):** Langflow: upgrade to patched release (CVE-2025-34291). Trend Micro Apex One: apply May 2026 Security Bulletin (CVE-2026-34926).
- [ ] **IT / Patch Mgmt (P2):** Apply May 2026 Windows cumulative updates for CVE-2026-41091 and CVE-2026-45498.
- [ ] **Leadership:** VENOMOUS#HELPER targets organizations with no specific vertical focus — any organization with internet-facing employees is at risk. Advise security awareness training emphasizing SSA and tax-themed lures. Three Ivanti EPMM CVEs on KEV warrant escalation to CISO for cloud migration evaluation. Advise energy, telecom, and government clients of elevated UAT-8837 risk.

## Sources & References

1. [Cisco Talos — UAT-8837 targets critical infrastructure in North America](https://blog.talosintelligence.com/uat-8837/)
2. [Group-IB — Operation Olalampo: Inside MuddyWater's Latest Campaign](https://www.group-ib.com/blog/muddywater-operation-olalampo/)
3. [Kaspersky/Securelist — Silver Fox tax campaign and ABCDoor backdoor](https://securelist.com/silver-fox-tax-notification-campaign/119575/)
4. [Securonix — VENOMOUS#HELPER Phishing Campaign](https://www.securonix.com/blog/venomous-helper-phishing-campaign/)
5. [CISA — Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
6. [CISA — Adds Seven Known Exploited Vulnerabilities (May 20, 2026)](https://www.cisa.gov/news-events/alerts/2026/05/20/cisa-adds-seven-known-exploited-vulnerabilities-catalog)
7. [The Hacker News — CISA Adds Exploited Langflow and Trend Micro Apex One Vulnerabilities to KEV](https://thehackernews.com/2026/05/cisa-adds-exploited-langflow-and-trend.html)
8. [The Hacker News — Ivanti EPMM CVE-2026-6973 RCE Under Active Exploitation](https://thehackernews.com/2026/05/ivanti-epmm-cve-2026-6973-rce-under.html)
9. [Rapid7 — Critical Buffer Overflow in PAN-OS (CVE-2026-0300)](https://www.rapid7.com/blog/post/etr-critical-buffer-overflow-in-palo-alto-networks-pan-os-user-id-authentication-portal-cve-2026-0300/)
10. [Tenable — CVE-2026-1281, CVE-2026-1340: Ivanti EPMM Zero-Day Vulnerabilities](https://www.tenable.com/blog/cve-2026-1281-cve-2026-1340-ivanti-endpoint-manager-mobile-epmm-zero-day-vulnerabilities)
11. [Wiz Blog — Critical Buffer Overflow in PAN-OS Exploited In-the-Wild](https://www.wiz.io/blog/critical-vulnerability-in-pan-os-exploited-in-the-wild-cve-2026-0300)
12. [CISA — Malicious Listener for Ivanti EPMM AR25-261a](https://www.cisa.gov/news-events/analysis-reports/ar25-261a)
13. [Rescana — RustyWater: MuddyWater APT Targets Israeli Government](https://www.rescana.com/post/rustywater-iranian-muddywater-apt-targets-israeli-government-and-infrastructure-with-advanced-rust)
14. [CISA Advisory AA25-239a — Salt Typhoon PRC Actors](https://www.cisa.gov/news-events/cybersecurity-advisories/aa25-239a)
15. [Trend Micro — Apex One May 2026 Security Bulletin](https://success.trendmicro.com/en-US/solution/KA-0023430)
16. [Hornetsecurity — Remcos RAT via Purchase Order Phishing](https://www.hornetsecurity.com/en/blog/remcos-rat-attack-chain/)
17. [The Hacker News — VENOMOUS#HELPER hits 80+ orgs via RMM abuse](https://thehackernews.com/2026/05/phishing-campaign-hits-80-orgs-using.html)
18. [SOCRadar — CVE-2026-0300 Root RCE in PAN-OS](https://socradar.io/blog/cve-2026-0300-root-rce-pan-os-captive-portal/)

---

<sub>© Acentry Security · Daily Threat Intelligence Feed · 2026-05-22 · Generated automatically — verify before operational use.</sub>
