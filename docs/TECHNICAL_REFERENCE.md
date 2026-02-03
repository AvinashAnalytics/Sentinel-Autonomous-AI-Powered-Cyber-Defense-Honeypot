# 📘 Sentinel Honeypot: Technical Reference Manual (V2.0)
**The Complete Architectural Blueprint**
**Date**: 2026-02-01 | **Classification**: INTERNAL TECHNICAL

---

## 1. Executive System Overview
Sentinel is a **Neuro-Symbolic Autonomous Response System** designed for high-fidelity scam baiting, intelligence extraction, and forensic analysis.
- **Core Loop**: OODA (Observe-Orient-Decide-Act) implemented via `HoneypotOrchestrator`.
- **Latency Strategy**: Uses `FastAPI` (Async) + `Groq` (LPU Inference) to achieve <2s interaction times.
- **Compliance**: Strictly enforces the GUVI Hackathon 5-key intelligence schema.
- **Key Capability**: Real-time campaign clustering using forensic hashing of phone numbers and UPI IDs.

---

## 2. Repository Structure & Dependency Graph
```ascii
app/
├── agents/             (Logic Layer)
│   ├── orchestrator.py ----------> (Controls All Agents)
│   ├── scam_detector.py ---------> (Uses core/llm_client)
│   ├── persona_engine.py --------> (Uses core/personas)
│   ├── intelligence_extractor.py -> (Uses utils/extractors)
├── core/               (Infrastructure)
│   ├── llm_client.py ------------> (External API Gateway)
│   ├── model_registry.py --------> (Model Capability Map)
├── api/                (Interface)
│   ├── routes.py ----------------> (Entry Point)
├── intelligence/       (Analysis)
│   ├── threat_engine.py ---------> (Campaign Clustering)
│   ├── risk_scorer.py -----------> (Weighted Scoring)
├── utils/              (Helpers)
│   ├── guvi_handler.py ----------> (Compliance Filter)
```

---

## 3. Folder-by-Folder Deep Analysis

### 📂 3.1 `app/agents/` (The Reasoning Engine)
**Purpose**: Contains the autonomous "Brains" of the system.
**Architecture**: Stateful agents that maintain context across turns.

#### 📄 `orchestrator.py`
- **Role**: The Central Nervous System.
- **Logic**: Implements the OODA loop. Manages the lifecycle of a request from `routes.py` to `callback_client.py`.
- **Key Function**: `process_message(message, conversation_id)`
- **Dependencies**: Imports ALL other agents.
- **Example Flow**:
    - **Input**: `User msg: "Send tax pending"`
    - **Action**: Calls `scam_detector.detect()` -> `persona_engine.generate()` -> `intel_extractor.extract()`
    - **Output**: `{"reply": "How much tax?", "risk": "HIGH"}`

#### 📄 `scam_detector.py`
- **Role**: Threat Classifier.
- **Logic**: Hybrid (Regex Keywords + LLM Semantic).
- **Key Function**: `detect(message)`
- **Sample I/O**:
    - **Input**: `"Congratulations you won 1 Crore lottery"`
    - **Output**: 
      ```json
      {
        "type": "lottery_scam",
        "confidence": 0.98,
        "triggers": ["won", "crore", "lottery"]
      }
      ```

#### 📄 `persona_engine.py`
- **Role**: Deceptive Response Generator.
- **Logic**: Selects a persona (e.g., "Grandpa") and injects "human" errors (typos, confusion).
- **Key Function**: `generate_response(scam_type, message)`
- **Sample I/O**:
    - **Input**: `"Send 5000 rs now"`
    - **Output**: `"Beta I am trying... internet is slow. Which button to press?"` (Injects delay tactics)

#### 📄 `intelligence_extractor.py`
- **Role**: Forensic Data Harvester.
- **Logic**: Uses `extractors.py` (Regex) + LLM (Context) to find entities.
- **Key Function**: `extract(message)`
- **Sample I/O**:
    - **Input**: `"Pay to upi 9876543210@ybl"`
    - **Output**: 
      ```json
      {
        "upi_ids": ["9876543210@ybl"],
        "phone_numbers": ["9876543210"]
      }
      ```

---

