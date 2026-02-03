---
title: Sentinel Scam Honeypot
emoji: 🛡️
colorFrom: red
colorTo: purple
sdk: docker
sdk_version: "1.0"
app_file: app/main.py
pinned: true
license: mit
tags:
  - cybersecurity
  - scam-detection
  - honeypot
  - threat-intelligence
  - mitre-attack
  - india
  - telegram-bot
  - groq
  - llm
---

<div align="center">

# 🛡️ SENTINEL
### Autonomous Scam Intelligence & Engagement Platform

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.11+](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK%20Mobile-red.svg)](https://attack.mitre.org/matrices/mobile/)
[![Groq Powered](https://img.shields.io/badge/Powered%20by-Groq-orange.svg)](https://groq.com/)

**Enterprise-Grade Threat Intelligence Platform for Indian Cyber Fraud Detection**

[Demo](#demo) • [Architecture](#architecture) • [API](#api-reference) • [Deploy](#deployment)

</div>

---

## 🎯 Executive Summary

Sentinel is an **autonomous AI honeypot system** designed to intercept, engage, and extract intelligence from scammers targeting Indian citizens. Unlike passive blocklists, Sentinel actively wastes scammer time while building actionable threat intelligence packages for law enforcement.

### Key Capabilities

| Capability | Description |
|------------|-------------|
| **Real-Time Scam Detection** | ML-powered classification across 15+ Indian scam types (KYC, UPI, OTP, APK) |
| **Adaptive Engagement** | Dynamic persona synthesis that keeps scammers engaged for 50+ messages |
| **Intelligence Extraction** | Automated extraction of UPI IDs, bank accounts, phone numbers, Aadhaar, PAN |
| **MITRE ATT&CK Mapping** | Full Mobile ATT&CK matrix integration (T1660, T1417, T1636) |
| **Stakeholder Exports** | CERT-In, TRAI, NPCI, NCRP-compatible intelligence packages |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    SENTINEL PLATFORM                             │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │   Telegram  │  │   GUVI API  │  │  REST API   │   INGEST     │
│  │   Webhook   │  │   Webhook   │  │  Endpoints  │              │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘              │
│         └────────────────┼────────────────┘                      │
│                          ▼                                       │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                  ORCHESTRATOR                              │  │
│  │  • Session Management  • Rate Limiting  • Audit Logging   │  │
│  └───────────────────────────────────────────────────────────┘  │
│                          │                                       │
│         ┌────────────────┼────────────────┐                      │
│         ▼                ▼                ▼                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │   SCAM      │  │  INTEL      │  │  RESPONSE   │   AGENTS    │
│  │  DETECTOR   │  │  EXTRACTOR  │  │  GENERATOR  │              │
│  └─────────────┘  └─────────────┘  └─────────────┘              │
│         │                │                │                      │
│         └────────────────┼────────────────┘                      │
│                          ▼                                       │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              INTELLIGENCE ENGINE                           │  │
│  │  • Risk Scoring  • Campaign Clustering  • MITRE Mapping   │  │
│  │  • Scammer Profiling  • XAI Explanations                  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                          │                                       │
│                          ▼                                       │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              ENFORCEMENT LAYER                             │  │
│  │  • CERT-In Export  • TRAI Complaints  • NPCI Alerts       │  │
│  │  • NCRP Reports  • Forensic Dossiers                      │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔐 Security & Compliance

| Standard | Implementation |
|----------|----------------|
| **MITRE ATT&CK Mobile** | Full TTP mapping using verified technique IDs |
| **STIX 2.1-lite** | CERT-In compatible threat intelligence format |
| **PII Protection** | Aadhaar/PAN masking before LLM processing |
| **Audit Logging** | SIEM-compatible JSONL format with signatures |
| **Rate Limiting** | Per-IP and per-session throttling |

---

## 📊 Supported Scam Types

```python
SCAM_TYPES = [
    "banking_scam",      # KYC/Account freeze pretexts
    "lottery_scam",      # Fake lottery winnings
    "job_scam",          # Employment fraud
    "investment_scam",   # Ponzi/Crypto schemes
    "government_scam",   # Aadhaar/PAN/Police impersonation
    "delivery_scam",     # Fake courier/customs
    "tech_support_scam", # Remote access trojans
    "loan_scam",         # Instant loan fraud
    "sextortion_scam",   # Blackmail attempts
    "romance_scam",      # Emotional manipulation
    "apk_scam",          # Malicious app distribution
    "crypto_scam",       # Wallet/exchange fraud
    "sim_swap_scam",     # OTP interception
    "electricity_scam",  # Utility bill fraud
    "insurance_scam",    # Policy fraud
]
```

---

## 🚀 Deployment

### Local Development

```bash
# 1. Clone repository
git clone https://github.com/AvinashAnalytics/sentinel-scam-honeypot.git
cd sentinel-scam-honeypot

# 2. Setup environment
cp .env.example .env
# Edit .env with your API keys

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run server
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `GROQ_API_KEY` | ✅ | Groq API key (comma-separated for rotation) |
| `GUVI_API_KEY` | ✅ | GUVI hackathon callback key |
| `TELEGRAM_BOT_TOKEN` | ❌ | Telegram bot integration |
| `SANDBOX_MODE` | ❌ | Enable safe testing mode |

### Hugging Face Spaces

```bash
# Deploy to HF Spaces
git push hf main
```

---

## 📡 API Reference

### Core Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/webhook` | GUVI-compliant message webhook |
| `POST` | `/api/v1/analyze` | Standalone scam analysis |
| `GET` | `/api/v1/session/{id}` | Session intelligence dump |
| `GET` | `/api/v1/campaigns` | Active campaign tracker |
| `GET` | `/health` | System health check |

### Example Request

```bash
curl -X POST "http://localhost:8000/webhook" \
  -H "Content-Type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  -d '{
    "sessionId": "test-001",
    "message": "Dear customer, your SBI account will be blocked. Share OTP to verify.",
    "messageType": "text"
  }'
```

---

## 📈 Performance

| Metric | Value |
|--------|-------|
| Detection Latency (Heuristic) | < 50ms |
| Detection Latency (LLM) | < 1.5s |
| Max Engagement Depth | 100+ messages |
| Concurrent Sessions | 500+ |
| Uptime Target | 99.9% |

---

## 🔬 Research Foundation

This system implements techniques from peer-reviewed research:

- **LLMHoney** (arXiv:2509.01463) - Dynamic response generation
- **VelLMes** (arXiv:2510.06975) - High-interaction honeypot framework
- **MITRE ATT&CK Mobile** - Adversary TTP classification

---

## 👤 Author

**Avinash Rai**  
Data Engineer & Cybersecurity Researcher

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/avinashrai)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/AvinashAnalytics)

---

## 📄 License

MIT License - See [LICENSE](LICENSE) for details.

---

<div align="center">

**Built for GUVI Hackathon 2026** 🇮🇳

*"Protecting citizens by turning scammer tactics against them."*

</div>
