# 🛡️ Sakhya — Anonymous Incident Reporting & Safety Mapping

> **"Report without fear. Know before you go."**

A privacy-first platform for reporting harassment and unsafe incidents anonymously, with AI-powered validation and real-time heatmap visualization of unsafe zones.

> ⚠️ **Zero identity collection.** Sakhya never asks for your name, phone number, or email. The system is designed so that even we cannot identify who filed a report.

---

## 🏠 Live Demo

👉 **[sakhya.vercel.app](https://sakhya.vercel.app)** *(replace with actual URL)*

---

## 📋 Table of Contents

1. [The Problem](#-the-problem)
2. [Solution](#-solution)
3. [System Workflow](#️-system-workflow)
4. [AI & Verification Layer](#-ai--verification-layer)
5. [Privacy & Security](#-privacy--security)
6. [Features](#-features)
7. [Tech Stack](#-tech-stack)
8. [Repository Structure](#-repository-structure)
9. [Impact](#-impact)
10. [Future Scope](#-future-scope)

---

## 🚨 The Problem

**Women underreport incidents of harassment at an alarming rate — not because it doesn't happen, but because reporting feels unsafe.**

| Why Women Don't Report | Reality |
|---|---|
| Fear of identification | "What if someone finds out it was me?" |
| Distrust of authorities | Reports are often dismissed or minimized |
| Social stigma | Fear of judgment from community or family |
| No immediate value | Filing a police report doesn't make the street safer tomorrow |
| Complex processes | Lengthy paperwork, in-person visits, re-traumatization |

Studies estimate **only 1 in 10 sexual harassment incidents** are formally reported. The gap between lived experience and official data means cities cannot identify danger zones, allocate resources, or design safer infrastructure.

**Sakhya exists to close that gap — without ever putting the reporter at risk.**

---

## 💡 Solution

Sakhya is a **zero-identity, crowd-sourced safety intelligence platform**. Users report incidents anonymously, the system validates reports using AI and behavioral analysis, and the aggregated data surfaces as live safety heatmaps that benefit the entire community.

The platform operates on three principles:
- **Anonymity is non-negotiable** — no account creation, no tracking, no identity inference
- **Trust through verification** — AI filters spam and malicious reports before they reach the map
- **Community as infrastructure** — every verified report makes the map more accurate for everyone

---

## ⚙️ System Workflow

```
[1] Incident Report Submitted
    └── User opens app (no login required)
    └── Selects incident type (harassment, stalking, unsafe area, etc.)
    └── Geo-tag captured via GPS or manual map pin
    └── Optional: brief text description, time of day, photo upload
    └── Zero personal identifiers collected
         ↓
[2] Privacy Sanitization Layer
    └── IP address stripped before storage
    └── Metadata (EXIF data) purged from any uploaded images
    └── Timestamp fuzzing applied (±15 min) to prevent precise identification
    └── Session tokens are ephemeral — not linked to device fingerprint
         ↓
[3] AI Validation Pipeline
    └── NLP model analyzes text for spam, gibberish, or hateful/irrelevant content
    └── Duplicate detection: checks for near-identical reports within same zone & window
    └── Geospatial anomaly check: flags implausible locations (middle of a lake, etc.)
    └── Behavioral scoring: detects rapid-fire submissions from same session
         ↓
[4] Multi-Layer Verification
    └── Reports scored across: content quality, location plausibility, temporal context
    └── High-confidence reports → auto-approved → added to heatmap immediately
    └── Medium-confidence reports → community corroboration queue (N similar reports confirm)
    └── Low-confidence / flagged reports → admin review queue
         ↓
[5] Heatmap Update
    └── Verified reports contribute to zone risk score
    └── Heatmap recalculates in real time
    └── Older reports decay in weight over time (recency-weighted scoring)
    └── High-density clusters surface as "active unsafe zones"
         ↓
[6] Admin Dashboard (Authorities / NGOs)
    └── Anonymized cluster data exported for civic action
    └── Trend analysis: time-of-day, incident type, zone patterns
    └── Repeat-pattern alerts for persistently flagged areas
    └── No raw report data is ever shared — only aggregated, anonymized intelligence
```

---

## 🧠 AI & Verification Layer

### 1. NLP Report Validation

Every text description passes through a two-stage NLP pipeline before the report is accepted:

- **Stage 1 — Content Filter:** Classifies report as relevant (harassment, unsafe area, threat) vs. irrelevant (spam, test entry, unrelated content). Uses a fine-tuned text classifier trained on safety-report datasets.
- **Stage 2 — Quality Scorer:** Assigns a credibility score based on specificity, coherent language structure, and contextual alignment with the selected incident type.

```
credibility_score = f(
    content_relevance,      // does text match incident category?
    specificity_score,      // vague vs. detailed description
    language_coherence,     // gibberish / bot-generated text detection
    temporal_consistency    // does reported time match submission time window?
)
```

### 2. Duplicate & Cluster Detection

The system prevents map manipulation via duplicate submissions:

- Reports within the same **500m radius + 1-hour window** are grouped into a single event cluster
- A second report strengthens confidence in the cluster; it does not inflate incident count
- Rapid-fire submissions (>3 reports within 5 minutes from one session) are rate-limited and flagged

### 3. Verification Decision Engine

```
final_status:
    score > 0.75  →  Auto-approved, added to heatmap
    score 0.45–0.75  →  Queued — requires corroboration (2+ similar reports) OR admin review
    score < 0.45  →  Rejected silently (no response to submitter to prevent gaming)
```

Rejected reports receive no feedback, preventing adversarial actors from reverse-engineering the scoring system.

### 4. Heatmap Risk Scoring

Zone risk is not a raw count — it is a weighted score:

```
zone_risk = Σ (report_weight × recency_decay × verification_confidence)

recency_decay:
    < 24 hours   →  1.0x (full weight)
    1–7 days     →  0.7x
    7–30 days    →  0.4x
    > 30 days    →  0.15x (historical context only)
```

This ensures the map reflects current conditions, not a permanent stigma on any area.

---

## 🔐 Privacy & Security

> Privacy is not a feature in Sakhya — it is the foundation. Every architectural decision starts from the question: *"Could this be used to identify the reporter?"*

| Privacy Control | Implementation |
|---|---|
| **No account required** | Zero sign-up, zero login — the app is fully functional without any identity |
| **IP stripping** | IP addresses are dropped at the API gateway before any data reaches the database |
| **EXIF purging** | All image metadata (device model, GPS coordinates embedded in photos) is stripped on upload |
| **Timestamp fuzzing** | Reported time is offset by ±15 minutes before storage to prevent timeline-based identification |
| **No device fingerprinting** | Session tokens are randomized per submission and not linked to device or browser attributes |
| **Ephemeral sessions** | No cookies, no localStorage tracking, no cross-session linkage |
| **Encrypted at rest** | All report data in the database is AES-256 encrypted |
| **Aggregation-only exports** | Admin dashboard and external exports contain only aggregated zone data — no individual reports |
| **Right to erasure** | Since no identity is stored, there is nothing to link back to a person — erasure is structurally guaranteed |

### Threat Model

We explicitly designed against these attack vectors:

| Threat | Defense |
|---|---|
| Authority or bad actor requesting reporter identity | Identity was never collected — there is nothing to hand over |
| Correlating submission time to a specific person | Timestamp fuzzing + IP stripping breaks time-based correlation |
| Fake/malicious reports flooding the map | NLP validation + duplicate detection + rate limiting |
| Photo metadata revealing reporter location | Server-side EXIF stripping before storage |
| Map used to stigmatize a neighborhood permanently | Recency decay ensures old reports fade; clusters expire without new activity |

---

## ✨ Features

### For Users (Reporters)
- **One-tap anonymous reporting** — no sign-up, no login
- **Incident categorization** — verbal harassment, physical threat, stalking, unsafe infrastructure, poor lighting, and more
- **GPS auto-tagging** or manual map pin drop
- **Time-of-day context** — morning / afternoon / night
- **Optional photo attachment** (EXIF auto-stripped)
- **Live safety heatmap** — see verified incidents near you before you travel

### For Administrators / Partner NGOs
- **Aggregated heatmap dashboard** — zone-level risk scores with trend lines
- **Incident type breakdown** — what kind of incidents dominate each area
- **Time-pattern analysis** — peak danger hours per zone
- **Review queue** — ambiguous reports surfaced for human review with AI score breakdown
- **Data export (anonymized)** — CSV/GeoJSON of cluster-level data for civic planning

---

## 🧱 Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React.js | Responsive web app, heatmap rendering, report submission UI |
| Backend | Node.js (Express) | REST API, report ingestion, validation orchestration |
| Database | MongoDB | Flexible schema for incident reports, clusters, zone scores |
| Maps | Google Maps API / Mapbox GL | Heatmap visualization, geo-tagging, zone rendering |
| AI / NLP | Python (FastAPI microservice) | Report validation, spam detection, credibility scoring |
| NLP Model | spaCy + custom classifier | Text classification, gibberish detection |
| Image Processing | Sharp (Node.js) | Server-side EXIF metadata stripping |
| Auth (Admin only) | JWT + bcrypt | Secure admin dashboard access |
| Hosting | Vercel (Frontend) + Render (Backend) | Fast deployment, free tier for demo |
| Encryption | AES-256 (at-rest) | Report data encryption in MongoDB |

---

## 📁 Repository Structure

```
sakhya/
├── README.md
├── .env.example
│
├── client/                        # React frontend
│   ├── public/
│   └── src/
│       ├── pages/
│       │   ├── Home.jsx           # Landing page with live heatmap
│       │   ├── Report.jsx         # Anonymous report submission flow
│       │   └── AdminDashboard.jsx # Admin analytics and review queue
│       ├── components/
│       │   ├── HeatMap.jsx        # Mapbox/Google Maps heatmap layer
│       │   ├── ReportForm.jsx     # Zero-identity report form
│       │   ├── ZoneDetail.jsx     # Click-on-zone incident breakdown
│       │   └── ReviewQueue.jsx    # Admin report review component
│       └── api/                   # Axios API client
│
├── server/                        # Node.js / Express backend
│   ├── routes/
│   │   ├── reports.js             # Report ingestion + sanitization
│   │   ├── heatmap.js             # Zone aggregation + risk score API
│   │   └── admin.js               # Admin dashboard + review queue API
│   ├── middleware/
│   │   ├── stripMetadata.js       # EXIF purge middleware
│   │   └── rateLimit.js           # Submission rate limiter
│   ├── models/
│   │   ├── Report.js              # MongoDB report schema
│   │   └── Zone.js                # Zone risk score schema
│   └── utils/
│       ├── fuzzTimestamp.js       # ±15 min timestamp offset
│       └── geoCluster.js          # Spatial cluster grouping logic
│
├── ai-service/                    # Python FastAPI NLP microservice
│   ├── main.py                    # Validation endpoint
│   ├── models/
│   │   ├── classifier.py          # Text relevance classifier
│   │   └── scorer.py              # Credibility scoring logic
│   ├── training/                  # Model training scripts + artifacts
│   └── requirements.txt
│
└── docs/
    ├── privacy_design.md          # Full privacy threat model
    ├── ai_pipeline.md             # NLP validation technical spec
    └── api_reference.md           # REST API documentation
```

---

## 📊 Impact

### The Problem With Official Data

Police-recorded harassment data is a fraction of actual incidents. Sakhya creates a **parallel safety intelligence layer** built from lived experience rather than formal complaints.

| Metric | Traditional Reporting | Sakhya |
|---|---|---|
| Identity required | Yes | Never |
| Time to file | 30–60 minutes | Under 60 seconds |
| Fear of retaliation | High | Structurally eliminated |
| Data reaches decision-makers | Months (if ever) | Real-time |
| Community benefit | None (private record) | Every report improves the map |

### Who Benefits

- **Individual users** — know which routes and areas to avoid, especially at night
- **Urban planners and municipalities** — evidence-based data for lighting, policing, and infrastructure decisions
- **NGOs and safety advocates** — ground-truth data to support campaigns and policy work
- **Researchers** — anonymized, aggregated datasets for gender safety research (with consent)

---

## 🔮 Future Scope

### 🗺️ Safe Route Navigation
Integrate heatmap risk scores with routing APIs to suggest paths that minimize exposure to high-risk zones — not just the shortest path, but the safest one.

### 🔁 Repeat Offender Pattern Detection
Cluster analysis across incident descriptions to identify potential behavioral patterns (same area, same time, similar descriptions) — flagged anonymously for authority attention without revealing reporters.

### 🆘 SOS & Trusted Contact Alerts
An opt-in emergency mode where a user can broadcast their live location to pre-saved trusted contacts with a single tap — completely separate from the anonymous reporting flow.

### 💬 Multilingual Support
NLP pipeline extended to support Hindi, Tamil, Bengali, and other regional languages — ensuring accessibility for users who aren't comfortable reporting in English.

### 📱 Offline-First Mobile App
A native Android app that queues reports locally when offline and syncs when connected — critical for users in areas with poor connectivity.

### 🤝 Civic API
A public anonymized data API for municipalities, researchers, and NGOs to integrate Sakhya's zone intelligence into their own safety planning tools.

---

## 🏁 Summary

Sakhya is built on a simple belief: **the act of reporting should never put someone at greater risk than the incident itself.**

By removing every barrier — identity, paperwork, fear of exposure — and replacing it with a fast, anonymous, AI-validated system, Sakhya turns individual experiences into collective safety intelligence.

Every verified report makes the map more accurate. Every accurate map helps someone make a safer choice.

> *Built to amplify voices that have been silenced by fear. Built for every person who thought "it's not worth reporting."*

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first.
Please read our [privacy design principles](docs/privacy_design.md) before contributing — all features must pass the identity-safety review.

---

## 📄 License

MIT License — see `LICENSE` for details.
