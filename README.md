# Her Otta Lah for 
Pycon-sg-2026
Initial PyCon SG hackathon submission for track2
Her Otta  Lah: Singapore OtterAI Menopause Companion

Track: PyCon Singapore 2026 Hackathon Open Track / Creative Track

Submission repositories:

Main submission repo: https://github.com/AIPensieve/Her-Otta-Lah-PyCon26Hackathon

Team integration repo: https://github.com/clover475/ai-otter-coach.git

Her Otta · Lah is a private AI companion that helps women understand body changes, calm mood shifts, and build gentle daily routines. It is a Singapore-localised AI wellbeing companion for menopausal and postmenopausal women. It combines a voice-first App and a small otter desktop companion. Users can speak naturally in Chinese, English, Singlish, Malay terms, or mixed phrases like “Today so lazy, knee bit Sakit.” The AI understands the state, classifies risk, recommends one low-pressure action, and returns structured JSON for the App and hardware to run fixed flows.

The project is useful, playful, inclusive, and harmless: it helps users say things out loud, calm down, move gently, and build a consented body/mood timeline. It does not diagnose, prescribe, or replace clinicians.

Evaluation Highlights





Process & Product: voice-first flow from “say something” to one small action, record card, and optional timeline.



Data Quality: no questionable scraped health dataset; transparent seed RAG plus consented local-first records.



User Focus: large-action, low-pressure, culturally local singapore womens tone for 45+ Singapore users.



Technical Execution: FastAPI, RAG-ready knowledge base, safety boundary, strict JSON contracts, hardware directives, Pygame accessible micro-game, and hybrid sponsor architecture.



Sponsor alignment:





AI Singapore / SEA-LION: Singlish, Malay, and dialect-aware language normalization and future embeddings.



Google Cloud SG / Vertex AI: multimodal scene understanding and Cloud Run deployment target.



OpenAI: Structured Outputs path for reliable JSON to App and hardware.

Demo Flow

Run the judge-friendly local demo:

.venv/bin/python scripts/demo_hybrid_architecture.py

Expected highlights:

language: singlish_malay_mixed
intent: exercise_request
suggested_action.skill_id: knee_friendly_5min
game_id: heel_drop_game_60s

The demo intentionally works without external API keys. If OPENAI_API_KEY is set, it also attempts a live Structured Outputs validation; otherwise it uses the local strict schema fallback.

Architecture

flowchart LR
    U["User voice / photo<br/>Chinese, English, Singlish, Malay"] --> API["FastAPI AI service"]
    API --> SEA["SEA-LION inspired<br/>local language normalization"]
    SEA --> SAFETY["Safety boundary<br/>no diagnosis, no unsafe exercise"]
    SAFETY --> RAG["Explainable RAG seed knowledge<br/>exercise, body, emotion, safety"]
    SAFETY --> ACT["Small action recommender<br/>calm / move / game / none"]
    ACT --> JSON["Strict structured JSON contract"]
    JSON --> APP["App<br/>record card, timeline, weekly summary"]
    JSON --> HW["ESP32 otter device<br/>AMOLED, buttons/touch, IMU, speaker"]
    API --> GAME["Accessible Pygame spec<br/>heel_drop_game_60s"]
    GAME --> JSON

Hardware Contract

The otter device does not run AI. It consumes hardware_directive and game event specs:

{
  "skill_id": "heel_drop_game_60s",
  "open_fixed_flow": "heel_drop_game_60s",
  "round_screen_state": "playful_timer",
  "watchface": "heel_drop_game_60s",
  "display_text": "Little otter catches red-bean ice",
  "voice_text": "Small-small movement can already. Press when you finish one.",
  "countdown_seconds": 60
}

For game flows, the AI service also returns motion_detection, scoring, completion, and sensor_events, so the App/Pygame/ESP32 firmware share the same event contract.

RAG Knowledge Base

RAG knowledge-base scaffolding for a menopause health-management AI agent, covering:





Women's exercise knowledge base



Menopause body knowledge base



Emotion soothing / meditation knowledge base



Product safety boundary knowledge base



Chinese–English bilingual term mapping



Underlying retrieval, chunking, embedding, and ranking logic

This project uses LlamaIndex to build a locally persistable vector index. Built-in content consists of original summaries based on public health commonsense and core training principles from Next Level—no verbatim excerpts from the book. You can continue adding licensed materials, clinician-reviewed content, or team-authored content under data/raw/.

Quick Start

python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# If using OpenAI embeddings
export OPENAI_API_KEY="..."
python scripts/build_index.py --reset

# Try a retrieval query
python scripts/query_rag.py "I've been sleeping poorly and feeling irritable lately—what exercise is suitable today?"

AI Demo

Run these directly at the hackathon demo:

.venv/bin/python scripts/demo_hackathon_ai.py
.venv/bin/python scripts/demo_hybrid_architecture.py
.venv/bin/python -m pytest -q

See docs/ai_technical_delivery.md for AI technical delivery notes.

If you do not have an OpenAI key, install llama-index-embeddings-huggingface and use local embeddings:

python scripts/build_index.py --embedding-provider huggingface --reset
python scripts/query_rag.py "I feel very low after hot flashes—what can I do?" --embedding-provider huggingface

Directory

data/
  raw/                       # Licensed materials go here: txt/md/pdf/docx
  seed/knowledge_seed.jsonl  # Original seed knowledge for live demos
  storage/                   # LlamaIndex persisted index
menopause_rag/
  bilingual.py               # Chinese–English term mapping and query expansion
  chunking.py                # Chunking by domain, intent, and risk
  config.py                  # Paths and index configuration
  index.py                   # Build, load, and persist index
  models.py                  # Data structures
  retriever.py               # Retrieval, filtering, lightweight reranking
  safety.py                  # Safety boundary checks
scripts/
  build_index.py             # Build RAG index
  query_rag.py               # Command-line retrieval demo
tests/
  test_chunking_safety.py

Product Usage Mapping





Home screen state understanding: Retriever.search(..., intent="home_state")



Exercise recommendations: intent="exercise_recommendation", prioritise exercise and body



Emotion soothing scripts: intent="emotion_soothing", prioritise emotion



Body/mood log interpretation: intent="log_interpretation", retrieve body, emotion, and safety



Weekly summary: intent="weekly_summary", retrieve all non-urgent domains



Doctor communication questions: intent="doctor_questions", always include safety



Safety boundary checks: call SafetyBoundary.classify() first, then decide whether normal recommendations are allowed

Local-First Memory

Long-term memory does not rely on external women's-health raw datasets. The App first collects a user-consented personal timeline:





data/memory/events.jsonl: AI event stream



data/memory/markdown/{user_id}.md: human-readable timeline



data/memory/records.csv: body/mood records



data/memory/actions.csv: calm-down / move-a-little completion tracking



data/memory/wearable_daily.csv: smart-hardware daily summaries

Recommended analytics table schemas:

records.csv
user_id,date,time,body_tags,mood_tags,sleep_quality,hot_flash,knee_pain,action_id,free_text

actions.csv
user_id,started_at,completed_at,action_type,skill_id,duration_seconds,completion_state

wearable_daily.csv
user_id,date,resting_hr,hrv,sleep_minutes,wake_count,skin_temp_delta,steps

Safety Note

This knowledge base is for health education, lifestyle guidance, and preparing for medical conversations only. It does not replace clinical diagnosis, emergency care, or prescription advice. Chest pain, difficulty breathing, one-sided limb weakness, self-harm risk, or unusually heavy bleeding should all trigger the safety boundary.
