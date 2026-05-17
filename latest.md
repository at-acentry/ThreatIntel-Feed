<!-- Acentry Daily Threat Intelligence Feed -->

<p align="center">
  <img src="assets/acentry-logo.png" alt="Acentry Security" width="220">
</p>

<h1 align="center">ACENTRY SECURITY</h1>
<h3 align="center">Daily Threat Intelligence Feed</h3>

---

**Date:** 2026-05-10
**Classification:** TLP:CLEAR — For Acentry Clients
**Analyst:** Acentry Threat Intelligence Team

> **Daily Brief.** The PAN-OS Captive Portal zero-day (**CVE-2026-0300**, CVSS 9.3) remains the most consequential issue in the field — Palo Alto Networks Unit 42 has confirmed in-the-wild exploitation by a likely state-sponsored cluster (CL-STA-1132) with no patch available until **May 13, 2026**. CISA added it to the KEV catalog on May 6 with a May 9 federal remediation deadline. In parallel, the **"Copy Fail" Linux kernel LPE (CVE-2026-31431)** offers deterministic root on virtually every Linux distro shipped since 2017, making it an extremely attractive container/multi-tenant escape primitive. Operators should treat any internet-exposed PAN-OS User-ID portal as compromised until proven otherwise and prioritize Linux kernel patching across cloud workloads this week.

## Executive Summary

- **CVE-2026-0300 (PAN-OS Captive Portal)** — actively exploited unauthenticated RCE as root; no patch yet; restrict portal exposure immediately and hunt for the published Unit 42 IOCs.
- **CVE-2026-31431 "Copy Fail" (Linux kernel)** — deterministic 4-byte write → root on essentially all distros since 2017; KEV-listed; FCEB deadline May 15, 2026.
- **CVE-2026-23918 (Apache HTTP/2)** — critical DoS / potential RCE; patch any HTTP/2-fronted stack.
- **CVE-2026-6973 (Ivanti EPMM)** — improper input validation; KEV-listed (May 7).
- **Salt Typhoon (China-nexus)** — continued telecom-sector espionage against US carriers (AT&T, Verizon reporting); call-metadata and intercept tradecraft persists into Q2 2026.
- **APT28 PROMPTSTEAL** — first observed live malware that queries an LLM (Qwen2.5-Coder via Hugging Face) to generate Windows commands at runtime; Mandiant urges a shift from IOCs to behavioral IOAs.

## Critical CVEs (Last 72h)

