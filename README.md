# HandsFree 🎙️

**Voice-Controlled, Location-Aware Personal Agent**

> *"You're driving, hands on the wheel. You speak. Your phone acts instantly — sends your live GPS location, sets alarms, checks weather — all without touching the screen, all processed on-device in under 500ms."*

Built for the **Cactus x Google DeepMind — FunctionGemma Hackathon**

---

## What It Does

HandsFree is a voice-first personal agent built on the Cactus inference engine and Google's FunctionGemma model. Speak a command, and the system transcribes your audio on-device, detects location intent, grabs real GPS coordinates, intelligently routes between on-device and cloud inference, and executes function calls — all in a single seamless pipeline.

**Why it matters:** You can't type while driving, cycling, or cooking. HandsFree solves this with sub-500ms voice-to-action, mostly without internet.

---

## Architecture

```
User Speaks
    │
    ▼
cactus_transcribe (Whisper on-device) .............. ~120ms
    │
    ▼
Location Intent Detection (keyword matching) ....... ~0ms
    │
    ▼ [if location detected]
CoreLocation GPS + Reverse Geocode ................. ~50ms
    │
    ▼
Query Rewriting (inject address + Maps link)
    │
    ▼
Complexity Estimator (simple / medium / complex)
    │
    ├──[simple/medium]──► FunctionGemma (on-device) .. ~80ms
    │
    └──[complex]────────► Gemini Flash (cloud) ....... ~800ms
    │
    ▼
Action Executor → Display Results + Pipeline Viz
```

**Smart Routing:** Simple single-tool commands stay blazing fast on-device. Complex multi-action requests gracefully fall back to Gemini in the cloud.

---

## Key Features

- **On-Device Transcription** — Whisper via Cactus, ~100-200ms, no internet required
- **Real GPS Location** — CoreLocation via pyobjc, reverse-geocoded to human-readable addresses
- **Hybrid Inference Routing** — Complexity estimator routes simple queries on-device, complex queries to cloud
- **Context Chains** — Pre-configured routines (e.g., "I've reached the office" triggers 4 actions in <100ms, no LLM needed)
- **Offline Mode** — ~80% of commands work without internet
- **Compare Mode** — Side-by-side view of FunctionGemma vs Gemini vs Hybrid routing with timing bars

---

## Example Scenarios

| Scenario | Command | Latency | Routing |
|---|---|---|---|
| Location Sharing | "Send my location to Mom" | ~280ms | 100% on-device |
| Context Chain | "I've reached the office" (triggers 4 actions) | ~350ms | On-device, skips LLM |
| Simple Query | "What's the weather in Tokyo?" | ~185ms | On-device |
| Complex Multi-Action | "Text Sarah I'm running late, check weather in NYC, set a 10 min timer" | ~930ms | Cloud (Gemini Flash) |
| Offline Mode | "Set an alarm for 6 AM and play morning playlist" | ~210ms | On-device, no internet |

---


## Setup

### Prerequisites

- macOS with Location Services enabled
- Cactus engine installed
- Gemini API key (for cloud fallback)

### Installation

```bash
# Download Whisper model
cactus download whisper-small

# Authenticate
cactus auth
export GEMINI_API_KEY="your-key-here"

# Install dependencies
pip install pyobjc-framework-CoreLocation streamlit audio-recorder-streamlit google-genai
```

### Enable Location Services

Go to **System Settings → Privacy & Security → Location Services** and allow your terminal app (Terminal or iTerm).

---

## Usage

### Run the Streamlit App

```bash
streamlit run app.py
```

### Run Benchmark

```bash
python benchmark.py
```

### Submit to Leaderboard

```bash
python submit.py --team "YourTeamName" --location "YourCity"
```

---

## Implementation Phases

| Phase | Task | Time |
|---|---|---|
| 1 | Foundation: verify build, download Whisper, install deps | 15 min |
| 2 | Location Service: CoreLocation + pyobjc + reverse geocode | 30 min |
| 3 | Voice Pipeline: orchestration, intent detection, GPS injection | 1 hr |
| 4 | Context Chains: pre-configured routines, chain matching | 30 min |
| 5 | Improve generate_hybrid: complexity routing, caching | 1 hr |
| 6 | Streamlit UI: mic, location sidebar, pipeline viz, compare mode | 1.5 hrs |
| 7 | Testing, benchmarking, submission | 30 min |
| **Total** | | **~5 hours** |

---

## Tech Stack

- **Cactus Engine** — On-device inference runtime
- **FunctionGemma (270M)** — On-device function calling model
- **Gemini Flash** — Cloud fallback for complex queries
- **Whisper (Small)** — On-device speech-to-text
- **CoreLocation (pyobjc)** — Native macOS GPS access
- **Streamlit** — Web UI with mic input and pipeline visualization

---

## Benchmark Scoring

- **F1 Accuracy** — 60% weight
- **Speed** — 15% weight (under 500ms = full marks)
- **On-Device Ratio** — 25% weight (more local = better)
- Difficulty weighting: easy 20%, medium 30%, hard 50%

---

*HandsFree — Built with Cactus Engine + Google FunctionGemma + Gemini Flash*
