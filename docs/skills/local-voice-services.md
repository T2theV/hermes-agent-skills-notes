# Local Voice Services (STT + TTS) Skill

## Overview

The **local-voice-services** skill enables you to set up **speech-to-text (STT)** and **text-to-speech (TTS)** locally using **faster-whisper**, **Piper**, and optionally **Speaches** for a unified API.

**Key Benefits**:
- **No external API costs** — Run entirely locally
- **Privacy** — Your voice data never leaves your machine
- **Low latency** — No network round-trips
- **Full control** — Customize models, voices, and settings

## Location

```
/opt/data/skills/devops/local-voice-services/SKILL.md
```

## Repository Location

```
docs/skills/local-voice-services.md
```

## How to Use This Skill as an Agent

### Step 1: Load the Skill Documentation

Before using this skill, load the full skill definition:

```python
from hermes_tools import skill_view

# Load the skill
skill = skill_view(name="local-voice-services")
```

This will give you access to:
- Full setup instructions
- Model selection guidance
- Docker configuration
- Hermes integration commands

### Step 2: Choose Your Deployment Method

**Option A: Separate Services (Recommended)**
- STT: faster-whisper (port 10300)
- TTS: Piper HTTP server (port 5002)

**Option B: Unified API (Speaches)**
- Single service with both STT and TTS
- OpenAI-compatible endpoints

**Option C: Hermes Integration**
- Configure in `~/.hermes/config.yaml`
- Use slash commands for voice control

### Step 3: Deploy and Test

```bash
# Check if services are running
curl http://localhost:10300/health  # faster-whisper
curl http://localhost:5002/         # Piper

# Test STT
curl -X POST http://localhost:10300/stt   -H 'Content-Type: application/json'   -d '{"audio": "base64_encoded_audio", "language": "en"}'

# Test TTS
curl -X POST http://localhost:5002/tts   -H 'Content-Type: application/json'   -d '{"text": "Hello world", "voice": "en_US-lessac-medium"}'
```

## Purpose

This skill is used when you need:

- **Voice assistants** — Run local voice interfaces
- **Transcription** — Convert speech to text without external APIs
- **Text-to-speech** — Generate natural-sounding speech
- **Hermen voice workflows** — Enable `/voice on`, `/voice tts`, `/voice off` commands
- **Privacy-focused audio processing** — Keep all data local

## How It Works

### 1. Speech-to-Text (STT) with faster-whisper

**What it does**: Converts audio to text using Whisper models.

**Available models**:
| Model | RAM | Speed | Quality |
|-------|-----|-------|--------|
| tiny | ~100MB | ⚡⚡⚡⚡⚡ | ⭐⭐ |
| base | ~200MB | ⚡⚡⚡⚡ | ⭐⭐⭐ |
| small | ~500MB | ⚡⚡⚡ | ⭐⭐⭐⭐ |
| medium | ~1.5GB | ⚡⚡ | ⭐⭐⭐⭐ |
| large-v3 | ~4GB | ⚡ | ⭐⭐⭐⭐⭐ |

**Command**:
```bash
# Install
pip install faster-whisper

# Run with Docker
docker run -d   -e WHISPER_MODEL=base   -p 10300:10300   lscr.io/linuxserver/faster-whisper:latest

# Test
python -m faster_whisper.transcribe --model base audio.wav
```

### 2. Text-to-Speech (TTS) with Piper

**What it does**: Generates natural-sounding speech from text.

**Available voices**:
| Voice | Language | Size |
|-------|----------|------|
| en_US-lessac-medium | English (US) | ~150MB |
| en_GB-stereotypical-female-medium | English (UK) | ~150MB |
| de_DE-thorsten-medium | German | ~150MB |

**Command**:
```bash
# Install
pip install piper-tts piper-tts[http]

# Download voice
python -m piper.download_voices en_US-lessac-medium

# Start HTTP server
python -m piper.http_server -m en_US-lessac-medium   --host 0.0.0.0 --port 5002

# Generate speech
python -m piper -m en_US-lessac-medium -f output.wav -- "Hello world"
```

### 3. Unified API with Speaches (Optional)

**What it does**: Provides a single API endpoint for both STT and TTS.

**Command**:
```bash
# Install
pip install speaches

# Run with both STT and TTS
speaches serve --whisper-model base --voice en_US-lessac-medium

# Or Docker
docker run ghcr.io/speaches-ai/speaches:latest
```

**Endpoints**:
- `/v1/audio/transcriptions` — STT (OpenAI-compatible)
- `/v1/audio/speech` — TTS (OpenAI-compatible)