| CVE ID | CVSS | Vendor / Product | Description | Exploited | Patch / Mitigation | Source |
|--------|-----:|------------------|-------------|-----------|--------------------|--------|
| CVE-2026-0300 | 9.3 | Palo Alto Networks PAN-OS (User-ID Captive Portal) | Unauthenticated buffer overflow → root RCE on PA-Series & VM-Series firewalls. | Yes (KEV, CL-STA-1132) | No patch until 2026-05-13. Disable captive portal or restrict to trusted zones. ATP Threat ID 510019 (PAN-OS 11.1+). | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) · [PAN advisory](https://security.paloaltonetworks.com/CVE-2026-0300) · [CISA KEV](https://www.cisa.gov/news-events/alerts/2026/05/07/cisa-adds-one-known-exploited-vulnerability-catalog) |
| CVE-2026-31431 | 7.8 | Linux kernel (`algif_aead` in AF_ALG) | "Copy Fail" — deterministic LPE in cryptographic subsystem; 732-byte exploit → root on essentially all distros shipped since 2017. | Yes (KEV) | Apply distro kernel updates (Ubuntu, RHEL, SUSE, Amazon Linux all shipping). Interim: blacklist `algif_aead` or block AF_ALG socket creation. | [Unit 42](https://unit42.paloaltonetworks.com/cve-2026-31431-copy-fail/) · [Microsoft](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/) · [Red Hat](https://access.redhat.com/security/vulnerabilities/RHSB-2026-02) |
| CVE-2026-23918 | Critical | Apache HTTP Server (HTTP/2) | Flaw enabling denial-of-service and potential RCE in HTTP/2 handling. | Reported risk | Update to fixed Apache HTTP Server release; disable HTTP/2 if not required. | [The Hacker News](https://thehackernews.com/2026/05/critical-apache-http2-flaw-cve-2026.html) |
| CVE-2026-6973 | High | Ivanti Endpoint Manager Mobile (EPMM) | Improper input validation. | Yes (KEV, added 2026-05-07) | Apply Ivanti security update for EPMM per vendor advisory. | [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) |
| CVE-2026-32202 | High | Microsoft Windows | Actively exploited Windows component flaw added to KEV alongside ConnectWise issue. | Yes (KEV) | Apply current Microsoft monthly rollup. | [The Hacker News](https://thehackernews.com/2026/04/cisa-adds-actively-exploited.html) |
| CVE-2024-1708 | High | ConnectWise ScreenConnect | Path traversal / authentication bypass chain — re-added to KEV with renewed exploitation. | Yes (KEV) | Upgrade ScreenConnect to vendor-fixed build immediately; rotate all credentials. | [The Hacker News](https://thehackernews.com/2026/04/cisa-adds-actively-exploited.html) |
| CVE-2026-20127 | High | Cisco Catalyst SD-WAN Manager | Vulnerability under emergency patch advisory; targeted in critical infrastructure intrusions. | Yes | Apply Cisco emergency patch per PSIRT. | [Greenbone](https://www.greenbone.net/en/blog/emergency-patch-cve-2026-20127-in-cisco-catalyst-sd-wan-actively-exploited-against-critical-infrastructure/) |
| CVE-2026-20122 / 20128 / 20133 | High | Cisco Catalyst SD-WAN Manager | Three additional flaws added to CISA KEV in April. | Yes (KEV) | Apply Cisco PSIRT updates. | [CISA](https://www.cisa.gov/news-events/alerts/2026/04/20/cisa-adds-eight-known-exploited-vulnerabilities-catalog) |

## APT / Threat Actor Activity

### CL-STA-1132 (likely state-sponsored, China-nexus suspected)
- **Targets:** Internet-exposed Palo Alto Networks PAN-OS firewalls (PA-Series and VM-Series) running the User-ID Captive Portal.
- **TTPs:**
  - Initial access via **CVE-2026-0300** unauthenticated buffer overflow → shellcode injected into nginx worker (`T1190`, `T1059`).
  - Post-exploit deployment of open-source tunneling: **EarthWorm** and **ReverseSocks5** (`T1090`, `T1572`).
  - Active Directory enumeration using firewall service-account credentials targeting domain root and `DomainDnsZones` (`T1018`, `T1087.002`).
  - Aggressive log destruction: clearing kernel crash messages, deleting nginx crash entries/core dumps, removing `ptrace` injection evidence and SUID privilege-escalation binaries (`T1070.002`, `T1070.004`).
  - SAML flood used to fail-over to a second device and inherit traffic, then re-exploit (`T1499`).
- **Recent activity:** First exploitation observed 2026-04-09; confirmed RCE ~2026-04-16; secondary device compromise 2026-04-29.
- **Attribution confidence:** Medium — Unit 42 assesses likely state-sponsored based on tradecraft, target selection, and operational restraint.
- **Sources:** [Unit 42 threat brief](https://unit42.paloaltonetworks.com/captive-portal-zero-day/), [Cybersecurity Dive](https://www.cybersecuritydive.com/news/palo-alto-networks-state-linked-zero-day/819588/)

### Salt Typhoon (a.k.a. APT41 sub-cluster / Brass Typhoon, China-nexus)
- **Targets:** US telecommunications providers (AT&T, Verizon among reported victims), ISPs and lawful-intercept infrastructure.
- **TTPs:** Exploitation of network-edge devices for persistence; abuse of lawful-intercept platforms; credential and call-metadata exfiltration; long-dwell counterintelligence operations.
- **Recent activity:** 2024–2026 multi-campaign telecom intrusions; new Q1/Q2 2026 reporting indicates continued presence and re-targeting after disclosure.
- **Attribution confidence:** High — Microsoft, ESET and US government attribution to PRC state-sponsored activity.
- **Sources:** [Wikipedia: Salt Typhoon](https://en.wikipedia.org/wiki/Salt_Typhoon) · [Aviatrix research](https://aviatrix.ai/threat-research-center/salt-typhoon-2026-telecom-breach/) · [Congress CRS IF12798](https://www.congress.gov/crs-product/IF12798) · [Obsidian Security](https://www.obsidiansecurity.com/incident-watch/apt41-barium-targets-telecom-providers-via-kernel-level-backdoor-espionage-campaign)

### Volt Typhoon (China-nexus, critical-infrastructure pre-positioning)
- **Targets:** US communications, energy, water and transportation sector OT/IT.
- **TTPs:** Living-off-the-land (LOTL) on Windows; compromised SOHO routers and Fortinet edge devices used as proxies; long-term, low-noise persistence aimed at disruptive options rather than espionage.
- **Recent activity:** Continued use of EarthWorm and similar tunneling tools (overlap with CL-STA-1132 toolset).
- **Attribution confidence:** High (US government joint advisory).
- **Sources:** [MixMode](https://www.mixmode.ai/blog/volt-typhoon-salt-typhoon-apt41-this-is-no-longer-a-drill) · [NJCCIC](https://www.cyber.nj.gov/threat-landscape/nation-state-threat-analysis-reports/china-linked-cyber-operations-targeting-us-critical-infrastructure)

### APT28 / FROZENLAKE (Russia, GRU 26165) — PROMPTSTEAL
- **Targets:** Ukrainian government and military entities.
- **TTPs:** PyInstaller-packaged data-mining implant masquerading as image-generation software; queries the **Qwen2.5-Coder-32B-Instruct** model on Hugging Face at runtime to dynamically generate Windows commands for document theft (`T1059.003`, `T1041`). First publicly observed live deployment of LLM-querying malware.
- **Recent activity:** Tracked by Google Threat Intelligence Group through 2025–2026; companion to the experimental **PROMPTFLUX** Gemini-rewriter dropper.
- **Attribution confidence:** High (GTIG attribution).
- **Sources:** [Google Cloud — AI risk & resilience](https://cloud.google.com/security/resources/ai-risk-and-resilience) · [The Hacker News on PROMPTFLUX](https://thehackernews.com/2025/11/google-uncovers-promptflux-malware-that.html) · [The Record](https://therecord.media/new-malware-uses-ai-to-adapt)

## Indicators of Compromise (IOCs)

> Indicators below are presented un-defanged for direct ingestion into SIEM/EDR. Handle in an isolated environment. All IOCs in this section are sourced from the Unit 42 threat brief on CVE-2026-0300 ([source](https://unit42.paloaltonetworks.com/captive-portal-zero-day/)) unless otherwise noted.

### File Hashes (SHA-256)

| Hash | Family / Threat | First Seen | Source |
|------|-----------------|-----------:|--------|
| e11f69b49b6f2e829454371c31ebf86893f82a042dae3f2faf63dcd84f97a584 | EarthWorm tunneling tool (CL-STA-1132) | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |

### Network — IPs

| IP | Threat | First Seen | Source |
|----|--------|-----------:|--------|
| 67.206.213.86 | CL-STA-1132 infrastructure (PAN-OS exploitation) | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| 136.0.8.48 | CL-STA-1132 infrastructure | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| 146.70.100.69 | CL-STA-1132 C2 staging (EarthWorm download) | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| 149.104.66.84 | CL-STA-1132 infrastructure | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |

### Network — URLs

| Indicator | Threat | First Seen | Source |
|-----------|--------|-----------:|--------|
| http://146.70.100.69:8000/php_sess | EarthWorm payload retrieval | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| https://github.com/Acebond/ReverseSocks5/releases/download/v2.2.0/ReverseSocks5-v2.2.0-linux-amd64.tar.gz | ReverseSocks5 download (legitimate repo abused by CL-STA-1132) | 2026-04 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |

### Host — File-system Artifacts

| Path | Threat | Source |
|------|--------|--------|
| /var/tmp/linuxap | CL-STA-1132 tunneling-tool drop location | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| /var/tmp/linuxda | CL-STA-1132 tunneling-tool drop location | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| /var/tmp/linuxupdate | CL-STA-1132 tunneling-tool drop location | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| /tmp/.c | Unidentified Python script staged by CL-STA-1132 | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| /tmp/R5, /var/R5 | ReverseSocks5 binary location | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |

### HTTP / User-Agent

| Indicator | Threat | Source |
|-----------|--------|--------|
| `Safari/532.31 Mozilla/5.5 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36 Edg/138.0.0.0` | Attacker user-agent observed in PAN-OS exploitation (note malformed Safari version `532.31` and `Mozilla/5.5`) | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| Repeated HTTP POST to `/php/login.php` or `/User-ID/` with unusually long parameter values | CVE-2026-0300 exploitation attempt | [Wiz](https://www.wiz.io/blog/critical-vulnerability-in-pan-os-exploited-in-the-wild-cve-2026-0300) |

> **Note on broader IOC feeds:** ThreatFox and MalwareBazaar continue to publish high-volume daily IOC sets (see [ThreatFox 2026-05-09 feed](https://radar.offseq.com/threat/threatfox-iocs-for-2026-05-09-6a18791b)). For ransomware-tagged indicators, query the [ThreatFox Ransomware tag](https://threatfox.abuse.ch/browse/tag/Ransomware/) directly for live results.

## YARA Rules

The following rules detect tooling and exploitation artifacts associated with the CL-STA-1132 PAN-OS campaign. Test in your environment before promoting to blocking mode.

```yara
rule Acentry_CLSTA1132_EarthWorm_Tunneler
{
    meta:
        author      = "Acentry Threat Intelligence"
        date        = "2026-05-10"
        description = "Detects the EarthWorm SOCKS5 tunneling tool sample observed in the Unit 42 CL-STA-1132 / CVE-2026-0300 PAN-OS exploitation cluster."
        reference   = "https://unit42.paloaltonetworks.com/captive-portal-zero-day/"
        hash        = "e11f69b49b6f2e829454371c31ebf86893f82a042dae3f2faf63dcd84f97a584"
        tlp         = "CLEAR"
    strings:
        $s1 = "rssocks" ascii
        $s2 = "lcx_listen" ascii
        $s3 = "lcx_slave" ascii
        $s4 = "lcx_tran" ascii
        $s5 = "EarthWorm" ascii nocase
    condition:
        uint32(0) == 0x464C457F        // ELF magic
        and filesize < 5MB
        and 3 of ($s*)
}

rule Acentry_CLSTA1132_PANOS_Drop_Paths
{
    meta:
        author      = "Acentry Threat Intelligence"
        date        = "2026-05-10"
        description = "Hunts for shell scripts or staging artifacts that reference the Unit 42-published drop paths used by CL-STA-1132 on compromised PAN-OS firewalls."
        reference   = "https://unit42.paloaltonetworks.com/captive-portal-zero-day/"
        tlp         = "CLEAR"
    strings:
        $p1 = "/var/tmp/linuxap" ascii
        $p2 = "/var/tmp/linuxda" ascii
        $p3 = "/var/tmp/linuxupdate" ascii
        $p4 = "/tmp/R5" ascii
        $p5 = "/var/R5" ascii
        $p6 = "146.70.100.69:8000/php_sess" ascii
    condition:
        any of them
}
```

## Recommended Actions

- [ ] **SOC — Hunt CVE-2026-0300:** Search edge logs (firewall/proxy/WAF) for inbound POSTs to `/php/login.php` and `/User-ID/` with abnormally long parameters; correlate against the four Unit 42 IPs and the malformed-Safari user-agent string.
- [ ] **SOC — Detection:** Deploy the two YARA rules above to EDR / file-server scanning. Add `e11f69b4...a584` and the four CL-STA-1132 IPs to deny-list / block-list policies.
- [ ] **SOC — AD telemetry:** Hunt for unusual `DomainDnsZones` enumeration and service-account logons originating from firewall management plane IP space in the last 30 days.
- [ ] **IT / Patch Mgmt — PAN-OS:** Disable the User-ID Authentication Portal where not strictly required; restrict to trusted internal source IPs; enable Threat ID 510019 (Advanced Threat Prevention) on PAN-OS 11.1+. Schedule the 2026-05-13 hotfix for emergency deployment.
- [ ] **IT / Patch Mgmt — Linux:** Push CVE-2026-31431 (Copy Fail) kernel updates across all server/workstation/container-host fleets this week; for systems that cannot patch immediately, blacklist the `algif_aead` module via `/etc/modprobe.d/`. FCEB deadline 2026-05-15.
- [ ] **IT / Patch Mgmt — Other:** Apply Ivanti EPMM patch (CVE-2026-6973), ConnectWise ScreenConnect upgrade (CVE-2024-1708), and Cisco SD-WAN Manager PSIRT updates (CVE-2026-20122 / 20127 / 20128 / 20133). Patch Apache HTTP Server (CVE-2026-23918) on any HTTP/2-fronted service.
- [ ] **Leadership / Client Comms:** Notify clients running internet-facing PAN-OS firewalls of the active exploitation, the patch ETA, and the interim mitigation. Recommend a same-day compromise assessment for any portal that has been exposed since 2026-04-09.
- [ ] **Strategic:** Review detection strategy for AI-enabled malware (PROMPTFLUX / PROMPTSTEAL): supplement static IOC matching with behavioral IOAs (process spawning patterns, outbound LLM-API traffic, dynamically generated command strings).

## Sources & References

1. [Unit 42 — Threat Brief: Exploitation of PAN-OS Captive Portal Zero-Day (CVE-2026-0300)](https://unit42.paloaltonetworks.com/captive-portal-zero-day/)
2. [Palo Alto Networks Security Advisory CVE-2026-0300](https://security.paloaltonetworks.com/CVE-2026-0300)
3. [CISA KEV — Catalog landing page](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
4. [CISA Alert (2026-05-07) — Adds CVE-2026-6973 to KEV](https://www.cisa.gov/news-events/alerts/2026/05/07/cisa-adds-one-known-exploited-vulnerability-catalog)
5. [CISA Alert (2026-05-01) — Adds CVE to KEV](https://www.cisa.gov/news-events/alerts/2026/05/01/cisa-adds-one-known-exploited-vulnerability-catalog)
6. [CISA Alert (2026-04-20) — Eight KEV additions](https://www.cisa.gov/news-events/alerts/2026/04/20/cisa-adds-eight-known-exploited-vulnerabilities-catalog)
7. [The Hacker News — CISA Adds Linux Root Bug CVE-2026-31431 to KEV](https://thehackernews.com/2026/05/cisa-adds-actively-exploited-linux-root.html)
8. [The Hacker News — CISA Adds ConnectWise + Windows Flaws to KEV](https://thehackernews.com/2026/04/cisa-adds-actively-exploited.html)
9. [The Hacker News — Critical Apache HTTP/2 Flaw (CVE-2026-23918)](https://thehackernews.com/2026/05/critical-apache-http2-flaw-cve-2026.html)
10. [The Hacker News — Palo Alto PAN-OS Flaw Under Active Exploitation](https://thehackernews.com/2026/05/palo-alto-pan-os-flaw-under-active.html)
11. [Unit 42 — "Copy Fail" CVE-2026-31431](https://unit42.paloaltonetworks.com/cve-2026-31431-copy-fail/)
12. [Microsoft Security Blog — Copy Fail (CVE-2026-31431)](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/)
13. [Red Hat — RHSB-2026-02 Cryptographic Subsystem Privilege Escalation](https://access.redhat.com/security/vulnerabilities/RHSB-2026-02)
14. [Help Net Security — Nine-year-old Linux kernel flaw enables LPE](https://www.helpnetsecurity.com/2026/04/30/copyfail-linux-lpe-vulnerability-cve-2026-31431/)
15. [Wiz Blog — Critical Buffer Overflow in PAN-OS Exploited In-the-Wild](https://www.wiz.io/blog/critical-vulnerability-in-pan-os-exploited-in-the-wild-cve-2026-0300)
16. [Cybersecurity Dive — Palo Alto warns state-linked cluster behind zero-day exploitation](https://www.cybersecuritydive.com/news/palo-alto-networks-state-linked-zero-day/819588/)
17. [Greenbone — Emergency Patch CVE-2026-20127 Cisco Catalyst SD-WAN](https://www.greenbone.net/en/blog/emergency-patch-cve-2026-20127-in-cisco-catalyst-sd-wan-actively-exploited-against-critical-infrastructure/)
18. [MixMode — Volt Typhoon, Salt Typhoon & APT41: This Is No Longer a Drill](https://www.mixmode.ai/blog/volt-typhoon-salt-typhoon-apt41-this-is-no-longer-a-drill)
19. [Aviatrix Threat Research — Salt Typhoon 2026 Telecom Breach](https://aviatrix.ai/threat-research-center/salt-typhoon-2026-telecom-breach/)
20. [Wikipedia — Salt Typhoon](https://en.wikipedia.org/wiki/Salt_Typhoon)
21. [Congress.gov CRS IF12798 — Salt Typhoon Hacks of Telecommunications Companies](https://www.congress.gov/crs-product/IF12798)
22. [Obsidian Security — APT41 / Barium telecom kernel-level backdoor](https://www.obsidiansecurity.com/incident-watch/apt41-barium-targets-telecom-providers-via-kernel-level-backdoor-espionage-campaign)
23. [NJCCIC — China-Linked Cyber Operations Targeting US Critical Infrastructure](https://www.cyber.nj.gov/threat-landscape/nation-state-threat-analysis-reports/china-linked-cyber-operations-targeting-us-critical-infrastructure)
24. [Google Cloud — AI Risk and Resilience: Mandiant special report](https://cloud.google.com/security/resources/ai-risk-and-resilience)
25. [The Hacker News — PROMPTFLUX uses Gemini AI to rewrite its code hourly](https://thehackernews.com/2025/11/google-uncovers-promptflux-malware-that.html)
26. [The Record — New malware uses AI to adapt during attacks](https://therecord.media/new-malware-uses-ai-to-adapt)
27. [Mandiant M-Trends 2026 — Google blog summary](https://blog.google/innovation-and-ai/infrastructure-and-cloud/google-cloud/m-trends-2026-report-cybersecurity/)
28. [CrowdStrike 2026 Global Threat Report findings](https://www.crowdstrike.com/en-us/blog/crowdstrike-2026-global-threat-report-findings/)
29. [ThreatFox — Live IOC database](https://threatfox.abuse.ch/)
30. [OffSeq Threat Radar — ThreatFox IOCs for 2026-05-09](https://radar.offseq.com/threat/threatfox-iocs-for-2026-05-09-6a18791b)

---

<sub>© Acentry Security · Daily Threat Intelligence Feed · 2026-05-10 · Generated automatically — verify before operational use.</sub>
