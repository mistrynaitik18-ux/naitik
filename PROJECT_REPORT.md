# CyberSentinel IR — Project Report
## IBM Cybersecurity Bootcamp | Final Project Submission

---

## Title Page

| Field | Detail |
|---|---|
| **Project Title** | CyberSentinel IR — Cybersecurity Incident Response Simulator |
| **Course** | IBM Cybersecurity Bootcamp |
| **Submission Type** | Final Capstone Project |
| **Version** | 1.0.0 |
| **Technology** | HTML5, CSS3, Vanilla JavaScript, Web Storage API |
| **Deliverable** | Single self-contained web application (`cybersentinel.html`) |

---

## 1. Abstract

CyberSentinel IR is an interactive, browser-based cybersecurity incident response simulation platform designed to demonstrate practical knowledge of the NIST SP 800-61 incident response lifecycle. The platform provides a full Security Operations Center (SOC) dashboard, incident management workflow, five interactive decision-scoring scenarios, investigation workspace, playbook library, threat intelligence viewer, reports, and audit logs — all delivered as a single HTML file with zero external dependencies.

---

## 2. Problem Statement

### 2.1 Background

Cybersecurity incident response is a critical organizational capability. The ability to detect, triage, contain, eradicate, and recover from security incidents requires both theoretical knowledge and hands-on practice. However, students and early-career analysts rarely have access to real SOC environments for practice.

### 2.2 Gap Identified

Existing educational tools either:
- Are purely theoretical (slide decks, textbooks)
- Require expensive infrastructure (SIEM, EDR, ticketing systems)
- Do not provide interactive, scored decision-making exercises
- Do not cover the complete IR lifecycle from detection to post-incident review

### 2.3 Proposed Solution

Build a fully functional, self-contained IR simulation platform that:
- Requires no infrastructure or installation
- Covers all phases of the NIST SP 800-61 IR lifecycle
- Provides realistic, scored decision-making exercises
- Demonstrates professional SOC tooling patterns
- Persists data for multi-session use

---

## 3. Objectives

| # | Objective | Met? |
|---|---|---|
| 1 | Implement full incident lifecycle (New → Investigating → Closed) | ✅ |
| 2 | Build interactive IR scenario simulator with scoring | ✅ |
| 3 | Create investigation workspace with notes and evidence | ✅ |
| 4 | Provide 5 NIST-aligned response playbooks | ✅ |
| 5 | Display threat intelligence IOCs | ✅ |
| 6 | Generate data-driven reports with date filtering | ✅ |
| 7 | Maintain a read-only audit log | ✅ |
| 8 | Persist all data across sessions | ✅ |
| 9 | Export incident and audit data to CSV | ✅ |
| 10 | Deliver as a single file with no build process | ✅ |

---

## 4. System Architecture

### 4.1 Architecture Pattern

The application uses a **Single-Page Application (SPA)** pattern within a single HTML file:

```
┌─────────────────────────────────────────────────────────────┐
│                     cybersentinel.html                       │
├─────────────┬──────────────────────────────────────────────-┤
│   HTML      │  Page templates, modals, navigation structure  │
├─────────────┼─────────────────────────────────────────────-─┤
│   CSS       │  Dark SOC theme, responsive grid, charts       │
├─────────────┼──────────────────────────────────────────────-┤
│  JavaScript │                                                 │
│  ├ Router   │  showPage() — hash-free SPA navigation         │
│  ├ DataStore│  db object ↔ localStorage (JSON)               │
│  ├ Dashboard│  Stats, charts, timeline rendering             │
│  ├ Incidents│  CRUD operations, filtering, export            │
│  ├ Simulator│  Scenario engine, scoring, feedback            │
│  ├ Invest.  │  Notes, evidence, response actions             │
│  ├ Playbooks│  Step/checklist management                     │
│  ├ ThreatIn.│  IOC table with search/filter                  │
│  ├ Reports  │  Date-range metrics, chart rendering           │
│  └ Audit    │  Append-only event log                        │
└─────────────┴──────────────────────────────────────────────-┘
                              │
                    localStorage
                    (cybersentinel_data)
```

### 4.2 Data Model

**Incident Object:**
```json
{
  "id": "INC-001",
  "title": "Phishing Campaign Targeting Finance",
  "category": "Phishing",
  "severity": "High",
  "status": "Investigating",
  "description": "...",
  "host": "FINANCE-PC-07",
  "sourceIp": "192.0.2.45",
  "analyst": "Jane Doe",
  "detected": "2024-11-15T08:22:00",
  "updated": "2024-11-15T10:45:00",
  "timeline": [ { "ts": "...", "type": "info", "text": "..." } ],
  "resolution": ""
}
```

**Audit Entry:**
```json
{
  "ts": "2024-11-15T10:45:00",
  "action": "Status Changed",
  "incId": "INC-001",
  "detail": "Acknowledged → Investigating",
  "analyst": "Jane Doe"
}
```

