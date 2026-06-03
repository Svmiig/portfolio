# Local AI — Voice-to-Text & On-Device LLM

> **TL;DR** — Set up a fully local AI stack for speech-to-text and lightweight LLM inference. No data leaves the machine, no API costs, no subscriptions. Whisper Turbo handles voice transcription; Qwen3 4B runs as a local LLM via Open WebUI. Used daily for voice-driven workflow control.

---

## The Idea: AI That Stays on Your Machine

Cloud AI is powerful but comes with tradeoffs: your data goes to someone else's server, every token costs money, and you're dependent on internet connectivity. For certain use cases — especially voice input and quick reasoning tasks — local models are a better fit.

The goal here was simple: be able to dictate notes, commands, and ideas into any tool, with transcription happening instantly on-device. No uploads, no latency from network round-trips, no per-minute billing.

---

## Problem

Voice-to-text solutions either:
- Send audio to cloud APIs (privacy risk, cost)
- Require expensive hardware to run well
- Produce low-quality output on consumer hardware

LLM solutions either:
- Are cloud-only (OpenAI, Anthropic)
- Require high-end GPUs to run locally
- Have poor UX for non-technical users

The question: can you run genuinely useful AI locally on a standard MacBook, for free?

---

## Solution

Two components, both running entirely on-device:

**Whisper Turbo** — OpenAI's open-source speech recognition model, Turbo variant. Optimized for speed without sacrificing accuracy. Runs on Apple Silicon (M-series) via Core ML acceleration. Transcribes speech in near real-time.

**Qwen3 4B via Open WebUI** — Alibaba's Qwen3 4B parameter model, quantized for consumer hardware. Served locally, accessed through Open WebUI — a ChatGPT-like browser interface that runs on localhost. Used for quick reasoning tasks, drafting, and workflow assistance without touching the cloud.

---

## Setup

### Whisper Turbo

```bash
# Install whisper
pip install openai-whisper

# Run transcription on a file
whisper audio.m4a --model turbo --language Finnish

# Or use faster-whisper for real-time use
pip install faster-whisper
```

On Apple Silicon (M2), the Turbo model runs comfortably in real-time. Finnish language support is strong — critical for a Finnish-language workflow.

### Open WebUI + Qwen3 4B

```bash
# Install Ollama (local model runner)
brew install ollama

# Pull Qwen3 4B
ollama pull qwen3:4b

# Install Open WebUI
pip install open-webui

# Start
open-webui serve
# Access at http://localhost:8080
```

Open WebUI provides a full chat interface with conversation history, model switching, and system prompt configuration — all running locally.

---

## Use Cases

**Voice-driven workflow control**
Dictate meeting notes, task lists, ideas. Whisper transcribes instantly. Output drops directly into Obsidian or any text field. No manual typing for longer-form content.

**Private drafting**
Use Qwen3 for first drafts of sensitive content — internal documents, personal notes, anything where you don't want the text processed by a third party.

**Offline capability**
Both tools work without internet. Useful when traveling, in poor connectivity, or when cloud APIs are unavailable.

**Cost elimination**
Zero per-token cost for routine tasks that don't require frontier model capability. Reserve Claude API calls for complex reasoning; use local models for simple tasks.

---

## Hardware

- **Device:** MacBook Air M2
- **RAM:** 8–16 GB (sufficient for 4B parameter models)
- **Acceleration:** Apple Silicon Neural Engine via Core ML (Whisper), Metal GPU (Ollama)

No discrete GPU required. M-series chips handle both models smoothly.

---

## Stack

- **Whisper Turbo** (OpenAI, open-source) — speech-to-text
- **faster-whisper** — optimized Whisper runtime
- **Qwen3 4B** (Alibaba, open-source) — local LLM
- **Ollama** — local model serving
- **Open WebUI** — browser-based chat interface for local models
- **Apple Silicon M2** — on-device inference

---

## Results

- Voice transcription running in real-time, fully on-device
- Zero cloud dependency for speech-to-text
- Zero API cost for lightweight LLM tasks
- Finnish language transcription accuracy on par with cloud solutions
- Data never leaves the machine
