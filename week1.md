# Week 1: Cyber Threat Intelligence Fundamentals

**Course:** Introduction to Threat Hunting (AITU, 2026–2027)
**Project topic:** Insider threat via trusted access - covered across two tracks throughout this project: **physical** (badge/ACS misuse, physical social engineering) and **digital** (credential/token theft, software supply-chain compromise). Both are the same underlying pattern - a legitimate, trusted credential or channel being abused - expressed in different environments. This week's glossary and classification cover both tracks so later weeks can build on either.

---

## 1. Glossary of Key Terms

### Physical track

- **Insider threat** - Risk posed by someone with authorized access who misuses that access, intentionally or not.
- **Physical insider threat** - Insider threat expressed through physical means rather than purely digital access.
- **Tailgating / piggybacking** - Following an authorized person through a secured door without using your own credentials; piggybacking implies the authorized person knowingly lets you in.
- **Badge cloning** - Copying the data from an RFID/NFC access badge onto a blank card to impersonate the legitimate holder.
- **USB drop attack** - Leaving a malicious USB device in a place an employee is likely to find and plug in.
- **Shoulder surfing** - Observing someone entering credentials or PINs, or viewing sensitive screens, in person.
- **Social engineering (physical)** - Manipulating staff face-to-face to gain access or information.
- **Access control system (ACS)** - The badge/biometric system that logs and controls physical entry to a facility.
- **Dwell time** - How long an unauthorized person remains undetected inside a facility after gaining access.

### Digital track

- **Credential/token theft** - Obtaining a legitimate user's login credentials, API key, or OAuth token, typically via phishing, malware, or a compromised dependency, and using it as if it were your own.
- **Supply-chain compromise** - Inserting malicious code into a trusted software component (a package, extension, or build pipeline) so it's distributed to victims through an otherwise-legitimate update channel.
- **Command-and-control (C2)** - Infrastructure an attacker uses to remotely direct malware or exfiltrate stolen data after an initial compromise.
- **Personal Access Token (PAT) abuse** - Using a stolen or over-permissioned PAT to perform actions (e.g., bulk repository cloning) that look like routine developer activity.

### Shared classification terms

- **Negligent insider** - An employee who causes harm unintentionally (e.g., leaves a door propped open, loses a badge, reuses a password).
- **Malicious insider** - An employee who intentionally misuses physical or digital access to cause harm or exfiltrate assets.
- **Compromised insider** - A legitimate employee whose credentials/badge/access is being used by someone else without their knowledge - the common category both tracks ultimately fall into.

---

## 2. Classification of Threat Types and Sources

<img width="589" height="93" alt="Threat type and source classification table" src="https://github.com/user-attachments/assets/e0026d51-acfe-4c70-a53c-ef88503d49cd" />

### ENISA classification - five types of insider threat

1. **Careless workers** - Mishandle data, break policies, install unauthorized apps.
2. **Inside agents** - Steal information on behalf of outside actors.
3. **Disgruntled employees** - Seek to harm the organization.
4. **Malicious insiders** - Abuse existing privileges for personal gain.
5. **Feckless third-parties** - Compromise security through negligence or misuse.

These five categories apply equally to both tracks: e.g., a "careless worker" can prop open a door (physical) or commit a hardcoded API key to a public repo (digital); a "feckless third-party" can be a contractor with a badge (physical) or a compromised open-source maintainer (digital).

---

## 3. Case Studies

### Physical track: USB Killer (College of Saint Rose, 2019)

A former student used a malicious USB device ("USB killer") to physically destroy 66 workstations and monitors, causing over $50,000 in equipment damage. He faced up to 10 years in prison. This demonstrates that physical access to endpoints is itself an attack surface, not just a data-exfiltration risk.

### Digital track (preview - expanded in Weeks 2–3)

The **Nx Console / GitHub supply-chain breach (May 2026)** and the **`websfm.kz` infrastructure exposure** are this project's digital-track case studies; full timelines, IOCs, and analysis are developed starting Week 2 once OSINT collection was performed.

---

## 4. Supporting Statistics

<img width="975" height="600" alt="week1_physical_stats" src="https://github.com/user-attachments/assets/0e92a1a3-c0ff-4622-bb94-5febb8cbdd0d" />


- **20%** of cybersecurity incidents started or ended with a physical action.
- **54%** of data breaches across all sectors included a physical attack as the main method.
- **72%** of employees consider leaving sensitive information in publicly accessible areas the most serious threat to data security.

These numbers are the reason the physical track is treated as a first-class part of this project rather than a side note to the digital track.

---

## 5. Sources

- ENISA Threat Landscape - *Physical manipulation, damage, theft and loss* (2020): https://www.enisa.europa.eu/publications/physical-manipulation-damage-theft-loss
- *The Threat Intelligence Handbook* (Recorded Future)
- SANS CTI Summit talks; MITRE resources
