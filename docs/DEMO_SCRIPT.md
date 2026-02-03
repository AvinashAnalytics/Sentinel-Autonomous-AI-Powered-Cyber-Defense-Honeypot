# 🎥 GUVI Demo Video Script (1 Minute)

**Goal:** Convince judges that this is a working, improved, and enterprise-ready honeypot system.

---

## 🎬 Scene 1: Introduction (0:00 - 0:10)
*   **Visual:** Show the `README.md` with the "Sentinel Scam Honeypot" banner and badges.
*   **Voiceover:** "Hi, this is Team [Team Name]. We've built Sentinel, an autonomous AI honeypot that traps scammers and extracts critical intelligence for Indian law enforcement."

## 🎬 Scene 2: The Attack (0:10 - 0:25)
*   **Visual:** Split screen. Left: Your API/Dashboard. Right: Swagger UI (`/docs`).
*   **Action:** Send a simulated scam message via Swagger UI (`/api/v1/analyze`).
    *   **Input:** `"Congratulations! You won 1 crore lottery! Send tax amount to verify at winner@paytm or call +91-9876543210"`
*   **Voiceover:** "Here, we simulate a typical lottery scam message attacking our citizen hotline."

## 🎬 Scene 3: The AI Response (0:25 - 0:40)
*   **Visual:** Show the JSON response in Swagger. Highlight:
    *   `scam_detected: true`
    *   `honeypot_response`: (e.g., "Wah! Sach mein?")
    *   `extracted_intelligence`: Phone number and UPI ID.
*   **Voiceover:** "Our hybrid AI instantly detects the scam, identifying it as a 'Lottery Fraud'. It generates a believable response using our 'Sharma Uncle' persona to hook the scammer, while automatically extracting their UPI ID and phone number."

## 🎬 Scene 4: The Command Center (0:40 - 0:55)
*   **Visual:** Switch to the **Sentinel Operational SOC Dashboard**. 
*   **Action:** 
    1. Scroll through the **Campaign Clusters** and **Intelligence Feeds**.
    2. Show the **Neural Logic Trace** pulsing.
    3. Demonstrate the **Admin Authorization** logic.
*   **Voiceover:** "Finally, we monitor everything from our Operational Dashboard. We're showing a production-ready vision here—integrating real-time logic tracing and forensic fingerprinting to provide definitive attribution for law enforcement, built to protect 1.4 billion citizens."

## 🎬 Scene 5: Conclusion (0:55 - 1:05)
*   **Visual:** Show the final footer with Developer Registry and "Avinash Analytics" branding.
*   **Voiceover:** "Sentinel is a nation-state grade defense system designed to scale for India's 1.4 billion citizens. We don't just block scams; we outsmart the scammers. Thank you."

---

## 🛠️ Validation Checklist for Video
1.  [ ] Are badges visible? (Shows polish)
2.  [ ] Is the JSON output readable? (Proofs it works)
3.  [ ] Is the Dashboard active? (Visual appeal)
4.  [ ] Did you mention "Citizen Safety"? (Emotional hook)