**IOC Entry:**
```json
{
  "indicator": "192.0.2.45",
  "type": "IP Address",
  "confidence": "High",
  "incident": "INC-001",
  "firstSeen": "2024-11-15",
  "lastSeen": "2024-11-15",
  "notes": "Phishing relay server (simulated)"
}
```

### 4.3 Data Flow

```
User Action → Event Handler → db Object Update → localStorage.setItem()
                                              ↓
                              UI Re-render (DOM manipulation)
                                              ↓
                              Audit Log Entry Appended
```

---

## 5. Module Descriptions

### 5.1 Overview Dashboard
The dashboard aggregates live data from `db.incidents` to compute:
- **Summary cards**: Count queries filtered by severity and status
- **MTTA** (Mean Time to Acknowledge): Average of `(first_non_new_update - detected)` across all non-New incidents
- **MTTR** (Mean Time to Resolve): Average of `(closed_updated - detected)` across all Closed incidents
- **Trend chart**: CSS bar chart of daily incident counts
- **Donut charts**: SVG-based proportional ring charts for severity and status distribution
- **Category chart**: CSS bar chart grouped by incident category

### 5.2 Incident Management
Implements the full incident workflow:

```
New → Acknowledged → Investigating → Containment → Eradication → Recovery → Closed
```

Features:
- Client-side search across ID and title fields
- Multi-dimensional filtering (severity × status × category)
- Modal-based create/edit forms with validation
- Confirmation dialog for close action (requires resolution text)
- Reopen functionality for closed incidents
- CSV export using Blob API

### 5.3 Incident Response Simulator

The simulator engine processes 5 scenarios, each containing 3 decision steps:

```
Scenario Data → renderSimStep() → User Choice → makeChoice(idx)
                                              ↓
                                  Score increment (if correct)
                                  Feedback display
                                  simState.choices[] append
                                              ↓
                                  nextStep() → renderSimStep()
                                           → showSimResults() (if last step)
```

**Scoring:**
- +1 point for each correct decision
- Maximum 3 points per scenario
- Grade: ≥80% = Excellent, ≥60% = Good, ≥40% = Needs Improvement, <40% = Review Required

**Scenarios:**
| ID | Title | Type |
|---|---|---|
| phishing | Phishing Email Attack | Social Engineering |
| bruteforce | Brute-Force Login Attack | Credential Attack |
| malware | Malware Detection | Endpoint Security |
| network | Suspicious Network Activity | Network Security |
| unauthorized | Unauthorized Account Access | Identity & Access |

### 5.4 Investigation Workspace
Per-incident workspace storing:
- **Notes** (`db.notes[incId]`): timestamped, attributed to analyst
- **Evidence** (`db.evidence[incId]`): described artifact with classification state machine (Unreviewed → Under Review → Confirmed Relevant / Not Relevant)
- **Response Actions** (`db.actions[incId]`): timestamped action records appended to incident timeline

### 5.5 Response Playbooks
Five 7-phase NIST SP 800-61 aligned playbooks:
1. Preparation
2. Detection & Identification
3. Initial Triage
4. Containment
5. Eradication
6. Recovery
7. Post-Incident Review

Progress stored in `db.playbookProgress` keyed by `playbook_key.step_index_item_index`.

### 5.6 Threat Intelligence
Static IOC table with 10 seeded entries. All data uses:
- **IP addresses**: RFC 5737 documentation ranges (192.0.2.0/24, 198.51.100.0/24, 203.0.113.0/24)
- **Domains**: RFC 2606 reserved `example.com`
- **Hashes**: Fictional hex strings not corresponding to real malware
- **Emails**: Fictional addresses at `example.com`

### 5.7 Reports & Analytics
Date-range filtered view of incident data showing:
- Total incidents in range, closure rate, MTTA, MTTR
- Severity distribution bar chart
- Category distribution bar chart
- Analyst workload bar chart
- Simulation performance history table
- Playbook completion progress bars

### 5.8 Audit Logs
All significant actions are recorded via `addAudit(action, incId, detail)`:
- Incident created/updated/closed/reopened
- Severity changed
- Analyst assigned
- Note added
- Evidence added
- Response action recorded
- Report/audit CSV exported

The audit table is rendered read-only. No UI element allows modification or deletion of audit records.

---

## 6. Implementation

### 6.1 Key Design Decisions

| Decision | Rationale |
|---|---|
| Single HTML file | Zero setup friction; runs anywhere; easy to distribute and evaluate |
| Vanilla JS over React | No build tools required; no npm dependencies; simpler deployment |
| localStorage over SQLite | No backend server required; persists across sessions in browser |
| CSS charts over Recharts | No CDN dependency; fully self-contained; sufficient for educational use |
| Fictional data (RFC ranges) | Ethical compliance; cannot be mistaken for real threat data |

### 6.2 UI/UX Design

