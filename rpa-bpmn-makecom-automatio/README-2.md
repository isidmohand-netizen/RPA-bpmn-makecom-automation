# 🎟️ EventPro — Event Ticket Sales & Check-In Automation
> RPA project built with **Make.com** and **BPMN** for the MSc MDAI course  
> *Managing and Automating Processes — Academic Year 2025–2026*

---

## 📹 Demo

[![Watch the demo](https://img.shields.io/badge/▶%20Watch%20Demo-Google%20Drive-blue?style=for-the-badge&logo=google-drive)](https://drive.google.com/file/d/1fAfVH2a1tYQQr4shBWZbpq84_GpjDl03/view?usp=share_link)

The screencast walks through:
- BPMN diagram in Signavio (pools, gateways, message flows)
- Live run of Scenario 1 & 2 — form submission → access pass email with QR codes
- Live run of Scenario 3 — QR scan → "Entry Granted" JSON response → Google Sheet updated

---

## 🎯 Project Goal

**EventPro Solutions** is an event management company (Grenoble, France) running ~40 events/year with up to 5,000 attendees. This project automates the full event registration and check-in lifecycle, replacing a painful manual process:

| Pain Point | Impact |
|---|---|
| Manual QR code & PDF creation | 6–8 hours of repetitive work per 500-attendee event |
| No time-slot enforcement at entry | Long queues, human errors at the door |
| No real-time check-in tracking | Event managers had zero live visibility |
| Re-entry management | Duplicate entries when participants left and returned |
| Data silos between registration & check-in | Hours of manual reconciliation after each event |

---

## ⚙️ How It Works

The automation is split into two phases and three Make.com scenarios:

### Phase 1 — Online Registration
A participant fills out a Google Form → they are added to the attendee database → unique entry and exit QR codes are generated → a personalized access pass PDF is created from a Google Docs template → the PDF is emailed automatically.

### Phase 2 — On-Site Check-In
The participant shows their QR code at the entrance → a webhook is triggered → the system looks up the attendee, validates time slot and entry flag → responds with **Entry Granted** or **Entry Denied** in real time.

---

## 🔄 Make.com Scenarios

### Scenario 1 — Registration & Access Pass Generation
`Google Forms → Google Sheets → OneDrive → Google Docs → Outlook`

13 modules: watches form submissions, adds the participant to the sheet, generates two QR codes (entry + exit), uploads images to OneDrive, creates a personalized PDF from a Google Docs template, and sends it via Outlook.  
**Condition:** filter prevents duplicate emails if the scenario re-runs.

### Scenario 2 — Time Slot Update
`Google Sheets (read) → Router → Google Sheets (update)`

Triggers after Scenario 1. A 3-path Router reads the participant's chosen time slot and writes the correct valid window to the sheet:
- Morning → 08:00–12:00
- Afternoon → 13:00–17:00
- All Day → 08:00–17:00

### Scenario 3 — Entry Check-In via QR Scan
`Webhook → Google Sheets → Webhook response`

Listens for incoming QR scans. Validates three conditions simultaneously: entry flag = 0 (not already inside) + current time ≥ valid_from + current time ≤ valid_to. Returns a JSON response (`ACCEPTED` or `REJECTED`) and updates the sheet with check-in timestamp and entry count.

---

## 🗺️ BPMN Process Model

**View in Signavio:**  
🔗 [Open diagram](https://academic.signavio.com/p/editor?id=64ac6399291943f9862511465f6a8e44)

The model covers the full lifecycle across **3 pools**:
- **Participant** — fills form, receives email, presents QR at entrance
- **Event Management System** — two lanes: Registration Platform (automated) + On-Site Check-In (automated)
- **Cloud Services** — OneDrive, Google Docs, Outlook (modeled as external pool to reflect message-based API integration)

Key elements: 21 tasks, 6 exclusive gateways, 6 message flows, 4 data objects, 1 data store (Google Sheets).

The `.bpmn` file is in the [`bpmn/`](./bpmn/) folder — importable into Signavio or any BPMN-compatible tool.

---

## 📂 Repository Structure

```
rpa-bpmn-makecom-automation/
├── bpmn/
│   └── EventPro_Process.bpmn          # BPMN 2.0 diagram (importable in Signavio)
├── make/
│   ├── Scenario1.blueprint.json       # Registration & access pass generation
│   ├── Scenario2.blueprint.json       # Time slot update
│   └── Scenario3.blueprint.json       # Entry check-in via QR scan
├── docs/
│   └── EventPro_Assignment.pdf        # Full assignment report
└── README.md
```

---

## 🛠️ Tools & Systems

| Tool | Role |
|---|---|
| **Make.com** | RPA automation platform (all 3 scenarios) |
| **Google Forms** | Registration interface |
| **Google Sheets** | Attendee database (replaces SQL) |
| **Google Docs** | Access pass template engine |
| **OneDrive** | QR code image storage |
| **Microsoft Outlook** | Access pass email delivery |
| **Signavio** | BPMN process modelling |
| **Make.com Webhooks** | QR scan check-in trigger |
| **Make.com Barcode module** | QR code generation |

---

## ⚠️ Security Note

The `.json` blueprint files have had all **webhook URLs, API keys, and connection credentials removed**. Replace the placeholder values with your own Make.com connections before importing.

---

## 👤 Author

**Idir Sid Mohand** — MSc MDAI, Grenoble  
[GitHub](https://github.com/isidmohand-netizen)
