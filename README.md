# Yaquod Agent

A bilingual (Arabic/English) real-time voice AI assistant powered by **LiveKit Agents** and **LiveKit Inference** (Deepgram STT + Cartesia TTS + Gemini LLM).

## Features

- **Real-time voice conversation** with low-latency streaming
- **Arabic & English support** with automatic language detection
- **Dynamic language switching** — the agent detects when you switch languages and responds in kind
- **Deepgram Nova-3** for speech-to-text (via LiveKit Inference)
- **Cartesia Sonic-3** for text-to-speech (via LiveKit Inference)
- **Google Gemini 3.1 Flash Lite** for conversational LLM (via LiveKit Inference)
- **Multilingual turn detection** for natural conversation flow

## Prerequisites

- Python 3.11+
- A [LiveKit Cloud](https://livekit.io) account
- A [LiveKit Cloud](https://livekit.io) account with Inference enabled (included on all plans)

## Setup

1. **Clone the repo and create the conda environment:**

   ```bash
   conda env create -f environment.yml
   conda activate livekit-agent
   ```

2. **Configure environment variables:**

   Copy `.env-example` to `.env` and fill in your LiveKit Cloud credentials:

   ```bash
   cp .env-example .env
   ```

3. **Run the agent:**

   ```bash
   python agent.py start
   ```

## Alternative: Using Ollama (Llama) instead of Gemini

To run the LLM locally with Ollama instead of LiveKit Inference:

1. **Install Ollama** from [ollama.ai](https://ollama.ai) and pull a model:

   ```bash
   ollama pull llama3.1:8b
   ```

2. **In `agent.py`, replace the inference.LLM line with:**

   ```python
   llm=openai.LLM.with_ollama(
       model="llama3.1:8b",
       base_url=os.getenv("OLLAMA_BASE_URL"),
   ),
   ```

3. **Set `OLLAMA_BASE_URL` in `.env`** (defaults to `http://localhost:11434`):

   ```env
   OLLAMA_BASE_URL=http://localhost:11434
   ```

4. **Ensure Ollama is running** before starting the agent:

   ```bash
   ollama serve
   ```

> Note: STT and TTS still use LiveKit Inference (Deepgram + Cartesia). Only the LLM changes.

## Linting & Formatting

This project uses [Ruff](https://docs.astral.sh/ruff) — a fast Python linter and formatter written in Rust.

### Commands

```bash
# Lint (report issues)
ruff check .

# Lint + auto-fix safe issues
ruff check --fix .

# Format (rewrite files in place)
ruff format .

# Check if files are already formatted (no changes)
ruff format --check .
```

### Configuration

All settings live in `pyproject.toml` under `[tool.ruff]`, `[tool.ruff.lint]`, and `[tool.ruff.format]`.

## Usage

Connect using any LiveKit-compatible client (e.g., [LiveKit CLI](https://github.com/livekit/livekit-cli), [Agents Playground](https://agents-playground.livekit.io), or a custom web/mobile app).

The agent greets in Arabic by default. Speak in Arabic or English — it auto-detects and switches dynamically.

## Architecture

- `agent.py` — Main application defining the `Assistant` class and RTC session
- `environment.yml` — Conda environment specification
- `pyproject.toml` — Linting and formatting configuration (ruff)
- `tests/` — Unit tests for the agent configuration

The agent uses LiveKit Agents **v1 session API** (`Agent`, `AgentServer`, `AgentSession`) with `inference.STT` (Deepgram Nova-3), `inference.LLM` (Gemini 3.1 Flash Lite), and `inference.TTS` (Cartesia Sonic-3), plus `TurnDetector` for turn detection. All AI models are accessed through LiveKit Inference — no separate API keys required beyond LiveKit Cloud credentials.
