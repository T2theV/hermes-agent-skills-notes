---
name: youtube-accurate-summaries
description: "Accurate YouTube summaries using real transcripts."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [YouTube, Video, Transcripts, Accurate Summary]
    related_skills: [youtube-content]
---

# YouTube Accurate Video Summaries

## When to use

Use when you need **accurate, reliable YouTube video summaries** based on actual transcripts fetched via `youtube-transcript-api`. This skill ensures summaries are based on REAL video content, not web search guesses or fabricated information.

**Key differentiator**: Unlike generic web searches or AI hallucinations, this skill fetches the actual YouTube transcript and creates summaries from verified content.

## Setup

### Primary: Local yt-dlp Gateway

**Self-hosted transcription service** based on yt-dlp. This is the PRIMARY, most reliable method.

**Gateway socket**: `/opt/data/gateway.sock` (port 8642)

**Check status**:
```bash
# Check if gateway is running
ls -la /opt/data/gateway.sock

# Check logs
tail -20 /opt/data/logs/gateway.log

# Check state
cat /opt/data/gateway_state.json
```

### Fallback: Python Dependencies

When gateway unavailable:

```bash
uv pip install youtube-transcript-api
```

### Third-Party APIs (no installation required)

- **youtube-transcript.io**: `https://youtube-transcript.io/api/transcript`
- **serapi.com**: Web playground interface

**Script location**: `SKILL_DIR/scripts/fetch_transcript.py`

## Workflow

### 1. Extract Video ID

Accept any YouTube URL format:
- `youtube.com/watch?v=VIDEO_ID`
- `youtu.be/VIDEO_ID`
- `youtube.com/shorts/VIDEO_ID`
- `youtube.com/embed/VIDEO_ID`

### 2. Fetch Transcript

**Primary Method: Local yt-dlp Gateway (Recommended)**

Uses a local transcription gateway based on yt-dlp for reliable transcript extraction.

```bash
# Via the gateway API
curl -X POST http://localhost:8642/api/transcript \
  -H "Content-Type: application/json" \
  -d '{"url": "https://youtu.be/VIDEO_ID", "lang": "en"}'

# Or directly with yt-dlp if gateway unavailable
uv run python SKILL_DIR/scripts/fetch_transcript.py <URL>
```

**Fallback Methods** (when gateway unavailable):
1. **youtube-transcript-api**: Python library (may fail on videos without captions)
2. **youtube-transcript.io**: Third-party API (rate-limited, requires internet)
3. **yt-dlp --print transcript**: Limited transcript extraction

**Options**:
- `--language en` - Specify language (e.g., `--language en,es`)
- `--timestamps` - Include timestamps in output
- `--text-only` - Output plain text instead of JSON

### 3. Create Structured Summary

From the fetched transcript, create summaries with these sections:

**📌 VIDEO OVERVIEW**
- Video ID, title, uploader, duration
- Core topic/message

**🔑 KEY POINTS**
- Numbered list of main ideas
- Bullet points for details

**💡 SAMPLE TRANSCRIPT**
- Include 2-3 representative quotes with context
- Shows the summary is based on real content

**✅ BOTTOM LINE**
- One-sentence takeaway
- Practical implications

## Output Format

```
📺 VIDEO SUMMARY
====================================================

📌 **VIDEO INFORMATION**
----------------------------------------------------
[Title, Duration, Uploader]

🔑 **KEY POINTS**
----------------------------------------------------
1. **Point One**
   - Detail
2. **Point Two**
   - Detail
...

💡 **SAMPLE TRANSCRIPT**
----------------------------------------------------
"[Actual quote from video]..."

✅ **BOTTOM LINE**
----------------------------------------------------
[One-sentence takeaway]
```

## Best Practices

✅ **ALWAYS verify the summary against the actual transcript**
✅ **Include video metadata** (ID, title, duration)
✅ **Quote directly from transcript** to prove accuracy
✅ **Structure with clear sections** for readability
✅ **Avoid speculation** - only summarize what's in the transcript

❌ **NEVER guess or hallucinate content**
❌ **NEVER rely on web search alone** (can be wrong/outdated)
❌ **NEVER assume video topic** without checking transcript

## Error Handling

### Gateway Issues

1. **Gateway socket missing**: Check if it's running
   ```bash
   ls -la /opt/data/gateway.sock
   tail -30 /opt/data/logs/gateway.log
   ```

2. **Gateway stopped**: Check state and lifecycle
   ```bash
   cat /opt/data/gateway_state.json
   cat /opt/data/state/gateway.lifecycle.json
   ```

3. **Transcript fetch fails**: Check video has captions
   - Go to video page → Check if captions are available
   - Some videos don't have auto-captions enabled

### Transcript Issues

- **No transcript found**: Video may not have auto-captions enabled
- **Transcript disabled**: Check video page for manual captions
- **Language mismatch**: Try `--language en,es,fr` fallback chain
- **API timeout**: Retry or try alternative API source

### Fallback Strategy

If all methods fail, return metadata only (title, channel, duration) and note transcript unavailability.

## Example Usage

```bash
# Basic summary
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py "https://youtu.be/VIDEO_ID"

# With English language specified
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py "https://youtu.be/VIDEO_ID" --language en

# Plain text output for quick reading
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py "https://youtu.be/VIDEO_ID" --text-only

# With timestamps
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py "https://youtu.be/VIDEO_ID" --timestamps
```

## Common Pitfalls

- **Assuming video topic**: Always check transcript first
- **Web search confusion**: Search results can be outdated or wrong
- **Language assumptions**: Specify language if not English
- **Short videos**: Even 5-min videos need transcript check

## Related Skills

- `youtube-content`: General YouTube transcript tool (same underlying script)
- `youtube-content/references/youtube-api-reference.md`: Comprehensive API reference
- `blocked-page-recovery`: For videos with access restrictions
- `hermes-agent`: For troubleshooting script issues

---

**Author Note**: This skill was created after discovering that web search often provides inaccurate or outdated video summaries. Always use the actual transcript for accuracy.
