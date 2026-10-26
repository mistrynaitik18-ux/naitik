# 🛡️ CyberSentinel IR — Cybersecurity Incident Response Simulator

> **IBM Cybersecurity Bootcamp Project** — A fully functional, single-file web application that simulates a professional Security Operations Center (SOC) incident response platform.

---

## 📌 Table of Contents

1. [Introduction](#introduction)
2. [Problem Statement](#problem-statement)
3. [Objectives](#objectives)
4. [Features](#features)
5. [Architecture Overview](#architecture-overview)
6. [Technology Stack](#technology-stack)
7. [Project Structure](#project-structure)
8. [Prerequisites](#prerequisites)
9. [Installation & Running](#installation--running)
10. [Application Modules](#application-modules)
11. [Demo Data](#demo-data)
12. [Testing](#testing)
13. [Limitations](#limitations)
14. [Future Improvements](#future-improvements)
15. [Disclaimer](#disclaimer)

---

## Introduction

**CyberSentinel IR** is an educational cybersecurity incident response simulation platform. It allows students, analysts, and security professionals to practise incident triage, investigation, containment, recovery, and reporting in a completely safe, browser-based environment with no external dependencies.

---

## Problem Statement

Cybersecurity students often learn theory without hands-on exposure to real-world incident response workflows. Building a functioning SOC IR platform as a learning tool bridges this gap — providing realistic scenarios, structured decision-making exercises, and professional tooling patterns without requiring access to real infrastructure.

---

## Objectives

- Demonstrate the full NIST SP 800-61 incident response lifecycle
- Simulate realistic IR scenarios covering common attack types
- Provide an interactive decision-scoring system with educational feedback
- Show professional-grade SOC UI/UX patterns
- Enable incident creation, investigation, closure, and reporting
- Capture audit trails and produce exportable reports

---

## Features

| Module | Description |
|---|---|
| 📊 **Dashboard** | Live stats: total/open/critical incidents, MTTA, MTTR, trend charts, severity/category/status distributions |
| 🚨 **Incident Management** | Full CRUD — create, edit, triage, investigate, close, export to CSV |
| 🎮 **IR Simulator** | 5 scored scenarios (Phishing, Brute-Force, Malware, Network, Unauthorized Access) with decision feedback |
| 🔍 **Investigation Workspace** | Per-incident notes, evidence register (with classification), timeline, response actions |
| 📋 **Response Playbooks** | 5 interactive playbooks with checkable 7-phase procedures |
| 🌐 **Threat Intelligence** | IOC table (IP, domain, hash, email) — searchable and filterable |
| 📈 **Reports & Analytics** | Date-filtered metrics, severity/category charts, analyst workload, sim scores, CSV export |
| 📝 **Audit Logs** | Read-only log of all significant actions with timestamp and analyst |
| ⚙️ **Settings** | Analyst name, refresh preference, data/simulation reset |

---

## Architecture Overview

```
cybersentinel.html  (single file)
│
├── CSS (embedded)          Dark-theme SOC styling, responsive grid, charts
├── HTML (embedded)         All page templates, modals, navigation
└── JavaScript (embedded)
    ├── Data Layer          localStorage-backed JSON store (db object)
    ├── Navigation          showPage() router, sidebar toggle
    ├── Dashboard           renderDashboard(), SVG donut charts, CSS bar charts
    ├── Incidents           CRUD, filtering, sorting, CSV export
    ├── Simulator           SCENARIOS data, scoring engine, feedback system
    ├── Investigation       Per-incident notes, evidence, response actions
    ├── Playbooks           PLAYBOOKS data, checkbox progress persistence
    ├── Threat Intel        IOC table with search/filter
    ├── Reports             Date-range metrics, workload charts
    └── Audit               Append-only audit log, CSV export
```

**Data persistence:** All data is stored in `localStorage` under the key `cybersentinel_data` as a JSON object. Data survives page refreshes and browser restarts.

---

## Technology Stack

| Layer | Technology |
|---|---|
| **Markup** | HTML5 |
| **Styling** | CSS3 (custom, no frameworks — dark SOC theme) |
| **Logic** | Vanilla JavaScript (ES2020+) |
| **Charts** | Pure CSS bar charts + inline SVG donut charts |
| **Storage** | Web Storage API (localStorage) |
| **Icons** | Unicode/emoji (no external icon library) |
| **Export** | Blob API + URL.createObjectURL (CSV download) |
| **Deployment** | Static file — open directly in browser |

---

## Project Structure

```
naitik_bootcamp_project/
├── cybersentinel.html      ← Main application (open this file)
├── requirements.txt        ← Dependency documentation
├── README.md               ← This file
└── PROJECT_REPORT.md       ← College/bootcamp project report
```

---

## Prerequisites

- Any modern web browser (Chrome 90+, Firefox 88+, Edge 90+, Safari 14+)
- No Node.js, Python, or database installation required
- No internet connection required after downloading the file

---

## Installation & Running

### Option 1 — Direct (recommended)

```bash
# Simply open the file in your browser
start cybersentinel.html          # Windows
open cybersentinel.html           # macOS
xdg-open cybersentinel.html       # Linux
```

### Option 2 — Local development server (optional)

```bash
# Python (built-in)
python -m http.server 8080
# Then open: http://localhost:8080/cybersentinel.html

# Node.js
npx serve .
# Then open: http://localhost:3000/cybersentinel.html

# VS Code
# Right-click cybersentinel.html → Open with Live Server
```

### No build step required. No npm install. No pip install.

---

## Application Modules

### 1. Dashboard
- Summary cards: Total, Open, Critical/High, Investigating, Resolved, MTTA, MTTR, Sim Sessions
- Incident trend bar chart (7-day)
- Severity and Status donut charts
- Category bar chart
- Recent incidents table
- Recent activity timeline
- Quick-action buttons

### 2. Incident Management
- Searchable, filterable, sortable table
- Filter by: severity, status, category
- Create incident via validated modal form
- View full incident details page
- Edit incident (title, severity, status, analyst, description, add notes)
- Add investigation notes, evidence, response actions
- Close with resolution summary (confirmation required)
- Reopen closed incidents
- Export all incidents to CSV

### 3. Incident Simulator (5 scenarios)
| Scenario | Difficulty | Focus |
|---|---|---|
| Phishing Email | Easy | Email analysis, escalation, post-incident |
| Brute-Force Login | Medium | Log analysis, containment, hardening |
| Malware Detection | Medium | EDR response, scope assessment, eradication |
| Suspicious Network Activity | Hard | DLP analysis, exfiltration investigation |
| Unauthorized Account Access | Medium | Authentication anomaly, identity response |

Each scenario: 3 decision points × 3 choices, feedback per decision, final score with grade, recommendations.

### 4. Investigation Workspace
- Select any open incident
- View incident overview + event timeline
- Add/delete investigation notes
- Add/classify evidence (Unreviewed → Under Review → Confirmed Relevant / Not Relevant)
- Recommended next steps checklist

### 5. Response Playbooks (5 playbooks)
- Phishing / Malware / Brute-Force / Compromised Account / Suspicious Network
- 7-phase NIST-aligned structure per playbook
- Expandable steps with checkable items
- Progress bar and completion tracking

### 6. Threat Intelligence
- 10 seeded IOCs: IPs (RFC 5737), domains (.example.com), file hashes (fictional), emails
- Filter by type, search by indicator
- Confidence level, associated incident, first/last seen, analyst notes
- Click incident ID to open incident detail

### 7. Reports & Analytics
- Date range filter
- Metrics: total incidents, closure rate, MTTA, MTTR
- Charts: severity distribution, category distribution, analyst workload
- Simulation performance history
- Playbook completion rates
- CSV export

### 8. Audit Logs
- Auto-captured events: create, update, severity change, assignment, note add, status change, action, close/reopen, export
- Read-only table (no edit/delete in UI)
- CSV export

### 9. Settings
- Analyst name configuration
- Data reset (with double-confirmation)
- Simulation score reset
- Technology stack and learning outcomes display
- Disclaimer

---

## Demo Data

On first load, the application seeds:

**10 incidents** covering all categories and severity levels:
- INC-001: Phishing Campaign — Finance Team (High / Investigating)
- INC-002: Ransomware on Endpoint (Critical / Containment)
- INC-003: Brute-Force on VPN Gateway (High / Acknowledged)
- INC-004: Unauthorized Admin Access (Critical / Eradication)
- INC-005: Suspicious Data Exfiltration (High / Investigating)
- INC-006: Malware Trojan.GenericKD (High / Recovery)
- INC-007: Internal Port Scan (Medium / New)
- INC-008: Phishing Credential Harvest (Medium / Closed)
- INC-009: Suspicious PowerShell (High / New)
- INC-010: MFA Fatigue Attack (Medium / Acknowledged)

**10 IOCs**: IP addresses, domains, file hashes, email addresses — all fictional/simulated  
**8 audit log entries**: seeded historical actions

All data is clearly marked as simulated. No real threat data is included.

---

## Testing

This project uses manual functional testing since it is a single-file browser application.

### Test Checklist

```
[ ] Open cybersentinel.html in browser — loads without errors
[ ] Dashboard displays all stat cards and charts
[ ] All 10 sidebar navigation items load correct pages
[ ] Create a new incident — appears in table and dashboard
[ ] Edit incident severity — change recorded in audit log
[ ] Open incident detail — timeline, tabs visible
[ ] Add investigation note — persists after page change
[ ] Add evidence — classification works
[ ] Record response action — appears in Actions tab
[ ] Close incident with resolution — status changes to Closed
[ ] Reopen closed incident — status changes back
[ ] Filter incidents by severity/status/category
[ ] Search incidents by ID and title
[ ] Export incidents CSV — downloads correctly
[ ] Run all 5 simulator scenarios — scoring works
[ ] Simulator feedback shown after each choice
[ ] Simulator result page shows correct score and grade
[ ] Playbook checkboxes persist after page switch
[ ] IOC table search and filter work
[ ] Reports page shows charts derived from actual data
[ ] Date filter narrows report data
[ ] Audit log shows all actions taken
[ ] Export audit CSV — downloads correctly
[ ] Settings save analyst name
[ ] Data persists after browser refresh (localStorage)
[ ] Global search bar finds incidents
[ ] Notification badge shows critical incident count
```

### Browser Console Check
Open DevTools (F12) → Console — no errors should appear on load or during navigation.

---

## Limitations

1. **No real backend** — Data stored in localStorage (browser-only, cleared if browser data is wiped)
2. **No multi-user support** — Single analyst session only
3. **No authentication** — No login screen (intentionally omitted per scope)
4. **Charts are CSS/SVG** — Not interactive like Recharts/Chart.js (no hover tooltips)
5. **No PDF export** — CSV only
6. **No real-time updates** — Refresh is manual
7. **No email integration** — Cannot send real notifications
8. **localStorage cap** — ~5MB; sufficient for hundreds of incidents

---

## Future Improvements

- [ ] Python/Flask REST API backend with SQLite persistence
- [ ] React + TypeScript frontend rewrite
- [ ] Multi-user support with JWT authentication
- [ ] Interactive Recharts visualizations
- [ ] PDF report generation
- [ ] Real SIEM/SOAR integration (Elastic SIEM, Splunk)
- [ ] STIX/TAXII threat intelligence feed import
- [ ] Automated playbook execution hooks
- [ ] Dark/light theme toggle
- [ ] Email/Slack notification integration

---

## Disclaimer

**This application is a purely educational simulation.**

- It does not send real phishing emails
- It does not execute malware or malicious code
- It does not attempt unauthorized access to any system
- It does not interact with any external network or system
- All IP addresses use IANA-reserved documentation ranges (RFC 5737): `192.0.2.x`, `198.51.100.x`, `203.0.113.x`
- All domains use the reserved `example.com` domain (RFC 2606)
- All file hashes are fictional and do not represent real malware
- All incidents and indicators of compromise are entirely fabricated for educational purposes

Use of this application is intended solely for cybersecurity education and training. The authors accept no responsibility for misuse.

---

*Built for IBM Cybersecurity Bootcamp — CyberSentinel IR v1.0.0*
