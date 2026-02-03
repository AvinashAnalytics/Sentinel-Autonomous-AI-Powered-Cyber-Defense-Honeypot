---
description: Explanation of key enterprise architectural components
---

# Enterprise Architecture Components

This document outlines the purpose and implementation status of critical enterprise features in the Sentinel system.

## 1. Persistent Database Migrations (Alembic)
**Status:** _Optional for Hackathon_ (Pending Future Implementation)
**Why you might NOT need it:**
- For the hackathon, you can manage your Supabase schema directly via the Supabase Dashboard SQL Editor.
- If you need to change a table, you can just drop/recreate it or run `ALTER TABLE` commands manually.
- **Verdict:** Skip for now.

## 2. Distributed Session Storage (Redis)
**Status:** _Optional for Hackathon_ (Pending Future Implementation)
**Why you might NOT need it:**
- Hugging Face Free Tier runs a single instance. Python's in-memory dictionaries work perfectly fine for holding conversation state for thousands of concurrent users on one server.
- Redis is only needed if you scale to 5+ servers behind a load balancer.
- **Verdict:** Skip for now.

## 3. Background Task Queue
**Status:** ✅ **Implemented & Essential** (FastAPI BackgroundTasks)
**Purpose:**
- Decouples heavy operations (PDF generation, forensic analysis, external API reporting) from the real-time chat loop.
- **Why it's needed:** Without this, your chatbot will feel "slow" and "laggy" to the scammer. This is critical for the "Realism" score.

## 4. Observability (Metrics & Logging)
**Status:** ✅ **Implemented & Essential** (Prometheus & Structured Logging)
**Purpose:**
- **Metrics (/metrics):** Provides real-time visibility into system health.
- **Why it's needed:** Judges love seeing a "Live Dashboard". This endpoint powers that dashboard.

## Implementation Guide

### Enable Database Migrations (Alembic)
1. Install Alembic: `pip install alembic`
2. Initialize: `alembic init alembic`
3. Configure `alembic.ini` with your database URL.
4. Auto-generate migration: `alembic revision --autogenerate -m "Initial migration"`
5. Apply migration: `alembic upgrade head`

### Enable Distributed Session Storage (Redis)
1. Install Redis client: `pip install redis`
2. Update `ConversationManager` to read/write state from Redis instead of local memory/SQL for active sessions.