## Deployment Options

### Option 1: Separate Services (Most Flexible)

**Docker Compose**:
```yaml
version: '3.8'
services:
  faster-whisper:
    image: lscr.io/linuxserver/faster-whisper:latest
    container_name: faster-whisper
    environment:
      - WHISPER_MODEL=base
    ports:
      - "10300:10300"
    volumes:
      - whisper_data:/config
      - whisper_models:/models
    restart: unless-stopped

  piper-tts:
    image: ghcr.io/rhasspy/piper:latest
    container_name: piper-tts
    environment:
      - VOICE=en_US-lessac-medium
    ports:
      - "5002:5002"
    volumes:
      - piper_data:/data
      - piper_models:/models
    restart: unless-stopped

volumes:
  whisper_data:
  whisper_models:
  piper_data:
  piper_models:
```

### Option 2: Unified Speaches API

**Docker Compose**:
```yaml
version: '3.8'
services:
  speaches:
    image: ghcr.io/speaches-ai/speaches:latest
    container_name: speaches
    environment:
      - WHISPER_MODEL=base
      - VOICE=en_US-lessac-medium
    ports:
      - "11435:11435"
    restart: unless-stopped
```

### Option 3: Hermes Integration

**Configuration** (`~/.hermes/config.yaml`):
```yaml
stt:
  enabled: true
  provider: local
  local:
    model: base  # tiny, base, small, medium, large-v3

tts:
  provider: piper  # or: edge, elevenlabs, openai, minimax, mistral, gemini
```

**Slash Commands**:
- `/voice on` — Enable voice-to-voice (full voice messaging)
- `/voice tts` — Always use text-to-speech for responses
- `/voice off` — Disable all voice features

## Usage Examples

### Basic STT

```bash
# Convert audio file to text
python -m faster_whisper.transcribe --model base input.wav

# With Docker API
curl -X POST http://localhost:10300/stt   -H 'Content-Type: application/json'   -d '{"audio": "base64_encoded_audio", "language": "en"}'
```

### Basic TTS

```bash
# Generate speech file
python -m piper -m en_US-lessac-medium -f output.wav -- "Hello world"

# With Docker API
curl -X POST http://localhost:5002/tts   -H 'Content-Type: application/json'   -d '{"text": "Hello world", "voice": "en_US-lessac-medium"}'
```

### Unified Speaches API

```bash
# STT
curl -X POST http://localhost:11435/v1/audio/transcriptions   -H 'Content-Type: application/json'   -d '{"model": "base", "file": "audio.wav"}'

# TTS
curl -X POST http://localhost:11435/v1/audio/speech   -H 'Content-Type: application/json'   -d '{"model": "en_US-lessac-medium", "input": "Hello world"}'
```

### Hermes Voice Control

```bash
# Enable voice features
/voice on

# Use TTS for all responses
/voice tts

# Disable voice features
/voice off
```

## Best Practices

✅ **DO**:
- Use `base` or `small` models for balance of speed/quality
- Download voice models once and reuse the HTTP server
- Configure in `~/.hermes/config.yaml` for persistence
- Test with short audio clips before full deployment
- Use int8 quantized models for faster loading

❌ **DON'T**:
- Don't call Piper CLI in loops (load HTTP server once)
- Don't forget to download voice models before starting server
- Don't use different languages for STT and TTS without translation
- Don't assume all voices support all languages

## Error Handling

### STT Issues

1. **Model loading too slow** — Use int8 quantized models (`base-int8`)
2. **STT not working** — Verify `faster-whisper` is installed and model downloaded
3. **Audio not detected** — Check audio format and encoding

### TTS Issues

1. **TTS no audio** — Check voice model files (`.onnx` + `.onnx.json`) exist
2. **Voice not found** — Verify voice model was downloaded correctly
3. **Server won't start** — Check if port is already in use

### GPU Acceleration

```bash
# For faster-whisper with CUDA
python -m faster_whisper.transcribe --model base --device cuda

# Requires CUDA 12+ and compatible GPU
```

## Limitations

- **Piper is TTS ONLY** — Does NOT do speech-to-text (need faster-whisper)
- **Voice models must stay together** — Piper stores `.onnx` weights and `.onnx.json` config in same directory
- **Language mismatch** — STT language must match TTS voice language (or use translation)
- **Model loading is one-time** — Load HTTP server once and reuse it
- **OpenAI API compatibility** — Speaches exposes compatible endpoints but may have rate limits

## Troubleshooting for Agents

### Skill Not Loading

