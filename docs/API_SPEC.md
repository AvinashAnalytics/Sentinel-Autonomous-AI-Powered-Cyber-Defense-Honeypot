# 📡 Sentinel API Specification (V2.0)
**Protocol**: REST / HTTP | **Format**: JSON
**Base URL**: `http://localhost:8000/api`

> 📘 **Note**: This document defines the Contract. For implementation details, see [Technical Reference](TECHNICAL_REFERENCE.md).

---

## 1. Authentication
All endpoints (except Health Check) require the `x-api-key` header.
```http
x-api-key: <YOUR_SECRET_KEY>
```

---

## 2. Core Endpoints

### 🟢 `POST /guvi/analyze` (Primary Ingress)
The main entry point for the GUVI Challenge interaction.

#### Request Schema
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `processId` | string | ✅ | Unique session identifier. |
| `message` | string | ✅ | The scammer's text input. |
| `conversationHistory`| list | ❌ | List of previous `{"text": "...", "sender": "..."}` objects. |

**Example Payload:**
```json
{
  "processId": "sess_998877",
  "message": "Send 5000 rs to 9876543210@paytm immediately or police will come.",
  "conversationHistory": []
}
```

#### Response Schema
| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | string | "success" or "error" |
| `scamDetected` | boolean | True if threat > threshold |
| `scamConfidence` | float | 0.0 to 1.0 |
| `extractedIntelligence` | object | The 5-key intelligence object |
| `reply` | string | The autonomous agent's response |

**Example Response:**
```json
{
  "status": "success",
  "reply": "Oh no! Police? I am scared. Whose number is that?",
  "scamDetected": true,
  "scamConfidence": 0.95,
  "riskLevel": "CRITICAL",
  "extractedIntelligence": {
      "upiIds": ["9876543210@paytm"],
      "phoneNumbers": ["9876543210"],
      "bankAccounts": [],
      "phishingLinks": [],
      "suspiciousKeywords": ["[URGENCY] immediately", "[THREAT] police"]
  }
}
```

---

### 🔵 `POST /callback/guvi` (System Outbound)
**Trigger**: This is an *outbound* webhook sent BY Sentinel TO the GUVI platform when a high-risk scam is finalized.

#### Payload Schema
```json
{
  "sessionId": "sess_998877",
  "scamDetected": true,
  "riskLevel": "CRITICAL",
  "totalMessagesExchanged": 5,
  "extractedIntelligence": { ... }
}
```

---

## 3. Administrative Endpoints (Protected)

### 🟠 `POST /v1/admin/reset`
Resets the memory and state for a specific session.
- **Body**: `{"session_id": "sess_998877"}`

### 🟠 `GET /v1/admin/metrics`
Returns system telemetry (uptime, active_sessions, threats_blocked).

---

## 4. Error Codes

| Code | Meaning | Solution |
| :--- | :--- | :--- |
| `401` | Unauthorized | Check `x-api-key` header. |
| `422` | Validation Error | Ensure JSON body matches `GUVIInputRequest` schema. |
| `429` | Rate Limit Exceeded | Slow down (Limit: 60 req/min). |
| `500` | Internal Error | Check Docker logs for LLM failures. |