- **Theme**: Dark navy (#0d1117) background, charcoal (#161b22) surfaces — matching professional SOC platforms
- **Accents**: Cyan (#58a6ff) for navigation and info, blue (#1f6feb) for primary actions
- **Severity colours**: Red (#f85149) Critical, Orange (#f0883e) High, Yellow (#e3b341) Medium, Green (#56d364) Low
- **Typography**: System UI font stack for cross-platform consistency
- **Responsive**: CSS Grid with responsive breakpoints; sidebar collapses on mobile

### 6.3 Security Considerations (Ethical Design)

- No external HTTP requests (no CORS issues, no data leakage)
- All sample data uses reserved/fictional values
- No credential storage of any kind
- Clear visual labelling of all simulated/fictional data
- Disclaimer on Settings page and in Threat Intel page

---

## 7. Testing Summary

### 7.1 Functional Test Results

| Test Case | Result |
|---|---|
| Application loads with seeded data | ✅ Pass |
| Dashboard metrics match incident data | ✅ Pass |
| All 10 nav items navigate correctly | ✅ Pass |
| Create incident — validates required fields | ✅ Pass |
| Create incident — appears in table and dashboard | ✅ Pass |
| Edit incident — changes persist | ✅ Pass |
| Severity change — audit log entry created | ✅ Pass |
| Close incident — requires resolution summary | ✅ Pass |
| Reopen closed incident | ✅ Pass |
| CSV export — downloads incident data | ✅ Pass |
| Simulator — all 5 scenarios playable | ✅ Pass |
| Simulator — correct decisions score +1 | ✅ Pass |
| Simulator — feedback shown per decision | ✅ Pass |
| Simulator — results page shows grade | ✅ Pass |
| Investigation notes persist | ✅ Pass |
| Evidence classification saves | ✅ Pass |
| Playbook checkbox state persists | ✅ Pass |
| IOC search and filter work | ✅ Pass |
| Reports date filter narrows data | ✅ Pass |
| MTTA/MTTR calculated from timestamps | ✅ Pass |
| Audit log is read-only | ✅ Pass |
| Data survives page refresh | ✅ Pass |
| Settings analyst name saves | ✅ Pass |
| Data reset with RESET confirmation | ✅ Pass |

**Total: 24/24 tests passing**

### 7.2 Browser Compatibility

| Browser | Tested | Result |
|---|---|---|
| Chrome 120+ | ✅ | Fully functional |
| Firefox 120+ | ✅ | Fully functional |
| Edge 120+ | ✅ | Fully functional |
| Safari 17+ | ✅ | Fully functional |

---

## 8. Cybersecurity Concepts Demonstrated

| Concept | Where Demonstrated |
|---|---|
| NIST SP 800-61 IR Lifecycle | Incident status workflow + Playbooks |
| Threat Classification | Severity (Critical/High/Medium/Low) + Category taxonomy |
| IOC Identification | Threat Intelligence module |
| Evidence Handling | Investigation workspace evidence register |
| Chain of Custody | Audit log + timestamped timeline |
| Social Engineering (Phishing) | Simulator Scenario A |
| Credential Attacks | Simulator Scenario B |
| Endpoint Security / EDR | Simulator Scenario C |
| Data Exfiltration Detection | Simulator Scenario D |
| Identity & Access Management | Simulator Scenario E |
| MTTA / MTTR Metrics | Dashboard + Reports |
| Defense in Depth | Playbook hardening recommendations |
| Least Privilege | Account compromise response playbook |
| MFA / Zero Trust | Multiple scenarios recommend MFA |

---

## 9. Conclusion

CyberSentinel IR successfully delivers a complete, functional cybersecurity incident response simulation platform as a single self-contained HTML file. The project demonstrates:

1. **Technical depth**: Full CRUD application with client-side routing, data persistence, CSV export, chart rendering, and a multi-step scoring engine — all without external dependencies

2. **Cybersecurity knowledge**: Accurate representation of IR workflows, attack patterns, defensive measures, and NIST SP 800-61 procedures across 5 attack scenario types

3. **Professional quality**: Enterprise SOC UI/UX, consistent design language, responsive layout, proper error handling, and audit trail

4. **Ethical design**: All simulated data uses reserved/fictional values; no real systems are affected; all content is clearly labelled as educational simulation

The platform is immediately usable for practising and demonstrating incident response skills, with a clear upgrade path to a full Flask/React/SQLite architecture for production use.

---

## 10. References

- NIST SP 800-61 Rev 2: *Computer Security Incident Handling Guide*
- SANS Institute: *Incident Handler's Handbook*
- MITRE ATT&CK Framework: *Enterprise Techniques*
- RFC 5737: *IPv4 Address Blocks Reserved for Documentation*
- RFC 2606: *Reserved Top Level DNS Names*
- IBM Security: *Cost of a Data Breach Report 2024*

---

*CyberSentinel IR v1.0.0 — IBM Cybersecurity Bootcamp Capstone Project*
