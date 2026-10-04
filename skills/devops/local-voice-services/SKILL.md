---
name: local-voice-services
description: "Set up local STT/TTS: faster-whisper (STT), Piper (TTS)."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
category: devops
metadata:
  hermes:
    tags: [docker, voice, stt, tts, faster-whisper, piper, speaches, audio, speech]
    related_skills: [hermes-agent]
  tools:
    - pip: faster-whisper, piper-tts, piper-tts[http], speaches
    - docker: linuxserver/faster-whisper, ghcr.io/rhasspy/piper, ghcr.io/speaches-ai/speaches
    - config: ~/.hermes/config.yaml (stt/tts sections)
---

# Local Voice Services: STT + TTS

**Use when:** Setting up speech-to-text (STT) or text-to-speech (TTS) locally, running voice assistants, or integrating audio workflows with Hermes without external API costs.

This skill covers setting up speech-to-text (STT) and text-to-speech (TTS) locally using faster-whisper, Piper, and optionally Speaches for a unified API.

## Quick Start

### Option 1: Separate Services (Recommended for flexibility)

**STT (Speech → Text):** faster-whisper
```bash
# Install
pip install faster-whisper

# Or run Docker
docker run -d \
  -e WHISPER_MODEL=base \
  -p 10300:10300 \
  lscr.io/linuxserver/faster-whisper:latest
```

**TTS (Text → Speech):** Piper
```bash
# Install
pip install piper-tts piper-tts[http]

# Download voice
python -m piper.download_voices en_US-lessac-medium

# Start server
python -m piper.http_server -m en_US-lessac-medium --host 0.0.0.0 --port 5002
```

### Option 2: Unified API (Speaches)

```bash
pip install speaches
speaches serve --whisper-model base --voice en_US-lessac-medium
# Or Docker:
docker run ghcr.io/speaches-ai/speaches:latest
```

### Option 3: Hermes Integration

Configure in `~/.hermes/config.yaml`:
```yaml
stt:
  enabled: true
  provider: local
  local:
    model: base  # tiny, base, small, medium, large-v3

tts:
  provider: piper  # or: edge, elevenlabs, openai, minimax, mistral, gemini, piper, kittentts, deepinfra, xai
```

## Model Choices

### faster-whisper (STT)
| Model | RAM | Speed | Quality |
|-------|-----|-------|--------|
| tiny | ~100MB | ⚡⚡⚡⚡⚡ | ⭐⭐ |
| base | ~200MB | ⚡⚡⚡⚡ | ⭐⭐⭐ |
| small | ~500MB | ⚡⚡⚡ | ⭐⭐⭐⭐ |
| medium | ~1.5GB | ⚡⚡ | ⭐⭐⭐⭐ |
| large-v3 | ~4GB | ⚡ | ⭐⭐⭐⭐⭐ |

### Piper (TTS)
| Voice | Language | Size | Quality |
|-------|----------|------|--------|
| en_US-lessac-medium | English (US) | ~150MB | Medium |
| en_GB-stereotypical-female-medium | English (UK) | ~150MB | Medium |
| de_DE-thorsten-medium | German | ~150MB | Medium |

See `piper.download_voices --help` for all available voices.

## Docker Compose Template

See `/user-docs/voice-services/docker-compose.yml` for a complete setup with:
- faster-whisper (STT) on port 10300
- piper-tts (TTS) on port 5002
- Optional: speaches unified API

## Hermes Voice Controls

Slash commands in Hermes:
- `/voice on` — Enable voice-to-voice (full voice messaging)
- `/voice tts` — Always use text-to-speech for responses
- `/voice off` — Disable all voice features

## Testing

```bash
# Test STT
python -m faster_whisper.transcribe --model base audio.wav

# Test TTS
python -m piper -m en_US-lessac-medium -f output.wav -- "Hello world"

# Test unified API (Speaches)
curl -X POST http://localhost:11435/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"model": "en_US-lessac-medium", "input": "Hello"}'
```

## Troubleshooting

1. **Model loading too slow** — Use int8 quantized models (e.g., `base-int8`)
2. **STT not working** — Verify `faster-whisper` is installed and model is downloaded
3. **TTS no audio** — Check voice model files (`.onnx` + `.onnx.json`) exist in data dir
4. **GPU acceleration** — For faster-whisper: add `--device cuda` flag (requires CUDA 12+)

## Common Pitfalls

- **Piper is TTS ONLY** — It does NOT do speech-to-text. You need faster-whisper (or another STT engine) for that.
- **Voice models must stay together** — Piper stores `.onnx` weights and `.onnx.json` config; both must exist in the same directory.
- **Model loading is one-time** — Don't call Piper CLI in loops; load the HTTP server once and reuse it.
- **Language mismatch** — The language you speak to STT must match the language of the TTS voice model, or use a multi-language capable system.
- **OpenAI API compatibility** — Speaches exposes OpenAI-compatible endpoints (`/v1/audio/transcriptions` and `/v1/audio/speech`) so existing clients work without changes.

## References

- faster-whisper: https://github.com/SYSTRAN/faster-whisper
- Piper: https://github.com/rhasspy/piper
- Speaches: https://github.com/speaches-ai/speaches
- Docker faster-whisper: https://hub.docker.com/r/linuxserver/faster-whisper
- Piper Docker: https://github.com/rhasspy/piper-docker
