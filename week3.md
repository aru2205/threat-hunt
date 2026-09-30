# Week 3: Data Processing and Exploitation

This week takes the three case studies from Week 2 and turns them into structured data that can be correlated.

## 1. Overview

The raw OSINT and telemetry from Week 2 can't be used for detection as it is. It has to be normalized, enriched and modeled as events first, and only then matched against live logs. We do this with MISP for event and IOC modeling and with Elastic Stack (KQL) for detection logic, covering all three case studies together.

## 2. Adapting MISP for Mixed Threat Intelligence

MISP is normally used for network and file IOCs. Here we also use it for physical security events, so both project tracks sit in one correlation platform instead of separate tools.

### Event structure and custom attributes

| Event | Track | Custom attributes |
|---|---|---|
| Event 1: Physical access anomaly (ACS/СКУД) | Physical | `badge_id`, `access_zone`, `employee_hr_status`, `door_id` |
| Event 2: VS Code supply-chain compromise | Digital | `extension_id` (nx-console), `sha256_hash` of main.js, `c2_domain`, `stolen_key_type` |
| Event 3: Infrastructure leak exposure | Digital | `domain` (websfm.kz), `ip_address` (213.130.74.24), `nameserver` (ns1.ps.kz) |

Event 1 comes from the badge-cloning case in Week 2. Events 2 and 3 come from the Nx Console and websfm.kz cases, also in Week 2.

Timeline of the attack behind Event 2:

<img width="1200" height="600" alt="Nx Console / GitHub supply-chain attack timeline" src="https://github.com/user-attachments/assets/98062b81-c867-4b5c-8276-8ae7b9be58eb" />

### Indicator reference

| Indicator type | Value / pattern | Suggested threat or anomaly |
|---|---|---|
| Behavioral (physical) | Badge-in with no matching badge-out | Tailgating or badge sharing |
| Behavioral (physical) | Badge entries at two distant doors within 5 minutes | Cloned RFID badge ("impossible travel" for badges) |
| Behavioral (physical) | Access attempt after the employee's termination date in HR | Offboarding failure or unauthorized physical access |
| Supply-chain | Obfuscated JS block (~2.7–3 KB) in the extension's main.js | Malicious VS Code extension payload |
| Supply-chain | Bulk `git clone` requests using Personal Access Tokens (PAT) | Automated repository exfiltration with stolen keys |
| OSINT / infrastructure | Exposed SPF/TXT records with wildcard includes (websfm.kz) | Email spoofing or domain hijacking vector |

<img width="900" height="600" alt="Indicators defined per category" src="https://github.com/user-attachments/assets/3fbc4c7a-5577-456f-b0ca-6db497f6fe72" />

Three of the six indicators are physical/behavioral, so the physical track has as much detection logic behind it as the digital one.

## 3. Data Processing Pipeline

1. **Timestamp normalization.** All logs (ACS doors, GitHub PAT access, VS Code execution logs) are converted to UTC in ISO-8601 format. This lets a badge event and a GitHub API call sit on the same timeline.
2. **Identity cross-referencing.** We map `Badge_ID → Employee_ID → HR_Status → GitHub_User`, so one person's physical and digital activity can be linked. For example, an offboarded employee's badge attempt can be matched with a GitHub token that is still active.
3. **Baseline filtering.** Normal shift patterns, expected CI/CD IP ranges and known developer machines are filtered out, so that only real anomalies reach the detection layer.

## 4. Detection Rules and Correlation Logic

### 4.1 KQL query (Elastic Stack): VS Code credential harvesting

This rule targets the Case Study 2 pattern: a process spawned by VS Code that then tries to read SSH keys, GitHub CLI config or generic credential files, which is what the Nx Console payload did.

```kql
(process.parent.name: "code.exe" or process.parent.name: "code")
and process.name: ("bash" or "sh" or "powershell.exe" or "cmd.exe" or "curl")
and process.args: ("*~/.ssh*" or "*~/.config/gh*" or "*credentials*")
```

VS Code has no legitimate reason to start a shell and read `~/.ssh` or `~/.config/gh`. Developers normally touch those files through git or ssh directly, not through a child process of the editor, so this behavior separates a malicious extension from ordinary work.

**Tuning note:** in production the rule needs an exception list for legitimate extensions that do shell out for credential-related tasks (some Git GUI extensions, for example). The list comes from the baseline filtering step in section 3, and without it the rule will be noisy.

### 4.2 Correlation rule (conceptual): physical/digital identity link

Using the identity mapping from section 3, a second rule fires when:

```
employee_hr_status = "terminated"
AND (badge event OR GitHub PAT event) for the same Employee_ID
    occurs after the termination timestamp
```

One rule covers the offboarding-failure case in both tracks, because it doesn't matter whether the leftover access is a badge or a token.

## 5. Recommended Reading

- MISP Training Documentation
- Elastic Security detection-rules documentation (KQL syntax reference)

## 6. Next Steps (Week 4)

1. Map each event in section 2 to Cyber Kill Chain stages and cite the relevant MITRE ATT&CK technique IDs.
2. Extract concrete IOCs from the GHSA advisories referenced in Week 2 (GHSA-c9j4-9m59-847w, GHSA-g7cv-rxg3-hmpx) and add them as attributes on Event 2.
3. Build the baseline exception list from section 4.1 so the KQL rule is deployable and not only illustrative.