If `skill_view(name="local-voice-services")` fails:

1. **Check skill exists**:
   ```python
   from hermes_tools import skills_list
   skills = skills_list()
   print([s for s in skills if "voice" in s["name"].lower()])
   ```

2. **Verify skill directory**:
   ```bash
   ls -la /opt/data/skills/devops/local-voice-services/
   ```

3. **Check SKILL.md validity**:
   ```bash
   head -20 /opt/data/skills/devops/local-voice-services/SKILL.md
   ```

### Services Not Starting

1. **Check if ports are in use**:
   ```bash
   # Port 10300 (faster-whisper)
   netstat -tlnp | grep 10300
   
   # Port 5002 (Piper)
   netstat -tlnp | grep 5002
   ```

2. **Check Docker containers**:
   ```bash
   docker ps | grep whisper
   docker ps | grep piper
   ```

3. **Check logs**:
   ```bash
   docker logs faster-whisper
   docker logs piper-tts
   ```

### Model Issues

1. **Verify model files exist**:
   ```bash
   # For faster-whisper
   ls -la /models/base/
   
   # For Piper
   ls -la /models/en_US-lessac-medium/
   ```

2. **Redownload models if needed**:
   ```bash
   # Piper voices
   python -m piper.download_voices en_US-lessac-medium
   
   # faster-whisper models
   wget https://huggingface.co/SYSTRAI/faster-whisper-base/resolve/main/model.bin
   ```

### Hermes Integration Issues

1. **Check config.yaml**:
   ```bash
   cat ~/.hermes/config.yaml
   ```

2. **Verify STT/TTS sections exist**:
   ```yaml
   stt:
     enabled: true
     provider: local
     local:
       model: base
   
   tts:
     provider: piper
   ```

3. **Restart Hermes**:
   ```bash
   # Check gateway state
   cat /opt/data/state/gateway.lifecycle.json
   ```

## Dependencies

### Python Packages

```bash
pip install faster-whisper
pip install piper-tts piper-tts[http]
pip install speaches  # Optional, for unified API
```

### Docker Images

- `lscr.io/linuxserver/faster-whisper:latest` — STT service
- `ghcr.io/rhasspy/piper:latest` — TTS service
- `ghcr.io/speaches-ai/speaches:latest` — Unified API (optional)

### System Requirements

- **RAM**: 
  - tiny: ~100MB
  - base: ~200MB
  - small: ~500MB
  - medium: ~1.5GB
  - large-v3: ~4GB
  
- **GPU** (optional but recommended): CUDA 12+ for faster-whisper

## References

- **faster-whisper**: https://github.com/SYSTRAN/faster-whisper
- **Piper**: https://github.com/rhasspy/piper
- **Speaches**: https://github.com/speaches-ai/speaches
- **Docker faster-whisper**: https://hub.docker.com/r/linuxserver/faster-whisper
- **Piper Docker**: https://github.com/rhasspy/piper-docker

## Skill Relationship

### How local-voice-services Relates to hermes-agent

| Feature | hermes-agent | local-voice-services |
|---------|-------------|---------------------|
| **Focus** | General Hermes configuration | Voice-specific STT/TTS setup |
| **STT** | Uses configured provider | Provides faster-whisper/Piper options |
| **TTS** | Uses configured provider | Provides Piper implementation |
| **Slash Commands** | General commands | `/voice on`, `/voice tts`, `/voice off` |
| **Best for** | Overall Hermes setup | Voice workflows, voice assistants |

**Why two skills?**
- `hermes-agent` is for **general Hermes configuration**
- `local-voice-services` is for **voice-specific setup** with STT/TTS

Both work together to enable voice-controlled Hermes interactions.

## Repository Structure

```
hermes-agent-skills-notes/
├── docs/
│   └── skills/
│       ├── local-voice-services.md  ← This documentation
│       └── local-voice-services-quick.md  ← Quick reference
└── skills/
    └── devops/
        └── local-voice-services/
            └── SKILL.md                    ← Skill definition
```

## Quick Reference

| Command | Description |
|---------|-------------|
| `faster-whisper` | Speech-to-text (STT) |
| `piper-tts` | Text-to-speech (TTS) |
| `speaches` | Unified STT+TTS API |
| `/voice on` | Enable voice features |
| `/voice tts` | Use TTS for responses |
| `/voice off` | Disable voice features |

## Author

**Version**: 1.0.0  
**Created**: Hermes Agent  
**License**: MIT

---

**Note**: This skill was created to enable local voice processing without external API costs. Always prefer local processing for privacy and cost savings.
