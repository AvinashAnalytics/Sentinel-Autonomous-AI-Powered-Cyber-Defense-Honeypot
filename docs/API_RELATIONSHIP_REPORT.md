# Sentinel API-HTML Relationship Report

This report documents the interaction between the Sentinel frontend (HTML/JS) and the backend API architecture.

## 1. Frontend Architecture Overview
The Sentinel platform utilizes a Single-Script Engine (`dashboard.js`) that powers the dashboard. This ensures that features remain synchronized regardless of the view.

| Frontend File | Purpose | Primary UI Focus |
| :--- | :--- | :--- |
| `index.html` | Standard SOC | Production-ready, balanced layout for monitors. |
| `dashboard.js` | Core Engine | Handles all API communication, state management, and DOM updates. |

## 2. API Interaction Map
The following table maps the frontend UI components to the backend endpoints they consume.

| Endpoint (v1) | Method | Frontend Module | Description |
| :--- | :--- | :--- | :--- |
| `/analyze` | POST | Live Scanner | Processes input messages; triggers AI Persona & Scam Detection. |
| `/stats` | GET | KPI Ribbon | Updates Interceptions, Active Agents, and Savings metrics. |
| `/telemetry` | GET | Threat Map | Provides IP/Geo data for the world map and cluster visualization. |
| `/telemetry/beacon` | POST | Forensic Lab | Transmits silent browser fingerprints and hardware hashes. |
| `/logs` | GET | Terminal UI | Streams real-time server activity logs to the dashboard terminal. |
| `/health/agents` | GET | System Pulse | Drives the 3D-effect heartbeat and agent status indicators. |
| `/threat-campaigns` | GET | Campaign Cluster | Populates the high-priority threat clusters (e.g., #APAC-01). |
| `/internals/trace` | GET | Neuro Trace | Visualizes the AI's OODA Loop decision-making path. |
| `/dossier/{id}` | GET | Forensic Lab | Generates the downloadable Markdown report for Law Enforcement. |
| `/mitre/matrix` | GET | Mitre Tab | Maps detections to the formal MITRE ATT&CK Framework. |

## 3. Data Flow Security
- **Auth**: All API calls use a modern JWT or API Key header (`x-api-key`) configured for GUVI platform compliance.
- **Latency**: Core endpoints are optimized for sub-150ms response times.
- **Resilience**: The frontend implements a retry-with-exponential-backoff strategy for the `/analyze` endpoint.

---

# 🌌 Sentinel Master API & Secret Catalog (v2.5.0)

## 🔐 1. External Secrets & Integrations
These must be set as Environment Variables.

| Secret Key | Purpose | Why it's Critical |
| :--- | :--- | :--- |
| `GROQ_API_KEY` | AI Brain / Inference | Powers sub-second scam detection & persona switching. |
| `HF_TOKEN` | Deployment / Models | Required for container hosting and accessing Llama-Guard. |
| `GUVI_API_KEY` | Compliance Auth | Authenticates the hackathon evaluation system. |
| `OPENROUTER_API_KEY` | AI Redundancy | Fallback if Groq limits are reached. |
| `DATABASE_URL` | Intelligence Storage | Stores forensics, chat logs, and fingerprint data. |

## 🛠️ 2. Core Functional APIs (`/api/v1`)
The engine of the system.

| Method | Endpoint | Use Case |
| :--- | :--- | :--- |
| `POST` | `/analyze` | Scam Message Parsing & Detection. |
| `GET` | `/stats` | Global Metrics (Interceptions, Funds Saved). |
| `GET` | `/telemetry` | Forensic Gathering (Browser/Device data). |
| `POST` | `/telemetry/beacon` | Silent Trapping (Passive fingerprinting). |
| `GET` | `/health/agents` | System Heartbeat (Agent Status). |
| `GET` | `/dossier/{id}` | Criminal Profile Generation. |
| `GET` | `/threat-graph` | Network Mapping. |
| `GET` | `/audit-logs` | Compliance / SOC Logging. |
| `GET` | `/campaigns` | Threat Clustering. |

## ⚖️ 3. Enforcement & Law APIs (`/api/v1/enforcement`)

| Method | Endpoint | Use Case |
| :--- | :--- | :--- |
| `POST` | `/report` | Automated Filing for Cybercrime portals. |
| `POST` | `/recommend-upi-action` | Financial Freeze requests. |
| `GET` | `/protection-advice` | Victim Safety instructions. |
| `GET` | `/reports` | Database of Legal Actions. |

## 🎓 4. Compliance & Hackathon APIs (`/api/guvi`)

| Method | Endpoint | Use Case |
| :--- | :--- | :--- |
| `POST` | `/analyze` | Platform Sync (GUVI Evaluation Server). |

## 🎭 5. Decoy & Trap APIs (`/decoys`)

| Method | Endpoint | Use Case |
| :--- | :--- | :--- |
| `GET` | `/upi/pay` | Fake Payment Gateway. |
| `GET` | `/bank/kyc-portal` | Fake KYC Portal. |
| `GET` | `/secure/otp-generate` | Fake OTP Logic. |
| `GET` | `/bank/error` | Server Stalling. |

## 📊 6. Professional Reports & Exports

| Method | Endpoint | Recipient |
| :--- | :--- | :--- |
| `GET` | `/stakeholder-export` | CERT-In / NPCI (STIX 2.1). |
| `GET` | `/audit-logs/export` | Enterprise SIEM (JSONL). |