### 📂 3.2 `app/core/` (Infrastructure Layer)
**Purpose**: Foundation for AI and Memory.

#### 📄 `llm_client.py`
- **Role**: Unified AI Gateway.
- **Logic**: Switches between Groq/OpenAI based on availability.
- **Key Function**: `generate(prompt, role)`
- **Internal Logic**: 
    1. Try `GROQ_API_KEY` (Llama-3).
    2. If error, switch to `OPENAI_API_KEY` (GPT-4o).
    3. Return string response.
- **Sample I/O (Internal)**:
    - **Input**: `Role.FAST_CHAT`, `Prompt: "Hi"`
    - **Output**: `"Hello!"` (from Groq/Llama-8b)

#### 📄 `model_registry.py`
- **Role**: Capability Mapper.
- **Logic**: Maps abstract roles (`SAFETY`, `REASONING`) to concrete Model IDs (`llama-guard-3`, `llama-3.3-70b`).
- **Example**: `Capability.FAST_CHAT` -> `llama-3.1-8b-instant`

#### 📄 `prompts.py`
- **Role**: Instruction Store.
- **Logic**: Contains Jinja2 templates for system prompts.
- **Content**: `SCAM_DETECTION_PROMPT`, `PERSONA_GENERATION_PROMPT`.

---

### 📂 3.3 `app/api/` (Ingress Layer)
**Purpose**: REST API Definition.

#### 📄 `routes.py`
- **Role**: HTTP Endpoint Definer.
- **Logic**: Binds `POST /api/guvi/analyze` to `guvi_handler.process_guvi_message`.
- **Security**: Validates `x-api-key` header.

#### 📄 `schemas.py`
- **Role**: Data Validation (Pydantic).
- **Logic**: Defines strict types for JSON inputs.
- **Example Schema**:
    ```python
    class GUVIInputRequest(BaseModel):
        processId: str
        message: str
        conversationHistory: List[Dict]
    ```

---

### 📂 3.4 `app/intelligence/` (Analysis Layer)
**Purpose**: Post-processing and Threat Logic.

#### 📄 `threat_engine.py` (⭐ Key Component)
- **Role**: Campaign Clusterer.
- **Logic**: Hashes (Phone + UPI) to create a `CampaignID`.
- **Sample I/O**:
    - **Input**: `UPI: scam@ybl`, `Phone: 9998887776`
    - **Output**: `CampaignID: CAMP_A1B2C3D4`

#### 📄 `risk_scorer.py`
- **Role**: Threat Assessment Engine.
- **Logic**: `Score = (Keywords * 0.3) + (Urgency * 0.25) + (Payment * 0.25)`.
- **Sample I/O**:
    - **Input**: `"URGENT pay TAX now"`
    - **Output**: `Score: 0.85 (CRITICAL)`

#### 📄 `mitre_mapper.py`
- **Role**: Standardized Classification.
- **Logic**: Maps internal scam types to MITRE ATT&CK Mobile Framework.
- **Example**: `apk_scam` -> `T1475 (Malicious App Delivery)`

---

### 📂 3.5 `app/utils/` (Utility Layer)
**Purpose**: Cross-cutting concerns.

#### 📄 `guvi_handler.py` (⭐ Compliance Core)
- **Role**: GUVI Spec Translator.
- **Logic**: Filters internal intel down to **Mandatory 5 Keys**.
- **Sample Transformation**:
    - **Internal**: `{"bank_accounts": ["123"], "rat_apps": ["AnyDesk"]}`
    - **GUVI Output**: 
      ```json
      {
        "bankAccounts": ["123"],
        "suspiciousKeywords": ["[RAT APP] AnyDesk"]
      }
      ```

#### 📄 `callback_client.py`
- **Role**: Async Reporter.
- **Logic**: Sends final JSON result to `hackathon.guvi.in`.
- **Mechanism**: Fire-and-forget async task with 3x retries.

#### 📄 `extractors.py`
- **Role**: Regex Library.
- **Logic**: Contains "Hard" patterns for UPI, Phones, PAN, Aadhaar.
- **Algorithms**: Implements `validate_luhn()` (Credit Cards) and `validate_verhoeff()` (Aadhaar).

---

