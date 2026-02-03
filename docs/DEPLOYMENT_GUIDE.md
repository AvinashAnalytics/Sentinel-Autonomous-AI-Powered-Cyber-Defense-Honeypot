# 🚀 Sentinel Deployment Guide
**Operational Manual (v2.0)**

> 📘 **Note**: For code architecture, see [Technical Reference](TECHNICAL_REFERENCE.md).

---

## 1. Prerequisites
- **Docker**: v20.10+ (Recommended)
- **Python**: v3.9+ (If running locally)
- **API Keys**: Groq (Required), OpenAI (Optional Fallback).

---

## 2. Configuration (`.env`)
Create a `.env` file in the project root.

```ini
# --- Core AI ---
GROQ_API_KEY=gsk_...                # Primary Inference (LPU)
OPENAI_API_KEY=sk-...               # Fallback Inference

# --- Compliance ---
GUVI_API_KEY=my_secret_key          # Secures /api/guvi/analyze
CALLBACK_URL=https://hackathon.guvi.in/api/v1/callback

# --- Database ---
DATABASE_URL=sqlite:///./sentinel.db # Or postgresql://...

# --- Tuning ---
RATE_LIMIT_PER_MINUTE=60
LOG_LEVEL=INFO
```

---

## 3. Deployment Modes

### 🐳 Docker (Production)
The recommended way to run Sentinel.

```bash
# 1. Build Image
docker build -t sentinel-honeypot:latest .

# 2. Run Container (Background)
docker run -d \
  -p 8000:8000 \
  --env-file .env \
  --name sentinel \
  sentinel-honeypot:latest

# 3. View Logs
docker logs -f sentinel
```

### 💻 Local (Development)
For hacking on the code.

```bash
# 1. Install Dependencies
pip install -r requirements.txt

# 2. Run Server (Reload enabled)
uvicorn app.main:app --reload --port 8000
```

---

## 4. Verification

### Health Check
```bash
curl http://localhost:8000/health
# Output: {"status": "healthy", "version": "2.0.0"}
```

### Test Analysis
```bash
curl -X POST http://localhost:8000/api/guvi/analyze \
  -H "x-api-key: my_secret_key" \
  -H "Content-Type: application/json" \
  -d '{"processId": "test_1", "message": "Urgent money needed"}'
```

---

## 5. Troubleshooting

| Error | Cause | Fix |
| :--- | :--- | :--- |
| `401 Unauthorized` | Missing Header | Check `x-api-key` in request. |
| `500 Internal Error` | LLM Failure | Check `GROQ_API_KEY` validity. |
| `ConnectionRefused` | Docker Port | Ensure `-p 8000:8000` is mapped. |
