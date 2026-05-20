<!-- Acentry Daily Threat Intelligence Feed -->

<h1 align="center">ACENTRY SECURITY</h1>
<h3 align="center">Daily Threat Intelligence Feed</h3>

---

**Date:** 2026-05-19  
**Classification:** TLP:CLEAR — For Acentry Clients  
**Analyst:** Acentry Threat Intelligence Team

> **Daily Brief.** Today's threat landscape is dominated by two converging priorities: an active DPRK-attributed macOS credential-theft campaign dubbed "Mach-O Man" that is targeting fintech and crypto executives via ClickFix social engineering, and a dense patch cycle following Microsoft's May 2026 Patch Tuesday (120 CVEs including three with CVSS ≥ 9.8). Three vulnerabilities currently on CISA's KEV catalog — Cisco Catalyst SD-WAN (CVSS 10.0), Fortinet FortiClient EMS, and the Linux "Copy Fail" kernel LPE — remain under active exploitation and warrant immediate attention. China-nexus actors Salt Typhoon and Volt Typhoon continue to sustain persistent access across telecom and critical infrastructure globally, while DragonForce ransomware added a U.S. healthcare provider and two European firms to its victim roster this week.

## Executive Summary

- **KEV Alert:** CISA KEV now lists Cisco SD-WAN CVE-2026-20182 (CVSS 10.0) — authentication bypass enabling full admin takeover; FCEB remediation deadline was May 17, 2026.
- **Linux "Copy Fail" (CVE-2026-31431):** Nine-year-old kernel LPE with public PoC affects virtually all Linux distributions shipped since 2017, including cloud and Kubernetes workloads. Patch to kernel ≥ 6.18.22 or disable `algif_aead`.
- **Lazarus "Mach-O Man":** Active DPRK macOS credential-theft kit distributed via fake Zoom/Teams/Meet meeting invites; steals browser secrets and macOS Keychain data and exfiltrates via Telegram. Verified IOCs and YARA rule below.
- **Microsoft May Patch Tuesday:** 120 CVEs patched including Windows DNS Client RCE (9.8), Netlogon RCE (9.8), and Dynamics 365 RCE (9.9) — no zero-days exploited in the wild at time of release.
- **DragonForce escalates healthcare targeting:** Claimed AdvancedHEALTH on May 16 with threat to release patient records; also hit Plan (telecom, Isle of Man) and Ingelan (engineering, Spain) on May 17.
- **Lazarus crypto theft continues:** ~$290M stolen from KelpDAO on April 18 attributed to Lazarus with high confidence; group has stolen >$500M in the past month alone.

## Critical CVEs (Last 24–72h)

