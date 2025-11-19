# SereneMind-AI-Mental-Health-Support-Companion
**SereneMind** is a lightweight prototype of a culturally sensitive, empathetic mental-health chat assistant.   It is intended for non-diagnostic emotional support, daily check-ins, coping suggestions, and safe routing to crisis resources when necessary.

''' vfgvg '''
serenemind/
├─ README.md
├─ LICENSE
├─ requirements.txt
├─ .env.example
├─ src/
│  ├─ app.py                  # Streamlit front-end + glue
│  ├─ agent/
│  │  ├─ __init__.py
│  │  ├─ core.py             # Agent prompt templates, LLM call wrapper
│  │  ├─ safety.py           # Crisis detection & safety routing
│  │  └─ emotion_utils.py    # lightweight emotion detection (rule-based)
│  ├─ resources/
│  │  └─ helplines.json      # country-specific helplines (example entries)
│  └─ tests/
│     ├─ test_safety.py
│     └─ test_emotion_utils.py
├─ demo/
│  └─ demo_script.txt        # YouTube demo script (also below)
└─ assets/
   ├─ thumbnail.png          # generated thumbnail image (from the tool)
   └─ mock_conversation.txt
# SereneMind — AI Mental Health Support Companion

**SereneMind** is a lightweight prototype of a culturally sensitive, empathetic mental-health chat assistant.  
It is intended for non-diagnostic emotional support, daily check-ins, coping suggestions, and safe routing to crisis resources when necessary.

> ⚠️ **Important:** This project is a prototype and **not** a replacement for professional mental health care. It should never be used to diagnose or treat mental health conditions.

## Features
- Text-based supportive conversations using an LLM.
- Lightweight emotion detection (rule-based baseline).
- Safety & crisis detection with routing to helplines.
- Streamlit UI for quick demo and testing.

## Repo layout
(See the repository file tree in the repo root.)

## Quick start (local)
1. Clone the repo:
```bash
git clone https://github.com/<your-username>/serenemind.git
cd serenemind
