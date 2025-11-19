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
 git clopython -m venv venv
source venv/bin/activate   # macOS / Linux
venv\Scripts\activate      # Windows
pip install -r requirements.txt
ne https://github.com/<your-username>/serenemind.git
OPENAI_API_KEY=sk-...

streamlit run src/app.py

---

# 5) Sample Python prototype
Create these files in `src/`.

## `src/app.py` (Streamlit front-end)
```python
"""
Simple Streamlit demo for SereneMind.
Run: streamlit run src/app.py
"""
import streamlit as st
from agent.core import SereneAgent
from agent.safety import check_crisis, CRISIS_KEYWORDS

st.set_page_config(page_title="SereneMind Demo", page_icon="🕊️")

st.title("SereneMind — Mental Health Support Companion")
st.write("A gentle, non-diagnostic companion for mood check-ins and coping suggestions.")

agent = SereneAgent()

if "history" not in st.session_state:
    st.session_state.history = []

with st.sidebar:
    st.header("Quick Help")
    st.markdown("If you are in immediate danger, call local emergency services.")
    st.markdown("This demo does not save sensitive data.")

# Mood check-in
st.subheader("Quick Mood Check-in")
mood = st.text_input("How are you feeling right now? (a sentence or two)", key="mood_input")

if st.button("Send"):
    if mood:
        # Safety check
        crisis_flag, matched = check_crisis(mood)
        if crisis_flag:
            st.warning("I detected language that may indicate you need immediate support.")
            st.info(agent.get_crisis_response(matched))
        else:
            response = agent.get_response(mood)
            st.session_state.history.append(("You", mood))
            st.session_state.history.append(("SereneMind", response))

# Conversation history
st.subheader("Conversation")
for speaker, text in st.session_state.history[::-1]:
    if speaker == "You":
        st.markdown(f"**You:** {text}")
    else:
        st.markdown(f"**SereneMind:** {text}")

# Simple footer
st.markdown("---")
st.markdown("Prototype — not a replacement for professional care.")import os
import openai
from dotenv import load_dotenv
from .emotion_utils import detect_emotion
from .safety import safe_prompt_suffix

load_dotenv()
openai.api_key = os.getenv("OPENAI_API_KEY")

class SereneAgent:
    def __init__(self, model="gpt-4o-mini" if True else "gpt-4o"):
        # Use any available OpenAI model compatible with your account.
        self.model = model

    def _call_llm(self, prompt, max_tokens=250):
        # Simple wrapper — adapt to your API client/params
        resp = openai.Completion. create(
            engine=self.model,
            prompt=prompt,
            max_tokens=max_tokens,
            temperature=0.7,
            n=1,
        )
        return resp.choices[0].text.strip()

    def get_response(self, user_text):
        # Detect a simple emotion tag (used for tailoring tone)
        emotion = detect_emotion(user_text)
        # Build a short prompt that instructs the model to be empathic and safe
        prompt = (
            f"You are SereneMind, a calm and culturally respectful wellbeing assistant"
            f"User says: \"{user_text}\"\n"
            f"Detected_emotion: {emotion}\n"
            "Respond empathetically, avoid diagnosis, offer 1-2 short coping suggestions, "
            "and ask an open question to continue the conversation.\n"
            f"{safe_prompt_suffix()}\n"
        )
        return self._call_llm(prompt)

    def get_crisis_response(self, matched_keywords):
        # Matches passed from safety.check_crisis
        return (
            "I’m really sorry that you’re feeling this way. "
            "If you are in immediate danger, please call your local emergency number right now. "
            "If you can, consider contacting this helpline or someone you trust. "
            f"(Detected keywords: {', '.join(matched_keywords)})"
        )
def detect_emotion(text: str) -> str:
    """
    Baseline rule-based emotion detection — returns one of:
    'sad', 'anxious', 'angry', 'neutral', 'happy'
    """
    t = text.lower()
    if any(w in t for w in ["sad", "down", "depressed", "unhappy", "hopeless"]):
        return "sad"
    if any(w in t for w in ["anxious", "anxiety", "worried", "panic", "nervous"]):
        return "anxious"
    if any(w in t for w in ["angry", "mad", "furious", "frustrat"]):
        return "angry"
    if any(w in t for w in ["happy", "good", "great", "fine", "okay"]):
        return "happy"
    return "neutral"
CRISIS_KEYWORDS = [
    "kill myself", "suicide", "want to die", "end my life",
    "hurt myself", "hang myself", "I will die", "I'm going to die",
    "kill me", "I can't go on"
]

def check_crisis(text: str):
    """
    Returns (flag: boolean, matched_keywords:list)
    """
    lower = text.lower()
    matched = [kw for kw in CRISIS_KEYWORDS if kw in lower]
    return (len(matched) > 0, matched)

def safe_prompt_suffix():
    """
    Return a short safety reminder to the LLM prompt to avoid diagnosis.
    avoid giving medical instructions, and present helpline suggestions if needed.
    """
    return (
        "IMPORTANT SAFETY: Do not provide a diagnosis. If you detect suicidal or self-harm language, "
        "Encourage contacting local emergency services and provide helpline resources. "
        "Be concise (no more than 3 sentences) and empathetic."
    )
from agent.safety import check_crisis
def test_crisis_detects():
    assert check_crisis("I want to kill myself")[0] is True
    assert check_crisis("I am sad") [0] is False




cd serenemind
