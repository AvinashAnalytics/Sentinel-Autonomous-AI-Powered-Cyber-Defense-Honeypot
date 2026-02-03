# 📂 Complete Codebase File Reference

> **Every File, Every Folder, Every Usage Example**

---

## 📁 Directory Structure

```
sentinel-scam-honeypot/
├── app/                          # Main application
│   ├── agents/                   # AI agent modules
│   ├── api/                      # REST endpoints
│   ├── core/                     # LLM & infrastructure
│   ├── database/                 # Storage layer
│   ├── decoys/                   # Honeypot traps
│   ├── enforcement/              # Reporting modules
│   ├── intelligence/             # Analysis engines
│   ├── middleware/               # Request filters
│   ├── utils/                    # Helper functions
│   ├── config.py                 # Settings
│   └── main.py                   # FastAPI app
├── scripts/                      # Test & audit scripts
├── docs/                         # Documentation
└── audit/                        # Audit reports
```

---

## 🤖 `app/agents/` - AI Agent Modules

### `orchestrator.py`
**Purpose:** Master coordinator - routes messages through detection → extraction → response pipeline.

**When Called:**
- Every incoming webhook request
- Entry point for all scam conversations

**Example Flow:**
```python
# routes.py calls:
result = await orchestrator.process_message(
    session_id="scam-001",
    message="Your account blocked, send OTP"
)
# Orchestrator then calls: scam_detector → intel_extractor → persona_engine
```

---

### `scam_detector.py`
**Purpose:** Classifies if message is scam, identifies scam type with confidence.

**When Called:**
- Step 1 of orchestrator pipeline (parallel with intel_extractor)

**LLM Role:** `SMART_REASONING` (kimi-k2-instruct)

**Input → Output:**
```python
# Input
message = "Congratulations! You won Rs. 50 lakhs lottery!"

# Output
{
    "is_scam": True,
    "scam_type": "lottery_scam",
    "confidence": 0.94,
    "reasoning": "Classic lottery scam pattern with unsolicited prize claim"
}
```

---

### `intelligence_extractor.py`
**Purpose:** Extracts entities (UPI, phones, banks, crypto) from messages.

**When Called:**
- Step 1 of orchestrator pipeline (parallel with scam_detector)

**LLM Role:** `STRUCTURED_OUTPUT` (gpt-oss-20b)

**Input → Output:**
```python
# Input
message = "Send payment to fraud@ybl. Call 9876543210 for prize."

# Output
{
    "upi_ids": ["fraud@ybl"],
    "phone_numbers": ["+919876543210"],
    "bank_mentions": [],
    "urgency_indicators": ["prize"]
}
```

---

### `persona_engine.py`
**Purpose:** Generates and maintains victim persona for scammer engagement.

**When Called:**
- Step 3 of orchestrator (after detection complete)
- Every reply generation

**LLM Role:** `FAST_CHAT` (llama-3.1-8b-instant)

**Key Features:**
- Persona LOCKED to session on first message
- High temperature (0.85) for human-like replies
- Short responses (max 50 tokens)

**Example:**
```python
# First message: Generate persona
persona = persona_engine.get_or_create_persona("session-001")
# Returns: {"name": "Kamla Aunty", "age": 67, "vulnerability": "bank_anxiety"}

# All subsequent messages: Same persona
reply = await persona_engine.generate_reply(
    scam_type="banking_scam",
    message="Your account frozen!"
)
# Returns: "Oh no beta! What happened to my account? I don't understand..."
```

---

### `adaptive_strategy.py`
**Purpose:** Adjusts engagement tactics based on scammer behavior.

**When Called:**
- After 3+ messages in session
- When scammer shows impatience

**Strategies:**
| Scammer Behavior | Response Style |
|------------------|----------------|
| Aggressive | More confused, delay |
| Patient | Slowly reveal "OTP" |
| Suspicious | More questions |

---

### `conversation_manager.py`
**Purpose:** Manages conversation state, turn tracking, history.

**When Called:**
- Session creation/lookup
- History append after each message

---

## 🌐 `app/api/` - REST Endpoints

### `routes.py`
**Purpose:** Main FastAPI routes - webhook, health, debug.

