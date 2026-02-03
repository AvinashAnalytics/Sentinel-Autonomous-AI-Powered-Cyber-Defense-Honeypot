# 📄 Sentinel Technical Whitepaper
**A Neuro-Symbolic Approach to Autonomous Scam Disruption**
**Date**: February 2026

> 📘 **Abstract**: This paper outlines the design of Sentinel, an autonomous honeypot system capable of engaging financial fraudsters in high-fidelity natural language conversations to extract forensic intelligence.

---

## 1. Introduction
Financial fraud in India (UPI scams, KYC fraud) operates at an industrial scale. Traditional blocking mechanisms are reactive. Sentinel proposes an **Active Defense** paradigm: wasting scammer time and resources through autonomous engagement.

---

## 2. Methodology: Neuro-Symbolic Architecture
Sentinel rejects the pure "LLM-only" approach, which is prone to hallucination and manipulation. Instead, it utilizes a **Neuro-Symbolic** design:

1.  **Symbolic Layer (The "Rule of Law")**:
    - **Regex Engines**: For detecting critical entities (UPI IDs, Phones) with 100% precision.
    - **State Machines**: Deterministic conversation phases (Intro -> Confusion -> Compliance -> Delay).

2.  **Neural Layer (The "Creative Brain")**:
    - **LLMs (Llama-3/GPT-4)**: Used rigidly for *semantic intent classification* and *persona-based text generation*.
    - **Safety Guards**: `Llama-Guard` filters preventing the bot from becoming toxic or agreeing to illegal solicitations.

---

## 3. The "Granulated Persona" Engine
To pass the Turing Test against human scammers, Sentinel relies on **Persona Granularity**. It doesn't just "act old"; it simulates:
- **Cognitive Load**: "Wait... let me find my glasses."
- **Technological Friction**: "The OTP is not coming. Should I click RESEND?"
- **Emotional Vulnerability**: "I am scared of police."
This granularity triggers the scammer's "Greed" and "Urgency" biases, prolonging the engagement.

---

## 4. Forensic Extraction Pipeline
Intelligence is harvested passively using a multi-stage pipeline:
1.  **Ingest**: Raw text is normalized.
2.  **Pattern Match**: High-precision regex extracts candidate entities.
3.  **Validation**:
    - **Luhn Algorithm**: Verifies Credit Card checksums.
    - **Verhoeff Algorithm**: Verifies Aadhaar numbers.
    - **PSP Check**: Validates UPI handles (e.g., `@okicici`).

---

## 5. Conclusion
Sentinel demonstrates that **Autonomous Agents** can effectively disrupt cybercrime operations not just by blocking, but by actively consuming the attacker’s most valuable resource: **Time**.
