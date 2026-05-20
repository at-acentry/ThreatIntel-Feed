<!-- Acentry Daily Threat Intelligence Feed -->

<h1 align="center">ACENTRY SECURITY</h1>
<h3 align="center">Daily Threat Intelligence Feed</h3>

---

**Date:** 2026-05-18  
**Classification:** TLP:CLEAR — For Acentry Clients  
**Analyst:** Acentry Threat Intelligence Team

> **Daily Brief.** Today's threat landscape is dominated by three converging crises: a maximum-severity CVSS 10.0 authentication bypass in Cisco Catalyst SD-WAN infrastructure (CVE-2026-20182) is being actively exploited by the sophisticated state-linked actor UAT-8616, representing the sixth Cisco SD-WAN zero-day weaponized by this group in 2026 alone. In parallel, the "Copy Fail" Linux kernel privilege escalation (CVE-2026-31431) — a reliably exploitable, 732-byte Python script that grants root on virtually all Linux distributions since 2017 — continues to spawn new compiled variants and dropper tooling now tracked in the wild. Iranian state-sponsored group MuddyWater (Mango Sandstorm) has added a false-flag ransomware dimension to its credential-harvesting operations, using Microsoft Teams social engineering to steal credentials and stage long-term espionage under cover of apparent ransomware intrusion. Organizations running Cisco SD-WAN, Linux servers, PAN-OS firewalls, or Ivanti EPMM should treat today's advisories as emergency-priority actions.

## Executive Summary

- **CVE-2026-20182 (CVSS 10.0 / KEV):** Critical Cisco Catalyst SD-WAN Controller authentication bypass actively exploited by UAT-8616; attacker can inject SSH keys as `vmanage-admin` and issue arbitrary NETCONF commands — patch immediately or block untrusted access to UDP/12346.
- **CVE-2026-31431 "Copy Fail" (CVSS 7.8 / KEV):** Linux kernel local privilege escalation affecting all major distributions since 2017; a working 732-byte PoC is publicly available and compiled variants are circulating — apply vendor patches; no workaround is sufficient alone.
- **CVE-2026-0300 (CVSS 9.3 / KEV):** Unauthenticated root-level RCE in Palo Alto PAN-OS Captive Portal has been confirmed in limited exploitation since April; state-sponsored attribution likely based on post-exploitation tooling (Earthworm, ReverseSocks5).
- **CVE-2026-6973 (CVSS 7.2 / KEV):** Ivanti EPMM RCE zero-day being chained with credential reuse from earlier CVE-2026-1281/1340 compromises; targets MDM infrastructure that manages corporate mobile fleets.
- **MuddyWater / Mango Sandstorm:** Iranian APT using Microsoft Teams IT-support impersonation to harvest MFA credentials and deploy a custom backdoor (`Game.exe`), masking espionage as a Chaos ransomware intrusion; confirmed IOCs include C2 domains `moonzonet[.]com` and `uploadfiler[.]com`.
- **"The Gentlemen" Ransomware:** Rapidly emerging RaaS group now ranked third globally after zero victims in August 2025; achieved 166 victims in Q1 2026 through pre-stockpiled access and non-US targeting strategy.

## Critical CVEs (Last 24–72h)