**Endpoints:**
| Endpoint | Method | Purpose | When Called |
|----------|--------|---------|-------------|
| `/webhook` | POST | GUVI/Telegram messages | Every scammer message |
| `/health` | GET | Liveness check | K8s probes |
| `/api/v1/analyze` | POST | Direct analysis | Testing |
| `/api/v1/session/{id}` | GET | Get session state | Debug |

**Example Webhook:**
```python
@router.post("/webhook")
async def webhook(request: WebhookRequest):
    result = await orchestrator.process_message(
        session_id=request.sessionId,
        message=request.message.text
    )
    return {"reply": result.reply, "scamDetected": result.detected}
```

---

### `schemas.py`
**Purpose:** Pydantic models for request/response validation.

**Key Models:**
```python
class WebhookRequest(BaseModel):
    sessionId: str
    message: MessagePayload
    
class AnalysisResponse(BaseModel):
    scamDetected: bool
    scamType: Optional[str]
    confidence: float
    reply: str
```

---

### `feeds.py`
**Purpose:** Threat intelligence feed endpoints.

**When Called:**
- External SIEM systems polling
- Dashboard data fetch

---

### `debug_routes.py`
**Purpose:** Debug/admin endpoints (disabled in production).

**Endpoints:**
- `/debug/sessions` - List all active sessions
- `/debug/clear` - Clear memory
- `/debug/stats` - System metrics

---

## ⚙️ `app/core/` - Infrastructure Layer

### `llm_client.py`
**Purpose:** Unified LLM abstraction - Groq, Anthropic, OpenAI.

**When Called:**
- EVERY LLM request from ANY agent

**Key Methods:**
```python
# Main generation
response = await groq_client.generate(
    prompt="Analyze this scam...",
    role="SMART_REASONING",
    model="kimi-k2-instruct",
    json_mode=True
)

# Key rotation
self._rotate_key(retry_after=30)  # Called on 429

# Model fallback
self._get_fallback_model("kimi-k2")  # Returns "llama-3.1-8b-instant"
```

---

### `model_registry.py`
**Purpose:** Model capability database - what each model can do.

**When Called:**
- Before LLM call to check capabilities
- Fallback chain construction

**Example:**
```python
# Check if model supports JSON mode
if model_registry.supports("gpt-oss-20b", Capability.JSON_OBJECT):
    payload["response_format"] = {"type": "json_object"}

# Get fallback chain
chain = model_registry.get_fallback_chain("groq", "SMART_REASONING")
# Returns: ["kimi-k2-instruct", "llama-3.3-70b", "llama-3.1-8b-instant"]
```

---

### `groq_errors.py`
**Purpose:** Groq-specific error taxonomy and recovery actions.

**When Called:**
- On any non-200 Groq response
- Rate limit header parsing

**Example:**
```python
error_type = classify_groq_error(429)  # Returns GroqErrorType.RATE_LIMITED
recovery = get_recovery_action(error_type, "kimi-k2")
# Returns: {"action": "fallback", "delay_seconds": 30}
```

---

### `memory.py`
**Purpose:** Session state storage - conversation history, persona, intel.

**When Called:**
- Session lookup on every request
- History append after each turn

**Example:**
```python
session = memory.get_or_create("session-001")
session.add_message("user", "Your account blocked!")
session.add_message("assistant", "Oh no! What do I do?")
session.update_intel({"upi_ids": ["scam@ybl"]})
```

---

### `context.py`
**Purpose:** Request-scoped context (budget tracking, metadata).

**When Called:**
- Created per request
- Passed through pipeline

---

### `static_prompts.py`
**Purpose:** Cache-optimized system prompts (1024+ tokens).

**When Called:**
- Loaded once at startup
- Passed to every LLM call

**Why Important:**
- Groq caches first 1024 tokens
- Static prompts = maximum cache hits
- 90% token savings on repeated calls

---

### `prompts.py`
**Purpose:** Dynamic prompt templates.

**When Called:**
- Prompt construction before LLM call

---

### `personas.py`
**Purpose:** Persona templates and traits.

**When Called:**
- Persona generation for new sessions

---

### `engagement_delay.py`
**Purpose:** Realistic typing delays to seem human.

**When Called:**
- Before sending reply to scammer

---

## 💾 `app/database/` - Storage Layer

