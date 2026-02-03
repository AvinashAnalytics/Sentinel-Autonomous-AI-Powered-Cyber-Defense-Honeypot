# Cost & Quota Optimization Strategy (Sentinel SOC)

## Overview
This document outlines the multi-layered strategy used by Sentinel Honeypot to maintain a high-intelligence "Enterprise" experience while operating strictly within **Groq's Free Tier** rate limits and token quotas.

## 1. Adaptive TPM-Aware Switchboard
The core of our optimization is the **SOC Switchboard**, which predicts the token cost of a message before it is sent.

*   **Proactive Throttle**: If a conversation history grows too large for the context-heavy `Llama 3.3 70B` (12K TPM limit), the system automatically proactively pivots to `Llama 4 Scout` (**30,000 TPM**).
*   **Context Championing**: When a conversation spans many turns, we switch to `Moonshot Kimi K2` with a **262K context window**. This prevents "Conversation Drift" and ensures a single rate-limited model doesn't stop the engagement.

## 2. Mandatory Prompt Caching
Groq's **Prompt Caching** is our "secret weapon." Cached tokens do not count towards TPM/RPD limits.

*   **Identical Prefix Injection**: We structure our prompts so the massive "Scam Taxonomy" and "Persona Instructions" are sent as a static prefix.
*   **Cache Hit Monitoring**: The `GroqClient` monitors logs for "Cache Hit" events. A single cache hit can save **~3,000 tokens** per turn, effectively doubling our effective rate limit.

### 2.1 Optimal Prompt Structure (Prefix Stability)
We have re-engineered `app/core/prompts.py` to follow the "Static-First" rule:
1.  **Fixed Role & Taxonomy**: Always at the top (Shared across all scanners).
2.  **JSON Schemas & Examples**: Placed before dynamic input (Shared across all turns).
3.  **Conversation Memory**: Placed at the very bottom.
   
This ensures that even when the conversation history grows (dynamic), the **"Brain Logic"** (Static) is always hit from the cache, maintaining consistent memory and 50% lower costs.

## 3. Distributed Key Rotation (Key Pool)
We implement a "Distributed Key Pool" to overcome the RPD (Requests Per Day) limit of a single account.

*   **Cooldown Intelligence**: When a key hits a `429 Too Many Requests` error, the system reads the `retry-after` header and puts that specific key on a precision cooldown timer. 
*   **Automatic Handover**: The orchestrator seamlessly switches to the next available key in the pool without the scammer or user noticing a delay.

## 4. Compound AI Architecture (Server-Side Tools)

Groq's **Compound** systems allow Sentinel to execute tools (Search, Browser, Code) on Groq's infrastructure, reducing the number of round-trips from the Sentinel server.

### 4.1 Compound vs. Compound-Mini
- **`groq/compound`**: Supports **Parallel Tool Calls**. Best for complex deep-dives where searching multiple sources or running multiple code blocks is required within a single turn.
- **`groq/compound-mini`**: Supports **Single Tool Call**. Optimized for **3x lower latency**. Use this for rapid lookups (e.g., verifying a single UPI ID or looking up the current exchange rate).

### 4.2 Dynamic Tool Throttling
To stay within the free tier limits (70,000 TPM limit on Compound):
1. **Prefer `compound-mini`** for first-turn triage.
2. **Enable specific tools** via `compound_custom.tools.enabled_tools` to prevent the model from wasting tokens on unnecessary browser automation when a simple `web_search` suffices.
3. **Use 'latest' versioning** to access hyper-optimized Feb 2026 server-side tools.

## 5. Zero-System Architecture (System-to-User Merge)
To optimize for Reasoning models (like `GPT-OSS`), which are sensitive to multi-turn complexity:
*   We "flatten" instructions into the message stream where possible.
*   We use **Native Hardware Reasoning** (`reasoning_effort: high`) instead of manual Chain-of-Thought prompts, saving **~500 tokens of "thinking text"** per turn by letting the model reason internally.

## 6. Metrics & Telemetry
The `RateLimitMonitor` provides live feedback:
*   `x-ratelimit-remaining-tokens`: Real-time TPM tracking.
*   `SOC ALERT`: Automatic system logging when we drop below 10% of our minute quota, triggering an immediate switch to a "Fast/Light" model to preserve the session.

---
**Status**: ACTIVE
**Primary Objective**: 100% Uptime for GUVI Hackathon Demo.
**Alignment**: Aligned with official Groq February 2026 Free Tier Documentation.
**Philosophy**: Zero-Cost Forensic Enterprise Grade Performance.
