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

## How to Use This Skill as an Agent

### Step 1: Load the Skill Documentation

Before using this skill, load the full skill definition:

```python
from hermes_tools import skill_view

# Load the skill
skill = skill_view(name="youtube-accurate-summaries")
```

This will give you access to:
- The full skill specification
- Available tools and commands
- Error handling procedures
- Best practices

### Step 2: Use in Your Workflow

When you need accurate YouTube summaries, you have two options:

**Option A: Direct Script Execution (Recommended)**

```bash
# Fetch and summarize the transcript
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL>

# With options
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py \
  "https://youtu.be/VIDEO_ID" \
  --language en \
  --timestamps
```

**Option B: Through Hermes Skill System**

```python
# Load the skill context
from hermes_tools import skill_view

skill = skill_view(name="youtube-accurate-summaries")

# The skill will guide you through:
# 1. Video URL extraction
# 2. Transcript fetching
# 3. Summary generation
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
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py \
  "https://youtu.be/VIDEO_ID" \
  --language en
```

### Plain Text Output

```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py \
  "https://youtu.be/VIDEO_ID" \
  --text-only
```

### With Timestamps

```bash
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py \
  "https://youtu.be/VIDEO_ID" \
  --timestamps
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

## Skill Relationship

### How youtube-accurate-summaries Relates to youtube-content

These are **two skills that share the same underlying script**:

| Feature | youtube-content | youtube-accurate-summaries |
|---------|----------------|---------------------------|
| **Focus** | Multiple output formats (summary, thread, blog) | Accurate transcript-based summaries only |
| **Output** | Flexible (summary, chapters, quotes, thread, blog) | Structured: Overview, Key Points, Sample Transcript, Bottom Line |
| **Script** | ✅ Uses fetch_transcript.py | ✅ Uses SAME fetch_transcript.py |
| **Best for** | Content repurposing, social media | Research, learning, fact-based summaries |

**Why two skills?**
- `youtube-content` is for **content creators** who want to repurpose videos
- `youtube-accurate-summaries` is for **researchers** who need factual accuracy

Both use the same `fetch_transcript.py` script to avoid code duplication.

## Repository Structure

```
hermes-agent-skills-notes/
├── docs/
│   └── skills/
│       ├── youtube-accurate-summaries.md  ← This documentation
│       └── youtube-accurate-summaries-quick.md  ← Quick reference
└── skills/
    └── media/
        ├── youtube-content/
        │   ├── SKILL.md                    ← Skill definition
        │   └── scripts/
        │       └── fetch_transcript.py     ← Shared transcript fetcher
        └── youtube-accurate-summaries/
            └── SKILL.md                    ← Reuses youtube-content script
```

## Quick Reference

The `fetch_transcript.py` script is shared with `youtube-content` skill.

| Command | Description |
|---------|-------------|
| `fetch_transcript.py <URL>` | Basic transcript fetch |
| `--language en` | Specify English |
| `--timestamps` | Include timestamps |
| `--text-only` | Plain text output |

## Troubleshooting for Agents

### Skill Not Loading

If `skill_view(name="youtube-accurate-summaries")` fails:

1. **Check skill exists**:
   ```python
   from hermes_tools import skills_list
   skills = skills_list()
   print([s for s in skills if "youtube" in s["name"].lower()])
   ```

2. **Verify skill directory**:
   ```bash
   ls -la /opt/data/skills/media/youtube-accurate-summaries/
   ```

3. **Check SKILL.md validity**:
   ```bash
   head -20 /opt/data/skills/media/youtube-accurate-summaries/SKILL.md
   ```

### Transcript Fetching Fails

If the transcript fetch fails:

1. **Check video has captions**:
   - Go to video page → Check if captions are available
   - Some videos don't have auto-captions enabled

2. **Try alternative API**:
   ```bash
   uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py \
     <URL> \
     --language en,es,fr
   ```

3. **Manual transcript**:
   - Check if video has manual captions
   - Copy from YouTube page manually

### Dependency Issues

If `youtube-transcript-api` is missing:

```bash
uv pip install youtube-transcript-api
```

Or check if it's available:
```bash
uv pip show youtube-transcript-api
```

## Author

**Version**: 1.0.0  
**Created**: Hermes Agent  
**License**: MIT

---

**Note**: This skill was created after discovering that web search often provides inaccurate or outdated video summaries. Always use the actual transcript for accuracy.