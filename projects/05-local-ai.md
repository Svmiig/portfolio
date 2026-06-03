# Local AI — Voice-to-Text & On-Device LLM

> **TL;DR** — Set up a fully local AI stack for speech-to-text and lightweight LLM inference across two machines. No data leaves the device, no API costs, no subscriptions. Whisper Turbo handles voice transcription on both Mac and Windows. Used daily for voice-driven workflow control.

---

## The Idea: AI That Stays on Your Machine

Cloud AI is powerful but comes with tradeoffs: your data goes to someone else's server, every token costs money, and you're dependent on internet connectivity. For certain use cases — especially voice input and quick reasoning tasks — local models are a better fit.

The goal: be able to dictate notes, commands, and ideas into any tool, with transcription happening instantly on-device. No uploads, no latency from network round-trips, no per-minute billing.

---

## Problem

Voice-to-text solutions either:
- Send audio to cloud APIs (privacy risk, cost)
- Require expensive hardware to run well
- Produce inconsistent quality across operating systems

The question: can you run genuinely useful speech-to-text locally on consumer hardware, for free, on both Mac and Windows?

---

## Solution

**Whisper Turbo** — OpenAI's open-source speech recognition model, Turbo variant. Optimized for speed without sacrificing accuracy. Runs on both Apple Silicon and Windows via standard Python tooling. Transcribes speech in near real-time with strong Finnish language support.

---

## Hardware Setup

Two-machine workflow — the same stack runs on both:

**MacBook Air M2** — portable, daily driver
- Apple Silicon acceleration via Core ML
- Whisper Turbo runs in real-time without fan noise
- Used for on-the-go voice input, meeting dictation, mobile workflow

**Windows Desktop** — stationary workstation
- Standard CPU/GPU inference
- Whisper Turbo via Python, same tooling as Mac
- Used for heavier tasks, longer transcription sessions

Having the same local AI stack on both machines means the workflow is consistent regardless of which device is in use.

---

## Setup

```bash
# Install Whisper
pip install openai-whisper

# Transcribe a file
whisper audio.m4a --model turbo --language Finnish

# For real-time use — faster-whisper (optimized runtime)
pip install faster-whisper
```

On Apple Silicon (M2), the Turbo model runs comfortably in real-time via Core ML acceleration. On Windows, the same Python package works via CPU or GPU depending on hardware. Finnish language accuracy is strong on both platforms.

---

## Use Cases

**Voice-driven workflow control**
Dictate meeting notes, task lists, ideas directly into Obsidian or any text field. Whisper transcribes instantly. No manual typing for longer-form content.

**Private transcription**
Meeting recordings, personal notes, sensitive content — transcribed locally. Audio never leaves the machine.

**Offline capability**
Works without internet. Useful when traveling, in poor connectivity, or when cloud APIs are down.

**Cost elimination**
Zero per-word cost for transcription. No monthly subscription. Reserve cloud API calls for tasks that actually require frontier model reasoning.

---

## Stack

- **Whisper Turbo** (OpenAI, open-source) — speech-to-text model
- **faster-whisper** — optimized Whisper runtime for real-time use
- **Apple Silicon M2** — Mac inference via Core ML acceleration
- **Windows Desktop** — PC inference via Python/CUDA

---

## Results

- Voice transcription running in real-time on both machines
- Zero cloud dependency for speech-to-text
- Finnish language accuracy on par with cloud solutions
- Consistent workflow across Mac and Windows
- Data never leaves the device
