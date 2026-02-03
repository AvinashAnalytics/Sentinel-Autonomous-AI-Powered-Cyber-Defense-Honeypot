# Limitations & Real vs. Simulated Disclosures

## 1. Radical Transparency Statement
In adherence to the GUVI Hackathon rules and professional engineering ethics, this section clearly demarcates implemented features from simulated components intended for demonstration of architectural viability.

## 2. Implemented (Real) Components
These features are fully functional and verifiable in the provided codebase:
*   **Multi-Agent Orchestration**: Real autonomous OODA loop logic in `orchestrator.py`.
*   **Hybrid Intent Detection**: LLM + Heuristic scam classification engine.
*   **Intelligence Extraction**: Real PII/Financial harvesting logic with 10+ regex/ML extractors.
*   **GUVI Gateway**: Fully operational `/api/guvi/analyze` endpoint.
*   **Mandatory Callback**: Real HTTP client reporting to the GUVI evaluation platform.
*   **Stakeholder Exporting**: Real generation of CERT-In (STIX), TRAI, and NPCI formatted reports.
*   **Management Dashboard**: Fully interactive JS/HTML dashboard with live telemetry.

## 3. Simulated Components (Proof-of-Concept)
These components are implemented as "Shadow Interfaces" to demonstrate how Sentinel would integrate into a national SOC, but do not connect to external production government systems:
*   **Cyber Police API**: Simulated response logic (`NCRP-XXXX` tracking numbers).
*   **Bank/UPI Freeze API**: Logic for requesting NPCI/RBI freezes is implemented, but the actual "Bank API Response" is simulated for demo safety.
*   **Global Threat Feeds**: The dashboard shows a blend of real session data and simulated OSINT feeds (Abuse.ch, Blocklist.de) to visualize a data-rich SOC environment.

## 4. Known Technical Limitations
*   **Database Scaling**: The current version uses an in-memory `MemoryDB`. While high-performance for a hackathon, an enterprise deployment would require Redis/PostgreSQL.
*   **Language Support**: Primary optimization is for English, Hindi, and Hinglish. Support for regional Dravidian or Eastern languages is currently via model translation and lacks native heuristic optimization.
*   **Network Latency**: Response times are heavily dependent on Groq/OpenAI API availability.

## 5. Future Work & Roadmap
*   **V3.0 Vision**: Move from "Digital Deception" to "Autonomous Triage" by integrating with real-world bank anti-fraud APIs.
*   **Native NLP Models**: Fine-tuning a smaller local model (Mistral-7B) on the Indian Scam Corpus (ISC) to remove reliance on high-cost cloud APIs.
*   **STIX/TAXII Server**: Implementing a full TAXII 2.1 server for bidirectional threat intelligence sharing with law enforcement.