### 📂 3.6 `app/enforcement/` (Reporting Layer)
**Purpose**: Law Enforcement Integration.

#### 📄 `stakeholder_exports.py`
- **Role**: Format Converter.
- **Logic**: Generates JSON for `CERT-In`, `TRAI`, `NCRP`.
- **Sample I/O**:
    - **Input**: Session Intel
    - **Output**: `STIX 2.1` Bundle (for CERT-In).

#### 📄 `police_api.py`
- **Role**: Simulated Policy Interface.
- **Logic**: **MOCK** Implementation (as per safety rules). Returns fake `NCRP_ID`.

---

## 4. System Execution Lifecycle (Trace)

1.  **Ingress (`routes.py`)**: 
    - User sends `POST /api/guvi/analyze`.
    - `x-api-key` validated.
    - `GUVIInputRequest` validated.

2.  **Orchestration (`orchestrator.py`)**:
    - `process_message()` called.
    - Check `ConversationManager` for history.

3.  **Analysis (`scam_detector.py`)**:
    - `"Hello friend"` -> **LOW CONFIDENCE**.
    - `"Pay tax"` -> **HIGH CONFIDENCE (Government Scam)**.

4.  **Strategy (`persona_engine.py`)**:
    - Selects `Persona: Elderly Citizen`.
    - Generates: `"I am confused... which form?"`

5.  **Forensics (`intelligence_extractor.py`)**:
    - Scans for UPI/Bank details.
    - If found, `risk_scorer.py` boosts score to 1.0.

6.  **Response (`guvi_handler.py`)**:
    - Formats output to `GUVIOutputResponse`.
    - Returns JSON to user.

7.  **Callback (`callback_client.py`)**:
    - If `risk > 0.9`, triggers async POST to GUVI URL.

---

## 5. Model Switching Strategy

| Trigger Scenario | Primary Model | Fallback Model | Why? |
| :--- | :--- | :--- | :--- |
| **General Chat** | `llama-3.1-8b-instant` | `gpt-3.5-turbo` | Speed (<0.5s) |
| **Deep Reasoning** | `llama-3.3-70b-versatile` | `gpt-4o` | Complex Scam Analysis |
| **Data Extraction** | `gpt-oss-20b` | `gpt-4-turbo` | Strict JSON Output |
| **Safety Filter** | `llama-guard-3` | `content-filter-alpha` | Ethical Compliance |

---

## 6. Real vs. Simulated Components (Disclosure)

| Component | Status | Implementation |
| :--- | :--- | :--- |
| **Scam Detection** | **REAL** | `app/agents/scam_detector.py` |
| **Intel Extraction** | **REAL** | `app/utils/extractors.py` |
| **GUVI Callback** | **REAL** | `app/utils/callback_client.py` |
| **Dashboard** | **REAL** | `web/dashboard.js` |
| **Police Reporting** | **SIMULATED** | `app/enforcement/police_api.py` (Mock Returns) |
| **Bank Freezing** | **SIMULATED** | `app/enforcement/awareness.py` (Mock Logic) |

---

## 7. Developer Guide: How to Extend

### Adding a New Scam Type
1.  Open `app/agents/scam_detector.py`.
2.  Add keywords to `SCAM_DATABASE`.
3.  Add regex pattern to `app/utils/extractors.py`.

### Adding a New Persona
1.  Open `app/core/personas.py`.
2.  Add a new dict entry (e.g., `ANGRY_INVESTOR`).
3.  Define `vocabulary` and `error_rate`.

### Adding a New Model
1.  Open `app/core/model_registry.py`.
2.  Define the Model ID.
3.  Register it in `Capability` enum.

---

## 8. Final System Verdict
The Sentinel architecture is a **Production-Grade Neuro-Symbolic State Machine**. It is not just "wrapping an LLM" - it is a full forensic pipeline that uses LLMs as sub-components within a larger rigid OODA loop. This ensures:
1.  **Safety**: Deterministic constraints on outputs.
2.  **Reliability**: Regex fallbacks when LLMs fail.
3.  **Compliance**: Exact JSON schemas for GUVI.
4.  **Forensics**: Real-time clustering of threat actors.

**Verdict**: READY FOR DEPLOYMENT.
