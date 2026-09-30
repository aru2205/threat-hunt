# Insider Threat via Trusted Access

## About the Project

This repository contains our work for the **Introduction to Threat Hunting** course (AITU, 2026–2027).

The project's core idea: an insider threat doesn't have to mean a malicious employee - it's what happens when **trusted access gets abused**, whether that access is a physical badge or a digital credential. We track this idea across two parallel tracks:

- **Physical track** - badge/ACS misuse, physical social engineering, reconnaissance via public photos.
- **Digital track** - stolen credentials, software supply-chain compromise, infrastructure exposure.

The same underlying pattern shows up in both: a legitimate, trusted channel is harvested or hijacked, and the system on the other end has no reason to distrust it. Each week applies a different CTI skill (fundamentals → OSINT collection → data processing → kill-chain analysis) to case studies from **both** tracks side by side, so the two aren't treated as separate projects but as two expressions of the same threat model.

Areas covered:

- Cyber Threat Intelligence fundamentals
- OSINT collection and analysis
- Threat actor profiling
- Physical insider threats
- Supply-chain attacks
- Infrastructure exposure
- Threat detection & correlation (MISP, Elastic/KQL)

---

## Week 1 - Threat Intelligence Fundamentals

Week 1 covers the foundational concepts, built for both tracks from the start rather than adding the digital side later:

- **Threat actor types:** Nation-State/APT groups, cybercriminals, insider threats, hacktivists.
- **Classification model:** intent, capability, and TTPs - applied to both physical and digital insiders.
- **Glossary:** physical-track terms (tailgating, badge cloning, shoulder surfing, dwell time) alongside digital-track terms (credential/token theft, supply-chain compromise, C2, PAT abuse), plus shared categories (negligent / malicious / compromised insider).
- **ENISA's five insider-threat types** (careless workers, inside agents, disgruntled employees, malicious insiders, feckless third parties), mapped to examples from both tracks.
- **Case study:** the USB Killer incident (College of Saint Rose, 2019) - physical access to endpoints as its own attack surface.
- **Supporting statistics** on how often physical action features in reported breaches, sourced from ENISA's Threat Landscape report.
- **Frameworks reviewed:** MITRE ATT&CK, ENISA, SANS, Recorded Future.

---

## Week 2 - Data Collection (OSINT)

Week 2 moves from theory to actual reconnaissance, run against three real case studies - one physical, two digital:

1. **Case Study 1 (Physical) - Badge cloning via social media OSINT.** Employee badge photos posted on LinkedIn/Facebook, combined with leaked building layouts, allowed a working RFID badge clone to be produced without ever touching the target's systems.
2. **Case Study 2 (Digital) - Nx Console / GitHub supply-chain breach (May 2026).** A stolen developer token led to a trojanized VS Code extension (2.2M+ installs) that harvested credentials from any workspace that opened it, ultimately exposing ~3,800 internal GitHub repositories. Verified against public incident reporting (StepSecurity, Cloud Security Alliance, Rankiteo), with a full minute-by-minute timeline and GHSA advisory references.
3. **Case Study 3 (Digital) - Infrastructure exposure of `websfm.kz`.** Passive OSINT via Intelligence X surfaced the domain's DNS/hosting fingerprint inside a large Kazakhstani-domain breach compilation - not a backend breach, but a reconnaissance-enablement finding. Includes a quantitative breakdown (nameserver-provider concentration, country-code distribution) of the sampled leaked records.

**Data types used:**

- **OSINT** - publicly available data. This round of collection used **Intelligence X** for domain/breach-archive searches, and public social-media imagery for the badge-cloning case. Shodan, VirusTotal, and Maltego are scoped as the next collection pass (see Week 2's "Next Steps"), once specific IPs/hashes from the case studies above are isolated for live lookup.
- **Internal/closed data** - SIEM, EDR, ACS/badge logs, GitHub audit logs, endpoint logs. Not collected directly this week, but mapped in the Data Source Mapping table as the internal counterpart to each OSINT finding.

OSINT shows what's visible from the outside; internal logs show what's actually happening inside. The project treats these as complementary, not redundant.

---

## Week 3 - Data Processing and Exploitation

Week 3 takes the three Week 2 case studies and turns them into structured, correlatable threat-intel data using **MISP** and **Elastic Stack/KQL** - one platform for both tracks.

**Pipeline:**

1. **Normalize** - convert every log source (ACS doors, GitHub PAT access, VS Code execution) to UTC/ISO-8601.
2. **Cross-reference identity** - `Badge_ID → Employee_ID → HR_Status → GitHub_User`, so a person's physical and digital activity can be correlated on one timeline.
3. **Baseline filter** - strip out normal shift patterns, expected CI/CD IP ranges, and known developer machines to isolate real anomalies.
4. **Detect** - a KQL rule targeting VS Code-spawned processes reaching for SSH keys/credentials (built directly from the Nx Console payload behavior), plus a conceptual correlation rule that flags offboarding failures on *either* track (leftover badge access or leftover GitHub token) using the same logic.

**MISP events modeled:** Physical Access Anomaly (badge/ACS), VS Code Supply-Chain Compromise, Infrastructure Leak Exposure - each with its own custom attributes and IOC indicators, split roughly evenly between physical and digital indicator types.

---

## Week 4 — Cyber Kill Chain

Week 4 runs the same case studies through Lockheed Martin's Cyber Kill Chain (Reconnaissance, Weaponization, Delivery, Exploitation, Installation, Command & Control, Actions on Objectives), mapping each stage to MITRE ATT&CK technique IDs.

The digital track (Nx Console/GitHub) maps cleanly onto ATT&CK at every stage (T1593, T1195.002, T1528, T1554, T1102, T1567, T1213). The physical track (badge cloning) doesn't: three of the seven stages have no direct Enterprise ATT&CK equivalent, since ATT&CK is built for software behavior, not physical access control. That gap is the reason Week 3's MISP model keeps physical indicators as their own category instead of forcing them into ATT&CK's vocabulary.

---

## Tools

Used or studied so far:

- **OSINT:** Intelligence X (used); Shodan, Censys, VirusTotal, Maltego (planned for the next collection round)
- **Detection & correlation:** MISP, Elastic Stack, KQL, Sigma rules
- **Frameworks:** MITRE ATT&CK, ENISA Threat Landscape
- **Internal data sources modeled:** GitHub audit logs, badge/ACS logs, VS Code extension logs

---

## Project Workflow

```
Threat Identification
        ↓
Data Collection (OSINT + internal)
        ↓
Data Verification
        ↓
Data Processing & Normalization
        ↓
Correlation (physical ⨯ digital identity)
        ↓
Threat Analysis (Kill Chain / ATT&CK)
```

---

## Main Idea

One security source rarely shows the full picture. OSINT shows what's publicly visible; internal logs show what's actually happening inside an organization. And a badge and a GitHub token are, functionally, the same kind of thing - a trusted credential someone else can steal. Combining both data sources, and treating both tracks as one threat model instead of two separate ones, is what this project is actually testing.
