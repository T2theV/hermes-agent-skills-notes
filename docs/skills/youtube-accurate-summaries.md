# YouTube Accurate Summaries Skill

## Overview

The **youtube-accurate-summaries** skill provides accurate summaries of YouTube videos by fetching and analyzing actual video transcripts using the `youtube-transcript-api`.

**Key Benefit**: Unlike generic web searches or AI hallucinations, this skill fetches REAL video transcripts and creates summaries from verified content.

## Location

```
/opt/data/skills/media/youtube-accurate-summaries/SKILL.md
```

## Repository Location

```
docs/skills/youtube-accurate-summaries.md
```

## Purpose

This skill is used when you need **accurate, reliable YouTube video summaries** based on actual transcripts. It's especially useful for:

- **Research**: Getting factual information from educational videos
- **Learning**: Understanding complex technical content
- **Content Creation**: Creating accurate blog posts or summaries
- **Decision Making**: Getting reliable information quickly

## How It Works

### 1. Video ID Extraction

The skill accepts any YouTube URL format:
- `youtube.com/watch?v=VIDEO_ID`
- `youtu.be/VIDEO_ID`
- `youtube.com/shorts/VIDEO_ID`
- `youtube.com/embed/VIDEO_ID`

### 2. Transcript Fetching

Uses `youtube-transcript-api` to fetch the actual video transcript.

**Script Location**: `/opt/data/skills/media/youtube-content/scripts/fetch_transcript.py`

**Command**:
```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL>
```

### 3. Structured Summary Creation

The skill creates summaries with these sections:

**📌 VIDEO OVERVIEW**
- Video ID, title, uploader, duration
- Core topic/message

**🔑 KEY POINTS**
- Numbered list of main ideas
- Bullet points for details

**💡 SAMPLE TRANSCRIPT**
- 2-3 representative quotes with context
- Proves summary is based on real content

**✅ BOTTOM LINE**
- One-sentence takeaway
- Practical implications

## Output Format Example

```
📺 VIDEO SUMMARY
====================================================

📌 **VIDEO INFORMATION**
----------------------------------------------------
[Title, Duration, Uploader]

🔑 **KEY POINTS**
----------------------------------------------------
1. **Main Point One**
   - Supporting detail
2. **Main Point Two**
   - Supporting detail

💡 **SAMPLE TRANSCRIPT**
----------------------------------------------------
"[Actual quote from video]..."

✅ **BOTTOM LINE**
----------------------------------------------------
[One-sentence takeaway]
```

## Usage Examples

### Basic Usage

```python
# Simple YouTube video summary
url = "https://youtu.be/VIDEO_ID"
summary = youtube_accurate_summaries(url)
```

### With Language Specification

```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py   "https://youtu.be/VIDEO_ID"   --language en
```

### Plain Text Output

```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py   "https://youtu.be/VIDEO_ID"   --text-only
```

### With Timestamps

```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py   "https://youtu.be/VIDEO_ID"   --timestamps
```

## Dependencies

- **youtube-transcript-api**: Python library for fetching YouTube transcripts
- **uv**: Python package manager

### Installation

```bash
uv pip install youtube-transcript-api
```

## Best Practices

✅ **DO**:
- Always verify summary against actual transcript
- Include video metadata (ID, title, duration)
- Quote directly from transcript to prove accuracy
- Structure with clear sections for readability

❌ **DON'T**:
- Guess or hallucinate content
- Rely on web search alone (can be wrong/outdated)
- Assume video topic without checking transcript
- Assume language if not English

## Error Handling

The skill handles common issues:

- **No transcript found**: Video may not have auto-captions enabled
- **Transcript disabled**: Check video page for manual captions
- **Language mismatch**: Try fallback chain (`--language en,es,fr`)
- **API timeout**: Retry or try alternative API source

## Limitations

- Video must have captions/transcript enabled
- Some videos don't provide transcripts
- Language detection may need manual specification
- Private or age-restricted videos may not work

## Related Skills

- **youtube-content**: General YouTube transcript tool (same underlying script)
- **hermes-agent**: For troubleshooting script issues
- **grounded-citations**: For adding citations to summaries

## Repository Structure

```
hermes-agent-skills-notes/
├── docs/
│   └── skills/
│       └── youtube-accurate-summaries.md  ← This file
└── skills/
    └── media/
        └── youtube-accurate-summaries/
            ├── SKILL.md                    ← Main skill documentation
            └── scripts/
                └── fetch_transcript.py     ← Transcript fetcher
```

## Quick Reference

| Command | Description |
|---------|-------------|
| `fetch_transcript.py <URL>` | Basic transcript fetch |
| `--language en` | Specify English |
| `--timestamps` | Include timestamps |
| `--text-only` | Plain text output |

## Author

**Version**: 1.0.0  
**Created**: Hermes Agent  
**License**: MIT

---

**Note**: This skill was created after discovering that web search often provides inaccurate or outdated video summaries. Always use the actual transcript for accuracy.