| CVE ID | CVSS | Vendor / Product | Description | Exploited | Patch / Mitigation | Source |
|--------|------|------------------|-------------|-----------|--------------------|--------|
| CVE-2026-20182 | 10.0 | Cisco / Catalyst SD-WAN Controller & Manager | Authentication bypass via unauthenticated DTLS on UDP/12346 allows attacker to obtain admin privileges remotely. CISA KEV deadline was 2026-05-17. | Yes — KEV | Apply Cisco security advisory; update to fixed version | [THN](https://thehackernews.com/2026/05/cisco-catalyst-sd-wan-controller-auth.html) |
| CVE-2026-42898 | 9.9 | Microsoft / Dynamics 365 (on-premises) | RCE via improper code-generation control; authorized network attacker can execute arbitrary code. May Patch Tuesday. | No | Apply May 2026 Patch Tuesday cumulative update | [Tenable](https://www.tenable.com/blog/microsofts-may-2026-patch-tuesday-addresses-118-cves-cve-2026-41103) |
| CVE-2026-41096 | 9.8 | Microsoft / Windows DNS Client | Heap-based buffer overflow in DNS client; no auth or user interaction required. Remote attacker sends crafted DNS response to trigger RCE. | No | Apply May 2026 Patch Tuesday | [WindowsNews](https://windowsnews.ai/article/microsoft-patches-critical-dns-client-rce-cve-2026-41096-in-may-2026-patch-tuesday.418370) |
| CVE-2026-41089 | 9.8 | Microsoft / Windows Netlogon | Stack-based buffer overflow in Netlogon; unauthenticated attacker sends crafted network packet to domain controller to achieve RCE. | No | Apply May 2026 Patch Tuesday | [Vulert](https://vulert.com/blog/microsoft-patch-tuesday-may-2026-dns-netlogon-rce/) |
| CVE-2026-35616 | 9.1 | Fortinet / FortiClient EMS 7.4.5–7.4.6 | Improper access control; pre-auth API bypass enables privilege escalation and arbitrary command execution. KEV-listed (added 2026-04-06). | Yes — KEV | Apply emergency hotfix; full patch in v7.4.7 | [CyberScoop](https://cyberscoop.com/fortinet-forticlient-ems-zero-day-cve-2026-35616-hotfix-known-exploited/) |
| CVE-2026-31431 | 7.8 | Linux Kernel (all distros since 2017) | "Copy Fail" — LPE flaw in kernel crypto subsystem (`algif_aead`); public PoC available. Affects cloud Linux, Kubernetes clusters at scale. | Yes — KEV | Upgrade to kernel ≥ 6.18.22 / 6.19.12 / 7.0; or `modprobe -r algif_aead` as interim fix | [HelpNetSecurity](https://www.helpnetsecurity.com/2026/04/30/copyfail-linux-lpe-vulnerability-cve-2026-31431/) |
| CVE-2026-6973 | N/A | Ivanti / Endpoint Manager Mobile (EPMM) | Improper input validation; attackers leverage harvested admin credentials to exploit "a very limited number" of customers. KEV-listed. | Yes — KEV | Apply Ivanti EPMM patch per vendor advisory | [SecurityOnline](https://securityonline.info/ivanti-epmm-exploited-in-the-wild-cve-2026-6973-zero-day/) |
| CVE-2026-0300 | Critical | Palo Alto Networks / PAN-OS (Captive Portal) | Buffer overflow in User-ID Authentication/Captive Portal service; unauthenticated attacker sends crafted packets for RCE with root on PA-Series and VM-Series. Unit 42 tracking exploitation cluster CL-STA-1132 (likely state-sponsored). | Yes | Apply PAN-OS hotfix per Palo Alto PSIRT advisory | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |

## APT / Threat Actor Activity

### Lazarus Group (aliases: APT38, Hidden Cobra, Guardians of Peace, ZINC)

- **Targets:** Fintech, cryptocurrency exchanges, DeFi platforms, macOS enterprise environments (developers, executives)
- **TTPs:** ClickFix social engineering via Telegram (`T1566.002`), User Execution (`T1204`), LaunchAgent persistence (`T1543.001`), Keychain credential theft (`T1555`), exfiltration via Telegram Bot API (`T1567.002`), Go-based Mach-O binaries with ad-hoc code signing, self-deletion post-exfiltration (`T1070.004`)
- **Recent activity:** "Mach-O Man" macOS kit (discovered April 21, 2026 by Bitso Quetzal Team / Mauro Eldritch via ANY.RUN) is actively distributing a multi-stage Go-based stealer. Victims receive fake meeting invites via Telegram, execute a terminal command to "fix" a connection error, which drops `teamsSDK.bin` (stager) → profiler (`D1*.bin`) → persistence (`com.onedrive.launcher`) → `macrasv2` stealer. Browser secrets, cookies, and macOS Keychain data are archived to `user_ext.zip` and exfiltrated via an exposed Telegram bot. On April 18, KelpDAO was exploited for ~$290M; combined with the Drift exploit, Lazarus has stolen >$500M in the past month.
- **Attribution confidence:** High — Go build artifacts, RC4 key reuse, Telegram exfil channel, and infrastructure overlap with prior Lazarus ClickFax/BeaverTail campaigns corroborated by FBI, CISA, and multiple vendors
- **Sources:** [ANY.RUN / Mauro Eldritch](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/), [Dark Reading](https://www.darkreading.com/threat-intelligence/north-koreas-lazarus-targets-macos-users-clickfix), [CoinDesk](https://www.coindesk.com/tech/2026/04/22/lazarus-group-has-become-especially-dangerous-with-new-mach-o-man-attack-certik)

---

### Salt Typhoon (aliases: Earth Estries, FamousSparrow, GhostEmperor)

- **Targets:** Telecommunications providers, government agencies, defense contractors, technology companies — confirmed victims in 80+ countries
- **TTPs:** Exploitation of known CVEs in Sophos Firewalls, Cisco IOS XE WebUI, Ivanti Connect Secure, and Fortinet FortiClient EMS for initial access; living-off-the-land via PowerShell, WMIC, and Windows service creation (`T1059.001`, `T1543.003`); registry manipulation; persistent implants inside telecom routing infrastructure; blends into legitimate network traffic to evade EDR
- **Recent activity:** FBI confirmed in February 2026 that threats remain active and ongoing. Group has compromised >200 organizations globally, with a focus on intelligence collection from carrier-grade telecom gear. Salt Typhoon mandate is espionage/SIGINT collection rather than pre-positioning for disruption (contrast: Volt Typhoon). Basic configuration errors and known CVEs continue to provide initial entry points.
- **Attribution confidence:** High — attributed by FBI, CISA, NSA, and Five Eyes partners; corroborated by Mandiant and Microsoft Threat Intelligence
- **Sources:** [CISA Advisory AA24-038A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-038a), [Censys](https://www.censys.com/blog/the-persistent-threat-of-salt-typhoon-tracking-exposures-of-potentially-targeted-devices), [Vectra](https://www.vectra.ai/resources/vectra-ai-threat-briefing-salt-typhoon)

---

### Volt Typhoon (aliases: BRONZE SILHOUETTE, Vanguard Panda, Dev-0391)

- **Targets:** U.S. critical infrastructure — energy, water, transportation, communications; focused on pre-positioning for potential disruption rather than immediate data exfiltration
- **TTPs:** Valid accounts for initial access (`T1078`), LOLBins to avoid malware footprint (`T1218`), FRP client obfuscation (BrightmetricAgent.exe / SMSvcService.exe UPX-packed inside ScanLine), credential dumping (`T1003`), lateral movement, encrypted C2 channels (`T1573`). Avoids EDR detection by operating exclusively with built-in OS tools.
- **Recent activity:** 2026 analysis confirms continued active presence in U.S. critical infrastructure. Group is assessed to be building capability to disrupt or destroy critical infrastructure services in the event of a major conflict with the United States. CISA STIX IOC bundle available: `MAR-10448362.c1.v2.CLEAR_stix2.json`.
- **Attribution confidence:** High — attributed by Microsoft, CISA, FBI, NSA, and Five Eyes allies
- **Sources:** [Cybelangel](https://cybelangel.com/blog/volt-typhoon/), [Picus Security](https://www.picussecurity.com/resource/blog/volt-typhoon-living-off-the-land-cyber-espionage), [Corelight](https://corelight.com/blog/volt-typhoon-salt-typhoon-edr)

---

### DragonForce (aliases: FireFlame, FuryStorm — ransomware cartel)

- **Targets:** Healthcare, telecommunications, engineering, retail — global scope; operates as RaaS cartel absorbing LockBit/BlackCat/RansomHub affiliates
- **TTPs:** Phishing for initial access, red-team frameworks (Cobalt Strike), double extortion (encrypt + exfiltrate), Conti-linked encryptor variant (`T1486`), data leak site; 80% revenue share to affiliates
- **Recent activity (72h):** May 16 — claimed AdvancedHEALTH (U.S. healthcare), threatening release of patient records and internal documents. May 17 — claimed Plan (telecom, Isle of Man) and Ingelan (engineering, Spain). DragonForce previously executed high-profile attacks on UK retailers Marks & Spencer, Co-op, and Harrods in 2025 before evolving into a full RaaS cartel model.
- **Attribution confidence:** High — self-claimed on verified leak site; tracked by Intel 471, Darktrace, Group-IB
- **Sources:** [DeXpose / AdvancedHEALTH](https://www.dexpose.io/dragonforce-strikes-advancedhealth-in-ransomware-attack/), [Acronis](https://www.acronis.com/en/tru/posts/the-dragonforce-cartel-scattered-spider-at-the-gate/), [Darktrace](https://www.darktrace.com/blog/tracking-a-dragon-investigating-a-dragonforce-affiliated-ransomware-attack-with-darktrace)

## Indicators of Compromise (IOCs)

> Indicators are presented un-defanged for direct ingestion. Handle in an isolated environment.

### File Hashes (SHA-256)

| Hash | Family / Threat | First Seen | Source |
|------|-----------------|------------|--------|
| `871d8f92b008a75607c9f1feb4922b9a02ac7bd2ed61b71ca752a5bed5448bf3` | Lazarus / Mach-O Man — `teamsSDK.bin` stager (macOS, Go) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `85bed283ba95d40d99e79437e6a3161336c94ec0acbc0cd38599d0fc9b2e393c` | Lazarus / Mach-O Man — `macrasv2` stealer (macOS, Go) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `4b08a9e221a20b8024cf778d113732b3e12d363250231e78bae13b1f1dc1495b` | Lazarus / Mach-O Man — `minst2.bin` persistence module (macOS, Go) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `0f41fd82cac71e27c36eb90c0bf305d6006b4f3d59e8ba55faeacbe62aadef90` | Lazarus / Mach-O Man — `D1yCPUyk.bin` / `D1YrHRTg.bin` profiler (macOS, Go) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `a9562ab6bce06e92d4e428088eacc1e990e67ceae6f6940047360261b5599614` | Lazarus / Mach-O Man — `localencode` / `OneDrive` downloader | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `eb3eae776d175f7fb2fb9986c89154102ba8eabfde10a155af4dfb18f28be1b5` | Lazarus / Mach-O Man — `com.onedrive.launcher.plist` LaunchAgent | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| `34ff2f72c191434ce5f20ebc1a7e823794ac69bba9df70721829d66e7196b044` | Lazarus / BeaverTail — ClickFix dropper (Windows) | 2026-03 | [Sekoia](https://blog.sekoia.io/clickfake-interview-campaign-by-lazarus/) |

### Network — IPs

| IP | Threat | First Seen | Source |
|----|--------|------------|--------|
| 172.86.113.102 | Lazarus C2 — Mach-O Man kit (`/Onedrive`, `/localencode`, `/payload` endpoints) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| 144.172.114.220 | Lazarus C2 — Mach-O Man kit | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| 45.159.248.110 | Lazarus / BeaverTail stealer C2 (ClickFix Windows campaign) | 2026-03 | [Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/05/06/clickfix-campaign-uses-fake-macos-utilities-lures-deliver-infostealers/) |
| 193.36.38.237 | Lazarus / ClickFix — C2 data exfiltration endpoint | 2026-03 | [Sekoia ClickFake](https://blog.sekoia.io/clickfake-interview-campaign-by-lazarus/) |
| 188.34.195.44 | Lazarus — malicious infrastructure (ClickFix campaign) | 2026-03 | [Sekoia ClickFake](https://blog.sekoia.io/clickfake-interview-campaign-by-lazarus/) |

### Network — Domains / URLs

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| update-teams.live | Lazarus / Mach-O Man — typosquat used for payload delivery | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| livemicrosft.com | Lazarus / Mach-O Man — typosquat (missing 'o' in microsoft) | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| http://172.86.113.102/Onedrive | Lazarus — payload delivery URL | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| http://172.86.113.102/localencode | Lazarus — persistence payload URL | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| https://update-teams.live/teams | Lazarus — fake Teams platform initial lure URL | 2026-04-21 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |

### Email Indicators

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| Telegram messages impersonating Zoom/Teams/Meet with fake "connection fix" terminal instructions (from compromised contacts) | Lazarus / Mach-O Man — initial access vector | 2026-04 | [ANY.RUN](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/) |
| Cross-industry fake event invitation phishing emails with ZIP attachments delivering credential stealers targeting U.S. companies | ClickFix infostealer campaign (broader, non-Lazarus overlap) | 2026-05 | [ANY.RUN Phishing](https://any.run/cybersecurity-blog/us-fake-invitation-phishing/) |

## YARA Rules

```yara
rule Lazarus_MachOMan_macOS_Stealer_Kit {
    meta:
        author      = "Mauro Eldritch (BCA LTD / Bitso Quetzal Team) — adapted by Acentry TI"
        date        = "2026-04-21"
        description = "Detects components of the Lazarus 'Mach-O Man' macOS credential-theft kit: Go-based Mach-O binaries (teamsSDK.bin, macrasv2, minst2.bin, D1*.bin profiler) identified by Go runtime artifacts, geniex-client namespace strings, and self-destruction command sequences."
        reference   = "https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/"
        tlp         = "CLEAR"
        hash1       = "871d8f92b008a75607c9f1feb4922b9a02ac7bd2ed61b71ca752a5bed5448bf3"
        hash2       = "85bed283ba95d40d99e79437e6a3161336c94ec0acbc0cd38599d0fc9b2e393c"
    strings:
        $go_http    = "Go-http-client" ascii
        $geniex1    = "geniex-client/core" ascii
        $geniex2    = "geniex-client/protocol" ascii
        $geniex3    = "geniex_client_sleep_state" ascii
        $destruct   = "Die command received, initiating self-destruction" ascii
        $onedrive   = "com.onedrive.launcher" ascii
        $c2_path1   = "/localencode" ascii
        $c2_path2   = "/payload" ascii
        $hobocopy   = "hobocopy_%d" ascii
        $buildid    = "XSnX8a5Y1OweX0Ob6lfO" ascii
    condition:
        (uint32(0) == 0xFEEDFACF or uint32(0) == 0xCEFAEDFE) and
        (
            ($go_http and 2 of ($geniex1, $geniex2, $geniex3)) or
            ($destruct and $onedrive) or
            (3 of ($c2_path1, $c2_path2, $hobocopy, $buildid, $geniex1, $geniex2))
        )
}

rule Lazarus_ClickFix_HTML_FakeCaptcha {
    meta:
        author      = "Toni_Dujmovic (ReversingLabs) — adapted by Acentry TI"
        date        = "2026-03-18"
        description = "Detects ClickFix HTML lure pages that trick users into copying and executing malicious PowerShell via fake CAPTCHA/verification prompts. Used extensively by Lazarus Group and other threat actors in 2026 campaigns targeting macOS and Windows."
        reference   = "https://www.reversinglabs.com/blog/clickfix-yara-rule"
        tlp         = "CLEAR"
    strings:
        $ps_exec1   = "powershell" nocase ascii wide
        $ps_exec2   = "cmd.exe /c" nocase ascii
        $ps_exec3   = "mshta" nocase ascii
        $verify1    = "Verify you are human" nocase ascii
        $verify2    = "I'm not a robot" nocase ascii
        $verify3    = "CAPTCHA" nocase ascii
        $copy1      = "navigator.clipboard.writeText" ascii
        $copy2      = "document.execCommand('copy')" ascii
        $run_hint1  = "Press Win+R" nocase ascii
        $run_hint2  = "Open Run" nocase ascii
        $run_hint3  = "paste in terminal" nocase ascii
    condition:
        filesize < 2MB and
        (1 of ($ps_exec*)) and
        (1 of ($verify*)) and
        (1 of ($copy*)) and
        (1 of ($run_hint*))
}

rule DragonForce_Ransomware_Encryptor {
    meta:
        author      = "petstuk (GitHub) — adapted by Acentry TI"
        date        = "2025-06-01"
        description = "Detects DragonForce ransomware encryptor samples identified from MalwareBazaar corpus. DragonForce (aliases: FireFlame, FuryStorm) is a RaaS cartel active in healthcare, telecom, retail, and engineering verticals globally."
        reference   = "https://github.com/petstuk/DragonForceRansomwareYARA"
        tlp         = "CLEAR"
    strings:
        $ransom_ext = ".dragonforce_encrypted" ascii nocase
        $note1      = "DragonForce" ascii
        $note2      = "README_DRAGONFORCE" ascii nocase
        $onion1     = "dragonforce" ascii nocase
        $mutex1     = "DragonForce_Mutex" ascii
        $pdb1       = "dragonforce" ascii nocase
    condition:
        uint16(0) == 0x5A4D and
        (
            2 of ($ransom_ext, $note1, $note2, $mutex1) or
            ($onion1 and $pdb1) or
            (3 of them)
        )
}
```

## Recommended Actions

- [ ] **SOC — Hunt:** Search EDR/SIEM for macOS processes executing from `$TMPDIR` with Go User-Agent strings (`Go-http-client`), LaunchAgent plist creation under `~/Library/LaunchAgents/com.onedrive.*`, and outbound connections to 172.86.113.102 or 144.172.114.220. Deploy `Lazarus_MachOMan_macOS_Stealer_Kit` YARA to macOS fleet scanning.
- [ ] **SOC — Hunt:** Search for ClickFix HTML delivery via proxy/email gateway logs — look for pages containing `navigator.clipboard.writeText` combined with CAPTCHA-themed language. Deploy `Lazarus_ClickFix_HTML_FakeCaptcha` YARA to email and web proxy.
- [ ] **SOC — Hunt:** Run threat hunts for Volt Typhoon LOLBin patterns: `netsh`, `wmic`, `ntdsutil` invoked by unusual parent processes; FRP client binaries masquerading as legitimate software; lateral movement via WMI or SMB from non-standard hosts.
- [ ] **SOC — Detect:** Enable signatures for CVE-2026-20182 exploitation attempts against Cisco SD-WAN UDP/12346 (vdaemon service); alert on any unauthenticated authentication events in SD-WAN controller logs.
- [ ] **IT / Patch Mgmt — Priority 1:** Apply Cisco Catalyst SD-WAN patch for CVE-2026-20182 immediately (CVSS 10.0, actively exploited, KEV deadline already passed).
- [ ] **IT / Patch Mgmt — Priority 2:** Patch Linux "Copy Fail" (CVE-2026-31431) — upgrade kernel to ≥ 6.18.22 / 7.0 across all cloud and on-premises Linux workloads; `modprobe -r algif_aead` as emergency interim on hosts that cannot be immediately patched.
- [ ] **IT / Patch Mgmt — Priority 3:** Apply Fortinet FortiClient EMS hotfix for CVE-2026-35616 (versions 7.4.5–7.4.6 affected); full patch in 7.4.7.
- [ ] **IT / Patch Mgmt — Priority 4:** Deploy Microsoft May 2026 Patch Tuesday across all Windows environments, prioritizing internet-facing systems, domain controllers (CVE-2026-41089 Netlogon RCE), Dynamics 365 on-prem (CVE-2026-42898), and DNS clients (CVE-2026-41096).
- [ ] **IT / Patch Mgmt — Priority 5:** Apply PAN-OS hotfix for CVE-2026-0300 (Captive Portal RCE); restrict web management interface to trusted management CIDRs if patching is delayed.
- [ ] **IT — macOS Hardening:** Enforce policies preventing unsigned/ad-hoc signed binaries from executing in `$TMPDIR`; audit user LaunchAgents for unauthorized plist entries (`com.onedrive.launcher`); train staff on ClickFix social engineering patterns — particularly fake meeting-platform terminal prompts delivered via Telegram.
- [ ] **Leadership:** DragonForce claimed AdvancedHEALTH on May 16 — any clients in U.S. healthcare should assess third-party vendor exposure and ensure ransomware playbooks are current. The Lazarus crypto theft wave ($500M+ in 30 days) elevates risk for clients with crypto treasury or DeFi exposure. Recommend tabletop exercise for DPRK-attributed financial theft scenarios.

## Sources & References

1. [Lazarus "Mach-O Man" Malware: What CISOs Need to Know — ANY.RUN / Mauro Eldritch](https://any.run/cybersecurity-blog/lazarus-macos-malware-mach-o-man/)
2. [CISA Adds Cisco SD-WAN CVE-2026-20182 to KEV — The Hacker News](https://thehackernews.com/2026/05/cisco-catalyst-sd-wan-controller-auth.html)
3. [CISA Adds Linux Root Access Bug CVE-2026-31431 to KEV — The Hacker News](https://thehackernews.com/2026/05/cisa-adds-actively-exploited-linux-root.html)
4. [CVE-2026-31431 "Copy Fail" FAQ — Security Boulevard](https://securityboulevard.com/2026/04/copy-fail-cve-2026-31431-frequently-asked-questions-about-linux-kernel-privilege-escalation-vulnerability/)
5. [Nine-year-old Linux kernel flaw CVE-2026-31431 — Help Net Security](https://www.helpnetsecurity.com/2026/04/30/copyfail-linux-lpe-vulnerability-cve-2026-31431/)
6. [Fortinet FortiClient EMS Zero-Day CVE-2026-35616 — CyberScoop](https://cyberscoop.com/fortinet-forticlient-ems-zero-day-cve-2026-35616-hotfix-known-exploited/)
7. [Microsoft May 2026 Patch Tuesday: 120 Vulnerabilities — BleepingComputer](https://www.bleepingcomputer.com/news/microsoft/microsoft-may-2026-patch-tuesday-fixes-120-flaws-no-zero-days/)
8. [Microsoft May Patch Tuesday: Netlogon & DNS RCE — Vulert](https://vulert.com/blog/microsoft-patch-tuesday-may-2026-dns-netlogon-rce/)
9. [Ivanti EPMM CVE-2026-6973 Exploited in the Wild — Security Online](https://securityonline.info/ivanti-epmm-exploited-in-the-wild-cve-2026-6973-zero-day/)
10. [PAN-OS Captive Portal Zero-Day CVE-2026-0300 — Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/)
11. [Salt Typhoon TTPs, Detection, and Defense — Vectra AI](https://www.vectra.ai/resources/vectra-ai-threat-briefing-salt-typhoon)
12. [Volt Typhoon 2026: Still Active in US Critical Infrastructure — Cybelangel](https://cybelangel.com/blog/volt-typhoon/)
13. [PRC State-Sponsored Actors Compromise U.S. Critical Infrastructure — CISA AA24-038A](https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-038a)
14. [DragonForce Strikes AdvancedHEALTH — DeXpose](https://www.dexpose.io/dragonforce-strikes-advancedhealth-in-ransomware-attack/)
15. [DragonForce Targets Plan.com — DeXpose](https://www.dexpose.io/dragonforce-targets-plan-in-ransomware-attack/)
16. [North Korea's Lazarus Targets macOS Users via ClickFix — Dark Reading](https://www.darkreading.com/threat-intelligence/north-koreas-lazarus-targets-macos-users-clickfix)
17. [Lazarus Group $290M KelpDAO Crypto Theft — UPI](https://www.upi.com/Top_News/World-News/2026/04/22/KelpDAO-LayerZero-North-Korea-crypto-hack-theft-Lazarus-Group/6151776848419/)
18. [ClickFix YARA Rule Catches What AV Misses — ReversingLabs](https://www.reversinglabs.com/blog/clickfix-yara-rule)
19. [DragonForce YARA Rules — petstuk / GitHub](https://github.com/petstuk/DragonForceRansomwareYARA)
20. [ClickFix Campaign: Fake macOS Utilities Delivering Infostealers — Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/05/06/clickfix-campaign-uses-fake-macos-utilities-lures-deliver-infostealers/)

---

<sub>© Acentry Security · Daily Threat Intelligence Feed · 2026-05-19 · Generated automatically — verify before operational use.</sub>