### `db.py`
**Purpose:** Database connection (SQLite/PostgreSQL).

**When Called:**
- Startup initialization
- Persistence operations

---

### `memory_db.py`
**Purpose:** In-memory session storage (Redis-like).

**When Called:**
- Fast session lookup
- Hackathon demo mode

---

### `models.py`
**Purpose:** SQLAlchemy/Pydantic models.

---

## 🪤 `app/decoys/` - Honeypot Traps

### `fake_endpoints.py`
**Purpose:** Fake banking/payment pages to trap scammers.

**Endpoints:**
| Endpoint | Looks Like | Captures |
|----------|------------|----------|
| `/api/bank/verify` | Bank OTP page | Scammer OTP attempts |
| `/api/upi/pay` | UPI payment | Payment attempts |

---

### `victim_profiles.py`
**Purpose:** Rich victim persona definitions.

---

## 🚨 `app/enforcement/` - Reporting

### `police_api.py`
**Purpose:** NCRP (Cyber Crime) report generation.

**When Called:**
- High-confidence scam detected
- User requests report

**Output:**
```python
report = police_api.generate_report(session)
# Returns:
{
    "complaint_type": "CYBER_FRAUD",
    "category": "UPI_FRAUD",
    "evidence": [...],
    "recommended_action": "Expedited review requested"
}
```

---

### `stakeholder_exports.py`
**Purpose:** Multi-agency export formats.

**Exports:**
| Agency | Format | When Used |
|--------|--------|-----------|
| CERT-In | STIX-lite JSON | Threat sharing |
| TRAI | CSV | Phone blocklist |
| NPCI | JSON | UPI fraud report |
| NCRP | Structured | Police complaint |

---

### `awareness.py`
**Purpose:** Victim safety messages.

**When Called:**
- After scam detected
- Before ending session

---

## 🧠 `app/intelligence/` - Analysis Engines

### `threat_engine.py`
**Purpose:** Master threat intel generator - IOCs, TTPs, campaigns.

**When Called:**
- After successful detection
- Intel compilation

**Output:**
```python
intel = threat_engine.analyze(session)
# Returns:
{
    "iocs": {"upi": ["scam@ybl"], "phones": [...]},
    "ttps": [{"id": "T1660", "name": "Phishing"}],
    "campaigns": ["banking_fraud_wave_jan24"],
    "risk_score": 87
}
```

---

### `mitre_mapper.py`
**Purpose:** Maps scam patterns to MITRE ATT&CK Mobile techniques.

**When Called:**
- Threat intel generation
- Report formatting

**Mappings:**
| Scam Type | MITRE ID | Technique |
|-----------|----------|-----------|
| Banking | T1660 | Phishing |
| SMS | T1636.004 | SMS Messages |
| Credential | T1417 | Input Capture |

---

### `emotional_analyzer.py`
**Purpose:** Detects manipulation tactics (urgency, fear, greed).

**When Called:**
- Parallel with scam detection
- Risk score contribution

**Output:**
```python
analysis = emotional_analyzer.analyze("URGENT! Account blocked in 24 hours!")
# Returns:
{
    "urgency_score": 0.92,
    "fear_score": 0.85,
    "manipulation_tactics": ["deadline_pressure", "authority_impersonation"]
}
```

---

### `risk_scorer.py`
**Purpose:** Weighted risk calculation (0-100).

**When Called:**
- After all analysis complete
- Final risk determination

**Formula:**
```python
risk = (
    scam_confidence * 0.35 +
    urgency_score * 0.20 +
    entity_severity * 0.25 +
    emotional_manipulation * 0.20
)
```

---

### `scammer_profiler.py`
**Purpose:** Adversary behavioral profiling.

**When Called:**
- Multi-turn sessions
- Pattern accumulation

---

### `xai_reasoning.py`
**Purpose:** Explainable AI - human-readable detection reasons.

**When Called:**
- Report generation
- Debug/audit

---

### `campaign_tracker.py`
**Purpose:** Links related scams by shared entities.

---

### `honeytokens.py`
**Purpose:** Generates traceable fake credentials.

---

### `threat_feeds.py`
**Purpose:** External threat feed integration.

---

