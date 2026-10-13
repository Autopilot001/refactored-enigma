# Enterprise Attack Detection Lab
### Entra ID + Splunk + Kali Linux — Identity, Reconnaissance Detection & SOC Workflow

A home-lab project that simulates a small enterprise environment, generates realistic attacker telemetry (network reconnaissance + identity attacks), ingests it into Splunk, and builds detections, dashboards, and an incident-response playbook around it.

This repo is designed as a **portfolio / learning project** for SOC analyst, detection engineer, and blue-team roles. It intentionally avoids shipping any malware, AV-evasion code, or offensive payloads — recon and attack simulation are done with standard, well-known tools (`nmap`, `hydra`-style brute-force simulation against a lab-only target, Atomic Red Team) strictly against infrastructure you own/control.

## Architecture

```
            ┌────────────────────┐
            │   Microsoft Entra  │
            │   ID (Azure AD)    │
            │  Sign-in + Audit   │
            │        logs        │
            └─────────┬──────────┘
                       │ (Microsoft Sentinel/
                       │  Graph API connector or
                       │  Splunk Add-on for MS Cloud Services)
                       ▼
            ┌────────────────────┐
            │  Splunk Enterprise  │◄────────────┐
            │  (SIEM, indexes,    │              │
            │  detections, dash-  │   Syslog/UF  │
            │  boards, alerts)    │              │
            └─────────┬──────────┘              │
                       │                          │
          ┌────────────┴───────────┐    ┌─────────┴─────────┐
          │   Target VM (Victim)    │    │  Kali Linux VM     │
          │  Linux/Windows + UF     │◄───│  (Attacker)         │
          │  forwards auth + fw     │    │  nmap scans,        │
          │  logs to Splunk         │    │  brute-force sim,   │
          └──────────────────────────┘   │  Atomic Red Team    │
                                          └─────────────────────┘
```

## What this project demonstrates

| Skill area | Covered by |
|---|---|
| Identity security / Entra ID | Conditional Access, sign-in log analysis, risky sign-in detection |
| Network reconnaissance detection | nmap scan simulation + Splunk port-scan detection searches |
| SIEM engineering | Splunk index/source design, field extraction, correlation searches |
| Detection engineering | SPL detections mapped to MITRE ATT&CK |
| SOC workflow | Dashboards, alert triage, incident response playbook |
| Safe adversary simulation | Atomic Red Team + EICAR-based AV/EDR detection testing (no live malware) |

## Repo structure

```
.
├── README.md
├── docs/
│   ├── 01-architecture.md
│   ├── 02-entra-id-setup.md
│   ├── 03-splunk-setup.md
│   ├── 04-kali-recon.md
│   ├── 05-detection-engineering.md
│   └── 06-incident-response-playbook.md
├── splunk/
│   ├── detections/        # SPL searches, one per detection
│   └── dashboards/        # dashboard XML/JSON
├── scripts/
│   └── nmap_scan.sh       # recon automation used to generate test telemetry
├── diagrams/
└── LICENSE
```

## Quick start

1. Read `docs/01-architecture.md` for the lab topology and VM sizing.
2. Stand up Entra ID + a trial tenant — `docs/02-entra-id-setup.md`.
3. Install Splunk Enterprise (free dev license) and the Microsoft Cloud Services add-on — `docs/03-splunk-setup.md`.
4. Build the Kali attacker VM and run the recon scripts against your **own lab target only** — `docs/04-kali-recon.md`.
5. Load the detections in `splunk/detections/` and the dashboard in `splunk/dashboards/`.
6. Walk through `docs/06-incident-response-playbook.md` to triage the alerts you just generated.

## Legal / ethical scope

Everything here is for an **isolated home lab you own**. Do not point the scanning scripts or brute-force simulation at any system you do not have explicit written authorization to test. Unauthorized port scanning or credential attacks against third-party systems is illegal in most jurisdictions (e.g., the U.S. Computer Fraud and Abuse Act, UK Computer Misuse Act).

## License

MIT — see `LICENSE`.