| CVE ID | CVSS | Vendor / Product | Description | Exploited | Patch / Mitigation | Source |
|--------|------|------------------|-------------|-----------|--------------------|--------|
| CVE-2026-20182 | 10.0 | Cisco / Catalyst SD-WAN Controller & Manager | Authentication bypass via vdaemon DTLS service (UDP/12346); allows unauthenticated attacker to inject SSH keys and gain admin access | Yes — KEV; UAT-8616 | Upgrade per `cisco-sa-sdwan-rpa2-v69WY2SW`; restrict UDP/12346 to trusted peers | [Cisco PSIRT](https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-sdwan-rpa2-v69WY2SW) |
| CVE-2026-31431 | 7.8 | Linux Kernel / All major distros (kernels 4.14–7.0-rc) | "Copy Fail" — logic bug in `authencesn` cryptographic template; unprivileged local user triggers controlled 4-byte page-cache write to elevate to root | Yes — KEV; public PoC in wild | Apply vendor kernel patches (Ubuntu, RHEL, Amazon Linux, SUSE available); restrict `AF_ALG` access where possible | [Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/) |
| CVE-2026-0300 | 9.3 | Palo Alto Networks / PAN-OS PA-Series & VM-Series | Buffer overflow in Captive Portal; unauthenticated RCE as root via crafted packets to ports 6081/6082 | Yes — KEV; limited in-wild; state-actor attribution | Upgrade to PAN-OS 12.1.4-h5 / 11.2.7-h13 / 11.2.10-h6 or later; restrict portal to trusted internal zones | [Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/) |
| CVE-2026-6973 | 7.2 | Ivanti / EPMM | Improper input validation; authenticated admin RCE, chained with CVE-2026-1281/1340 credential reuse | Yes — KEV; limited targeted | Upgrade EPMM to 12.6.1.1 / 12.7.0.1 / 12.8.0.1; rotate all EPMM admin credentials | [Help Net Security](https://www.helpnetsecurity.com/2026/05/08/ivanti-epmm-zero-day-cve-2026-6973/) |
| CVE-2026-1281 | 9.8 | Ivanti / EPMM | Unauthenticated RCE; precursor in chained exploitation with CVE-2026-6973 via harvested credentials | Yes — KEV; exploited Q1 2026 | Patch EPMM; rotate all EPMM admin credentials regardless of prior patch state | [Unit 42](https://unit42.paloaltonetworks.com/ivanti-cve-2026-1281-cve-2026-1340/) |
| CVE-2026-20127 | 9.8 | Cisco / Catalyst SD-WAN Controller | Earlier auth bypass in same product family, exploited by UAT-8616 since 2023; related to CVE-2026-20182 campaign | Yes — KEV; exploitation ongoing | Patch per Cisco advisory; verify no unauthorized SSH keys were injected post-compromise | [Cisco Talos](https://blog.talosintelligence.com/uat-8616-sd-wan/) |

## APT / Threat Actor Activity

### UAT-8616 (Cisco Talos designation; nation-state suspected)

- **Targets:** Critical national infrastructure (CNI) operators — energy utilities, telecoms, transportation; global scope with emphasis on high-value Western and Asia-Pacific organizations
- **TTPs:** Initial access via Cisco SD-WAN auth bypass (`T1190`); privilege escalation via version-downgrade to exploit CVE-2022-20775 (`T1068`); SSH key injection into `vmanage-admin` (`T1098.004`); NETCONF command execution (`T1059`); Operational Relay Box (ORB) network abuse for C2 obfuscation (`T1090.003`); extensive log clearing (`T1070.001`); lateral movement via compromised SD-WAN management plane
- **Recent activity:** Active exploitation of CVE-2026-20182 (May 2026), continuing a campaign stretching back to at least 2023 that has now weaponized six distinct Cisco SD-WAN zero-days. Following public PoC release, ten additional threat clusters began opportunistic exploitation — UAT-8616 remains the primary sophisticated actor but the attack surface has broadened significantly.
- **Attribution confidence:** Medium — Cisco Talos tracks with high confidence as a distinct cluster; infrastructure overlaps with monitored ORB networks suggest nation-state sponsorship; no public country attribution
- **Sources:** [Cisco Talos Blog](https://blog.talosintelligence.com/uat-8616-sd-wan/), [Help Net Security](https://www.helpnetsecurity.com/2026/05/15/cisco-sd-wan-zero-day-cve-2026-20182/), [SecurityWeek](https://www.securityweek.com/cisco-patches-another-sd-wan-zero-day-the-sixth-exploited-in-2026/)

---

### MuddyWater (aliases: Mango Sandstorm, MERCURY, Seedworm, Static Kitten, Earth Vetala, TA450)

- **Targets:** Western and MENA-region organizations; construction, manufacturing, business services, and government sectors; confirmed US-based victims in most recent campaign
- **TTPs:** Initial access via unsolicited Microsoft Teams external chat requests impersonating IT support (`T1566.004`); screen-sharing to harvest credentials and manipulate MFA (`T1111`, `T1056.001`); deployment of remote access tools DWAgent and AnyDesk for persistence (`T1219`); custom downloader `ms_upd.exe` signed with "Donald Gay" code-signing certificate (previously used in Fakeset/CastleLoader) fetches `Game.exe` backdoor (`T1105`, `T1027`); `Game.exe` provides remote command execution, interactive shell, file manipulation, sandbox evasion (`T1059.003`); C2 over HTTPS (`T1071.001`); false-flag Chaos ransomware deployment to mask state-espionage as financial crime (`T1036`)
- **Recent activity:** Rapid7 disclosed a MuddyWater intrusion (March 2026) where Teams social engineering led to full credential harvest, lateral movement, and data exfiltration — the Chaos ransomware appearance was confirmed as a deliberate false flag. C2 infrastructure includes `moonzonet[.]com` and `uploadfiler[.]com` at `172.86.126[.]208:443`. Chaos ransomware claimed 36 victims on its leak site as of late March 2026, predominantly US-based.
- **Attribution confidence:** High — code-signing certificate reuse ("Donald Gay") cross-links to prior MuddyWater campaigns; Rapid7 and Microsoft Threat Intelligence corroborate attribution; MITRE ATT&CK Group G0069
- **Sources:** [The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html), [MITRE ATT&CK G0069](https://attack.mitre.org/groups/G0069/), [Xcitium Threat Labs](https://threatlabsnews.xcitium.com/blog/muddywater-leverages-microsoft-teams-for-credential-theft-in-false-flag-ransomware-attack/)

---

### "The Gentlemen" (Ransomware-as-a-Service)

- **Targets:** Non-US organizations (deliberate avoidance of US-centric targeting); broad geographic diversification; third place globally in victim counts as of Q1 2026
- **TTPs:** Pre-stockpiled network access leveraged for rapid deployment (`T1078`); encryptionless extortion trend with data-theft-only variants; RaaS affiliate model; aggressive expansion from zero victims in August 2025 to 166 in Q1 2026
- **Recent activity:** The Gentlemen achieved third place globally in ransomware victim counts (Q1 2026, Check Point Research), representing the most significant new entrant to the threat landscape this year. Exemplifies the industry-wide shift toward data-leak-only extortion — the 28% ransom payment rate industrywide in 2025 is driving actors toward encryptionless models.
- **Attribution confidence:** Low — limited public technical analysis; tracked by Check Point Research
- **Sources:** [Check Point Q1 2026 Ransomware Report](https://research.checkpoint.com/2026/the-state-of-ransomware-q1-2026/), [CYFIRMA Weekly Intelligence 08 May 2026](https://www.cyfirma.com/news/weekly-intelligence-report-08-may-2026/)

## Indicators of Compromise (IOCs)

> Indicators are presented un-defanged for direct ingestion. Handle in an isolated environment.

### File Hashes (SHA-256)

| Hash | Family / Threat | First Seen | Source |
|------|-----------------|------------|--------|
| a567d09b15f6e4440e70c9f2aa8edec8ed59f53301952df05c719aa3911687f9 | CVE-2026-31431 "Copy Fail" — Theori original PoC (732B Python) | 2026-04-29 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| 3c5ec61632d0699e048d8428461c4d65f89988a370396db2f070f63ebbf9dbae | CVE-2026-31431 "Copy Fail" — PoC variant (740B Python, CRLF line endings) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| d401e7d1c00605749d6c617ace73ab20a762b72e41c2e1590331596e38219a61 | CVE-2026-31431 "Copy Fail" — PoC variant (731B Python) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| e097ce64b1bf0933e69c9d342038fb52f4b278da62b265daa3adf22c00658a9c | CVE-2026-31431 "Copy Fail" — PoC variant (806B Python) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| 7ef953069578beef7daf2d984d16351b0c60c6adcbf8062ffbfaac6ce944c1da | CVE-2026-31431 "Copy Fail" — compiled ELF shellcode (160B, raw x86-64 payload) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| 01881b737c106a6d84e8e12cb417671b5f612f13adcb6cb1edd8a46704ddd252 | CVE-2026-31431 "Copy Fail" — compiled ELF shellcode (161B, raw x86-64 payload) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| b35ddf2ecd035faf9b38af62779502b7fa19037115054a00ed8d5327a3f2ec03 | CVE-2026-31431 "Copy Fail" — goodcopy.c source (blasty C reimplementation) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| ed0018054d8e7058b299b7591bc32364dbc439c25be4067450189b1a73033c67 | CVE-2026-31431 "Copy Fail" — goodcopy ELF64 static binary (930KB) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |
| 84f44c4a699e025ceff588028c9e041b213d6198fc7fa40b7d24ca6ebbf9b305 | CVE-2026-31431 "Copy Fail" — goodcopy ELF64 dynamic PIE binary (17KB) | 2026-04-30 | [ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules) |

### Network — IPs

| IP | Threat | First Seen | Source |
|----|--------|------------|--------|
| 172.86.126.208 | MuddyWater (Mango Sandstorm) — C2 server (HTTPS/443); `ms_upd.exe` and `Game.exe` backdoor callback infrastructure | 2026-03 | [The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html) |

### Network — Domains / URLs

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| moonzonet.com | MuddyWater (Mango Sandstorm) — C2 domain; malware component download and backdoor callback | 2026-03 | [The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html) |
| uploadfiler.com | MuddyWater (Mango Sandstorm) — C2 domain; file exfiltration and payload staging | 2026-03 | [The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html) |
| copy.fail | CVE-2026-31431 "Copy Fail" — Theori PoC disclosure site; dropper scripts reference this URL for payload retrieval | 2026-04-29 | [Theori / ReversingLabs](https://copy.fail/) |

### Email Indicators

| Indicator | Threat | First Seen | Source |
|-----------|--------|------------|--------|
| External Teams chat requests impersonating "IT Helpdesk" / "IT Support" from unmanaged tenants | MuddyWater — initial social engineering access vector; Teams external chat from unknown/unmanaged tenant | 2026-03 | [Xcitium Threat Labs](https://threatlabsnews.xcitium.com/blog/muddywater-leverages-microsoft-teams-for-credential-theft-in-false-flag-ransomware-attack/) |
| Code-signing cert Subject CN: "Donald Gay" | MuddyWater — authenticode cert used to sign `ms_upd.exe` downloader; previously used on Fakeset/CastleLoader malware | 2026-03 | [The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html) |

## YARA Rules

```yara
/*
    YARA Rules: CVE-2026-31431 "Copy Fail" — Detection Suite
    Linux kernel local privilege escalation via AF_ALG + authencesn + splice()
    page-cache scratch-write primitive.

    Authors    : ReversingLabs Threat Research; Malware Utkonos
    Date       : 2026-04-30
    Reference  : https://www.reversinglabs.com/blog/copy-fail-5-yara-rules
                 https://copy.fail/
    TLP        : CLEAR
    CVE        : CVE-2026-31431
*/

rule CopyFail_PoC_Theori_Exact : CVE_2026_31431 exploit lpe linux
{
    meta:
        description = "Detects original Theori copy_fail_exp.py PoC for CVE-2026-31431 (Copy Fail Linux LPE) — HIGH confidence"
        author      = "ReversingLabs Threat Research"
        date        = "2026-04-30"
        version     = "1.0"
        severity    = "HIGH"
        confidence  = "HIGH"
        tlp         = "CLEAR"
        reference   = "https://copy.fail/"
        cve         = "CVE-2026-31431"
        hash_sha256 = "a567d09b15f6e4440e70c9f2aa8edec8ed59f53301952df05c719aa3911687f9"

    strings:
        $import_alias   = "import os as g,zlib,socket as s" ascii
        $aead_algo      = "authencesn(hmac(sha256),cbc(aes))" ascii
        $aead_key       = "0800010000000010" ascii
        $zlib_magic     = { 78 DA }
        $target_su      = "/usr/bin/su" ascii
        $shellcode_blob = "78daab77f571" ascii nocase

    condition:
        filesize < 10KB and
        $import_alias and
        $aead_algo and
        $aead_key and
        $target_su and
        ($shellcode_blob or $zlib_magic)
}


rule CopyFail_Primitive_Python : CVE_2026_31431 exploit lpe linux hunting
{
    meta:
        description = "Detects Python scripts using AF_ALG authencesn + os.splice() primitive — catches renamed/refactored variants (MEDIUM confidence)"
        author      = "ReversingLabs Threat Research"
        date        = "2026-04-30"
        version     = "1.0"
        severity    = "HIGH"
        confidence  = "MEDIUM"
        tlp         = "CLEAR"
        reference   = "https://xint.io/blog/copy-fail-linux-distributions"
        cve         = "CVE-2026-31431"

    strings:
        $aead_algo  = "authencesn(hmac(sha256),cbc(aes))" ascii
        $splice1    = "os.splice" ascii
        $splice2    = "from os import splice" ascii
        $af_alg_int = "socket(38," ascii
        $af_alg_con = "AF_ALG" ascii
        $aead_bind1 = ".bind((\"aead\"" ascii
        $aead_bind2 = ".bind(('aead'" ascii

    condition:
        filesize < 500KB and
        $aead_algo and
        (1 of ($splice1, $splice2)) and
        (1 of ($af_alg_int, $af_alg_con)) and
        (1 of ($aead_bind1, $aead_bind2))
}


rule CopyFail_Shellcode_Reference_1 : CVE_2026_31431 shellcode lpe linux
{
    meta:
        author      = "Malware Utkonos / cited by ReversingLabs Threat Research"
        date        = "2026-04-30"
        description = "Detects CopyFail shellcode: setuid(0) [syscall 105], execve [syscall 59], exit [syscall 60] in x86-64 ELF"
        reference   = "https://www.reversinglabs.com/blog/copy-fail-5-yara-rules"
        tlp         = "CLEAR"
        cve         = "CVE-2026-31431"

    strings:
        $op1 = { b0 69 0f 05 }
        // mov al, 0x69 ; syscall  →  setuid(0)
        $op2 = { 6a 3b 58 99 0f 05 }
        // push 0x3b ; pop rax ; cdq ; syscall  →  execve
        $op3 = { 6a 3c 58 0f 05 }
        // push 0x3c ; pop rax ; syscall  →  exit

    condition:
        all of them
}
```

## Recommended Actions

- [ ] **SOC — Hunt:** Search EDR/SIEM for processes invoking `AF_ALG` socket with `authencesn(hmac(sha256),cbc(aes))` algorithm string on Linux hosts; alert on `splice()` syscall sequences from unprivileged users followed by SUID binary execution. Deploy CopyFail YARA rules above to endpoint AV and file analysis pipelines.
- [ ] **SOC — Hunt:** Block/alert on outbound connections to `moonzonet.com`, `uploadfiler.com`, and `172.86.126.208`; search EDR telemetry for `ms_upd.exe`, `DWAgent`, and `AnyDesk` launched as children of suspicious parent processes; review Microsoft Teams external-chat logs for unsolicited contacts from unmanaged tenants.
- [ ] **SOC — Hunt:** Review Cisco SD-WAN audit logs for unauthorized SSH key additions to `vmanage-admin`, unexpected NETCONF sessions on TCP/830, and inbound connections from untrusted peers to UDP/12346. Compare authorized SSH keys against known-good baseline; treat any unrecognized key as IOC.
- [ ] **SOC — Detect:** Enable or tune detection signatures for CVE-2026-0300 (PAN-OS); monitor for anomalous traffic to ports 6081/6082 on PAN-OS devices; deploy Palo Alto Threat Prevention signatures per vendor guidance.
- [ ] **IT / Patch Mgmt — P1 (Emergency):** Patch Cisco Catalyst SD-WAN Controller and Manager per advisory `cisco-sa-sdwan-rpa2-v69WY2SW`; if patching is delayed, immediately restrict UDP/12346 to trusted SD-WAN peers via ACL.
- [ ] **IT / Patch Mgmt — P1 (Emergency):** Apply Linux kernel patches for CVE-2026-31431 on all production Linux systems; prioritize multi-tenant, cloud, CI/CD, and Kubernetes environments where container escape via shared-kernel LPE has highest blast radius.
- [ ] **IT / Patch Mgmt — P1 (Emergency):** Upgrade PAN-OS to 12.1.4-h5, 11.2.7-h13, 11.2.10-h6, or later fixed releases; restrict Captive Portal to trusted internal zones pending patch completion.
- [ ] **IT / Patch Mgmt — P2 (Urgent):** Upgrade Ivanti EPMM to 12.6.1.1, 12.7.0.1, or 12.8.0.1; rotate all EPMM admin credentials — prior CVE-2026-1281/1340 compromises may have harvested credentials being reused in current CVE-2026-6973 attacks.
- [ ] **IT / Identity:** Audit Microsoft Teams external access policies; restrict or disable external chat requests from unmanaged/unknown tenants; enforce conditional access requiring compliant device before screen-sharing sessions are permitted; educate helpdesk staff on IT-impersonation social engineering tactics.
- [ ] **Leadership:** Three simultaneous CVSS ≥9.0 KEV-listed vulnerabilities under active exploitation affecting network edge devices (Cisco SD-WAN, PAN-OS) represent elevated risk of perimeter breach; recommend briefing CISO and reviewing IR playbooks for network edge compromise scenarios. The Gentlemen ransomware group's rapid rise warrants inclusion in threat modeling for non-US clients.

## Sources & References

1. [CISA Adds Cisco SD-WAN CVE-2026-20182 to KEV — The Hacker News](https://thehackernews.com/2026/05/cisa-adds-cisco-sd-wan-cve-2026-20182.html)
2. [Active Exploitation of Cisco Catalyst SD-WAN by UAT-8616 — Cisco Talos](https://blog.talosintelligence.com/uat-8616-sd-wan/)
3. [Cisco Security Advisory: CVE-2026-20182](https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-sdwan-rpa2-v69WY2SW)
4. [CVE-2026-20182 Emergency Patch Guide — Rapid7](https://www.rapid7.com/blog/post/ve-cve-2026-20182-critical-authentication-bypass-cisco-catalyst-sd-wan-controller-fixed/)
5. [Cisco Patches Another SD-WAN Zero-Day (6th in 2026) — SecurityWeek](https://www.securityweek.com/cisco-patches-another-sd-wan-zero-day-the-sixth-exploited-in-2026/)
6. [CVE-2026-31431 "Copy Fail": Linux Root Privilege Escalation — Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/05/01/cve-2026-31431-copy-fail-vulnerability-enables-linux-root-privilege-escalation/)
7. [Copy Fail Flaw: 5 YARA Rules for Detection — ReversingLabs](https://www.reversinglabs.com/blog/copy-fail-5-yara-rules)
8. [CVE-2026-31431 Copy Fail FAQ — Tenable](https://www.tenable.com/blog/copy-fail-cve-2026-31431-frequently-asked-questions-about-linux-kernel-privilege-escalation)
9. [CERT-EU Advisory 2026-005 — Copy Fail](https://cert.europa.eu/publications/security-advisories/2026-005/)
10. [CVE-2026-0300: PAN-OS Captive Portal RCE — Unit 42](https://unit42.paloaltonetworks.com/captive-portal-zero-day/)
11. [Root-Level RCE in Palo Alto Firewalls Exploited — Help Net Security](https://www.helpnetsecurity.com/2026/05/06/palo-alto-firewalls-vulnerability-exploited-cve-2026-0300/)
12. [CVE-2026-6973 Ivanti EPMM RCE Under Active Exploitation — The Hacker News](https://thehackernews.com/2026/05/ivanti-epmm-cve-2026-6973-rce-under.html)
13. [Ivanti EPMM Zero-Day CVE-2026-6973 — Help Net Security](https://www.helpnetsecurity.com/2026/05/08/ivanti-epmm-zero-day-cve-2026-6973/)
14. [Critical Vulnerabilities in Ivanti EPMM CVE-2026-1281/1340 — Unit 42](https://unit42.paloaltonetworks.com/ivanti-cve-2026-1281-cve-2026-1340/)
15. [MuddyWater Uses Microsoft Teams to Steal Credentials — The Hacker News](https://thehackernews.com/2026/05/muddywater-uses-microsoft-teams-to.html)
16. [MuddyWater Leverages Teams for Credential Theft — Xcitium Threat Labs](https://threatlabsnews.xcitium.com/blog/muddywater-leverages-microsoft-teams-for-credential-theft-in-false-flag-ransomware-attack/)
17. [MuddyWater / G0069 — MITRE ATT&CK](https://attack.mitre.org/groups/G0069/)
18. [The State of Ransomware Q1 2026 — Check Point Research](https://research.checkpoint.com/2026/the-state-of-ransomware-q1-2026/)
19. [CISA Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
20. [Active Ivanti Exploitation Traced to Single Bulletproof IP — GreyNoise](https://www.greynoise.io/blog/active-ivanti-exploitation)

---

<sub>© Acentry Security · Daily Threat Intelligence Feed · 2026-05-18 · Generated automatically — verify before operational use.</sub>