### `enrichment_service.py`
**Purpose:** Entity enrichment (phone/UPI validation).

---

### `telemetry.py`
**Purpose:** System metrics and monitoring.

---

### `engagement_metrics.py`
**Purpose:** Tracks engagement effectiveness.

---

### `graph_threat_intel.py`
**Purpose:** Graph-based threat correlation.

---

## 🔧 `app/utils/` - Helper Functions

### `extractors.py`
**Purpose:** Regex-based entity extraction.

**When Called:**
- Every message (before LLM)
- Zero-cost intel gathering

**Patterns:**
```python
PATTERNS = {
    "upi": r'[a-zA-Z0-9.\-_]{2,64}@(ybl|paytm|okaxis|...)',
    "phone": r'[+91]?\d{10,12}',
    "ifsc": r'[A-Z]{4}0[A-Z0-9]{6}',
    "crypto_btc": r'\b(1|3|bc1)[a-zA-Z0-9]{25,39}\b'
}
```

---

### `token_utils.py`
**Purpose:** Token estimation and smart truncation.

**When Called:**
- Before LLM call (budget check)
- 413 error recovery

```python
tokens = estimate_tokens("Hello world")  # ~3 tokens
truncated = smart_truncate(long_text, max_tokens=4000)
```

---

### `json_utils.py`
**Purpose:** Robust LLM JSON parsing.

**When Called:**
- After LLM response
- Handles markdown backticks, trailing commas

---

### `callback_client.py`
**Purpose:** GUVI API callback client.

**When Called:**
- Scam detected with high confidence
- Session complete

---

### `guvi_handler.py`
**Purpose:** GUVI request format translation.

---

### `dossier_generator.py`
**Purpose:** Markdown forensic report generation.

---

### `audit_logger.py`
**Purpose:** SIEM-compatible audit logging.

---

### `logger.py`
**Purpose:** Structured logging with PII masking.

---

## 🧪 `scripts/` - Test & Audit

| Script | Purpose | When to Run |
|--------|---------|-------------|
| `fast_behavior_tests.py` | Quick persona behavior tests | Before deployment |
| `behavioral_tests.py` | Full behavioral test suite | Full audit |
| `test_prompt_caching.py` | Verify Groq cache hits | After prompt changes |
| `verify_detector_soc.py` | Detector accuracy tests | After model changes |
| `verify_extractor_soc.py` | Extractor accuracy tests | After regex changes |
| `guvi_multi_turn_test.py` | GUVI format compliance | Before submission |
| `multi_turn_audit.py` | Session continuity tests | Edge case testing |
| `strict_submission_audit.py` | Full submission validation | Final check |
| `verify_concurrency.py` | Parallel execution tests | Performance audit |
| `verify_production_hardening.py` | Security & resilience | Production prep |
| `audit_behavioral_personas.py` | Persona consistency | Character testing |

---

## 🔗 Complete Call Chain Example

```
WEBHOOK ARRIVES: "Your SBI account blocked!"
        │
        ▼
routes.py::webhook()
        │
        ▼
orchestrator.py::process_message()
        │
        ├─[PARALLEL]──────────────────────────────────┐
        │                                              │
        ▼                                              ▼
scam_detector.py::analyze()           intelligence_extractor.py::extract()
        │                                              │
        ▼                                              ▼
llm_client.py::generate()             extractors.py::extract_all()
        │                                              │
        ▼                                              ▼
[GROQ API: kimi-k2]                   llm_client.py::generate()
        │                                              │
        ▼                                              ▼
{scam_type: "banking"}                [GROQ API: gpt-oss]
        │                                              │
        └──────────────────┬───────────────────────────┘
                           │
                           ▼
              risk_scorer.py::calculate()
                           │
                           ▼
              persona_engine.py::generate_reply()
                           │
                           ▼
              llm_client.py::generate()
                           │
                           ▼
              [GROQ API: llama-3.1-8b FAST_CHAT]
                           │
                           ▼
              "Oh no beta! What happened to my account?"
                           │
                           ▼
              threat_engine.py::analyze()
                           │
                           ▼
              callback_client.py::send_final_result()
                           │
                           ▼
              [GUVI API]
```

---

**Document Version:** 2026-02-03  
**Total Files Documented:** 60+ Python files
