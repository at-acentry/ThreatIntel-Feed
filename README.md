<h1 align="center">Acentry Security · Threat Intelligence Feed</h1>

A daily, analyst-curated threat intelligence brief covering the most critical cyber security risks of the last 24–72 hours: actively exploited CVEs, APT / state-sponsored campaign activity, indicators of compromise (IOCs), and YARA rules ready for SOC ingestion.

Each morning a fresh brief is published to **[`latest.md`](latest.md)**; a dated copy is preserved under [`archive/`](archive/).

> **Classification:** TLP:CLEAR — For Acentry clients and the broader defender community.  
> **Cadence:** Daily, ~07:00 local.  
> **Scope:** Critical CVEs (CVSS ≥ 8.0, KEV-listed, or actively exploited), APT activity, IOCs (hashes, IPs, domains, email), YARA rules, recommended actions.

---

## Today

- **[Latest brief →](latest.md)**

## Recent Archives

- [2026-05-18](archive/2026-05-18.md)
- [2026-05-17](archive/2026-05-17.md)
- [2026-05-10](archive/2026-05-10.md)

<sub>The full archive is available in the [`archive/`](archive/) directory.</sub>

---

## How to use this feed

1. **Pull the latest** — `latest.md` always reflects the current day's brief.
2. **Ingest the IOCs** — every brief includes copy-paste-ready hashes, IPs, domains, and YARA rules.
3. **Verify before operational use** — IOCs and rules are sourced from public threat intelligence providers; validate against your environment before deploying as blocking controls.
4. **Subscribe to changes** — watch this repo on GitHub or use the [RSS-style commit feed](https://github.com/at-acentry/ThreatIntel-Feed/commits/main.atom).

## Sources we monitor

CISA KEV catalog · NVD · Microsoft MSRC · Cisco PSIRT · Fortinet PSIRT · Ivanti · Palo Alto Networks (Unit 42) · Mandiant / Google Threat Intelligence Group · CrowdStrike · Cisco Talos · Microsoft Threat Intelligence · Recorded Future · Sentinel Labs · ESET · Kaspersky Securelist · Trend Micro · Volexity · Elastic Security Labs · BleepingComputer · The Hacker News · SecurityWeek · Dark Reading · The Record · MITRE ATT&CK · Abuse.ch (URLhaus, MalwareBazaar, ThreatFox) · YARAHQ · Florian Roth's signature-base.

## Disclaimer

This feed is provided as best-effort defensive intelligence. Acentry Security accepts no liability for action taken on the basis of these briefs. Always verify indicators against authoritative sources before escalation, blocking, or notification. Treat all IOCs as untrusted data — handle hashes/URLs/domains in isolated environments only.

---

<sub>© Acentry Security · ThreatIntel-Feed · Generated automatically.</sub>
