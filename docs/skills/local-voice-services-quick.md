# Local Voice Services - Quick Reference

## What It Does

Sets up local **speech-to-text (STT)** and **text-to-speech (TTS)** using faster-whisper and Piper. No external API costs, full privacy.

## When to Use

- Need voice assistants locally
- Want privacy (no external APIs)
- Building voice workflows with Hermes
- Low-latency audio processing

## How to Use as an Agent

### Step 1: Load the Skill

```python
from hermes_tools import skill_view
skill = skill_view(name="local-voice-services")
```

### Step 2: Choose Deployment Method

**Option A: Separate Services (Recommended)**
- STT: faster-whisper (port 10300)
- TTS: Piper HTTP (port 5002)

**Option B: Unified API (Speaches)**
- Single service, both STT and TTS

**Option C: Hermes Integration**
- Configure in `~/.hermes/config.yaml`

## Quick Commands

```bash
# Check if services are running
curl http://localhost:10300/health  # faster-whisper
curl http://localhost:5002/         # Piper

# Test STT
curl -X POST http://localhost:10300/stt   -H 'Content-Type: application/json'   -d '{"audio": "base64_encoded", "language": "en"}'

# Test TTS
curl -X POST http://localhost:5002/tts   -H 'Content-Type: application/json'   -d '{"text": "Hello", "voice": "en_US-lessac-medium"}'

# Enable voice features in Hermes
/voice on
/voice tts
/voice off
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

| Voice | Language | Size |
|-------|----------|------|
| en_US-lessac-medium | English (US) | ~150MB |
| en_GB-stereotypical-female-medium | English (UK) | ~150MB |
| de_DE-thorsten-medium | German | ~150MB |

## Docker Setup

```yaml
version: '3.8'
services:
  faster-whisper:
    image: lscr.io/linuxserver/faster-whisper:latest
    environment:
      - WHISPER_MODEL=base
    ports:
      - "10300:10300"
    restart: unless-stopped

  piper-tts:
    image: ghcr.io/rhasspy/piper:latest
    environment:
      - VOICE=en_US-lessac-medium
    ports:
      - "5002:5002"
    restart: unless-stopped
```

## Hermes Configuration

```yaml
# ~/.hermes/config.yaml
stt:
  enabled: true
  provider: local
  local:
    model: base

tts:
  provider: piper
```

## Testing

```bash
# Test STT
python -m faster_whisper.transcribe --model base audio.wav

# Test TTS
python -m piper -m en_US-lessac-medium -f output.wav -- "Hello world"

# Test unified API (Speaches)
curl -X POST http://localhost:11435/v1/audio/speech   -H 'Content-Type: application/json'   -d '{"model": "en_US-lessac-medium", "input": "Hello"}'
```

## Troubleshooting

### Services Not Starting

```bash
# Check ports
netstat -tlnp | grep -E "10300|5002"

# Check Docker
docker ps | grep -E "whisper|piper"

# Check logs
docker logs faster-whisper
docker logs piper-tts
```

### Model Issues

```bash
# Verify model files
ls -la /models/base/
ls -la /models/en_US-lessac-medium/

# Redownload if needed
python -m piper.download_voices en_US-lessac-medium
```

### Hermes Integration

```bash
# Check config
cat ~/.hermes/config.yaml

# Verify STT/TTS sections
grep -A 5 "stt:" ~/.hermes/config.yaml
grep -A 5 "tts:" ~/.hermes/config.yaml
```

## Best Practices

✅ **DO**:
- Use `base` or `small` models for balance
- Download voice models once, reuse HTTP server
- Configure in `~/.hermes/config.yaml`
- Test with short clips first
- Use int8 quantized models for faster loading

❌ **DON'T**:
- Don't call Piper CLI in loops (load server once)
- Don't forget to download voice models
- Don't mismatch STT and TTS languages
- Don't assume all voices support all languages

## Limitations

- **Piper is TTS ONLY** — Need faster-whisper for STT
- **Voice models must stay together** — `.onnx` + `.onnx.json` in same directory
- **Language mismatch** — STT language must match TTS voice language
- **Model loading is one-time** — Load HTTP server once and reuse

## Dependencies

```bash
# Python packages
pip install faster-whisper
pip install piper-tts piper-tts[http]
pip install speaches  # Optional

# Docker images
lscr.io/linuxserver/faster-whisper:latest
ghcr.io/rhasspy/piper:latest
ghcr.io/speaches-ai/speaches:latest  # Optional
```

## Slash Commands

| Command | Description |
|---------|-------------|
| `/voice on` | Enable voice-to-voice messaging |
| `/voice tts` | Use text-to-speech for responses |
| `/voice off` | Disable all voice features |

## Repository Location

- Full docs: `docs/skills/local-voice-services.md`
- Quick ref: `docs/skills/local-voice-services-quick.md`
- Skill source: `skills/devops/local-voice-services/SKILL.md`

## Related Skills

- **hermes-agent**: General Hermes configuration
- **home-assistant**: Voice control for smart home devices

## Author

**Version**: 1.0.0  
**Created**: Hermes Agent  
**License**: MIT
