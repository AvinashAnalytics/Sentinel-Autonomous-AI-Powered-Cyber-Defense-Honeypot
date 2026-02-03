# 🎓 GUVI Submission Checklist

This document provides a final verification list to ensure all components required for the **Agentic Honey-Pot for Scam Detection & Intelligence Extraction** challenge are present and operational.

## 1. Documentation Checklist
Verify that the following files are in your `docs/` folder:
- [ ] **[ARCHITECTURE.md](ARCHITECTURE.md)**: High-level system design.
- [ ] **[API_SPEC.md](API_SPEC.md)**: GUVI-compliant endpoint details.
- [ ] **[GUVI_COMPLIANCE_REPORT.md](GUVI_COMPLIANCE_REPORT.md)**: Requirement traceability matrix.
- [ ] **[TECHNICAL_WHITEPAPER.md](TECHNICAL_WHITEPAPER.md)**: Methodology and innovations.
- [ ] **[SECURITY_ETHICS.md](SECURITY_ETHICS.md)**: Privacy and safety guardrails.
- [ ] **[DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)**: Step-by-step setup instructions.
- [ ] **[LIMITATIONS_AND_FUTURE_WORK.md](LIMITATIONS_AND_FUTURE_WORK.md)**: Real vs. Simulated disclosure.

## 2. Technical Readiness Checklist
- [ ] **REST API**: Endpoint `POST /api/guvi/analyze` is active on port 8003.
- [ ] **Auth**: `x-api-key` header is mandatory and validated.
- [ ] **Intelligence**: The extraction logic captures exactly the 5 keys: `bankAccounts`, `upiIds`, `phishingLinks`, `phoneNumbers`, `suspiciousKeywords`.
- [ ] **Callback**: The `FINAL_CALLBACK_URL` is set to the official GUVI endpoint.
- [ ] **Multi-turn**: Memory is active and tracks `processId` across turns.

## 3. Deployment Checklist
- [ ] `.env` file contains valid `GROQ_API_KEY` and `GUVI_API_KEY`.
- [ ] `requirements.txt` includes all necessary dependencies.
- [ ] `main.py` or `run_server_8003_v2.py` starts without errors.

## 4. Final Verification Command
Run the following to verify compliance statically:
```bash
python tests/test_guvi_compliance.py
```
If this passes, your submission is technically ready.
