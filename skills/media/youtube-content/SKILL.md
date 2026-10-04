---
name: youtube-content
description: "YouTube transcripts to summaries, threads, blogs."
version: 1.0.0
author: Teknium (teknium1), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [YouTube, Video, Transcripts, Media]
    related_skills: []
---

# YouTube Content Tool

## When to use

Use when the user shares a YouTube URL or video link, asks to summarize a video, requests a transcript, or wants to extract and reformat content from any YouTube video. Transforms transcripts into structured content (chapters, summaries, threads, blog posts).

Extract transcripts from YouTube videos and convert them into useful formats.

## Setup

Primary dependency: `youtube-transcript-api` via `uv pip install`.

**FALLBACK WORKFLOW** (when primary dependency unavailable):
1. Create isolated venv: `cd /tmp && python3 -m venv yt-dlp-env && source yt-dlp-env/bin/activate && pip install yt-dlp`
2. Use yt-dlp for metadata: `yt-dlp --dump-json VIDEO_ID`
3. yt-dlp `--transcript` flag requires EJS (JavaScript runtime); use `--print transcript` with appropriate flags instead.
4. Document limitation: yt-dlp may fail without JS runtime; this is a known constraint, not a workflow failure.

**Third-party APIs** (no dependency installation required):
- `youtube-transcript.io`: GET `/api/transcript?url=URL&format=json` — see `references/youtube-api-reference.md`
- `serapi.com`: playground interface, no key required — see `references/youtube-api-reference.md`
- Both may have rate limits; document in error handling.

**URL normalization**: Always extract the 11-character video ID first. Accept: `youtube.com/watch?v=ID`, `youtu.be/ID`, `youtube.com/shorts/ID`, `youtube.com/embed/ID`.

## Helper Script

`SKILL_DIR` is the directory containing this SKILL.md file. The script accepts any standard YouTube URL format, short links (youtu.be), shorts, embeds, live links, or a raw 11-character video ID.

```bash
# JSON output with metadata
uv run python SKILL_DIR/scripts/fetch_transcript.py "https://youtube.com/watch?v=VIDEO_ID"

# Plain text (good for piping into further processing)
uv run python SKILL_DIR/scripts/fetch_transcript.py "URL" --text-only

# With timestamps
uv run python SKILL_DIR/scripts/fetch_transcript.py "URL" --timestamps

# Specific language with fallback chain
uv run python SKILL_DIR/scripts/fetch_transcript.py "URL" --language tr,en
```

## Output Formats

After fetching the transcript, format it based on what the user asks for:

- **Chapters**: Group by topic shifts, output timestamped chapter list
- **Summary**: Concise 5-10 sentence overview of the entire video
- **Chapter summaries**: Chapters with a short paragraph summary for each
- **Thread**: Twitter/X thread format — numbered posts, each under 280 chars
- **Blog post**: Full article with title, sections, and key takeaways
- **Quotes**: Notable quotes with timestamps

### Example — Chapters Output

```
00:00 Introduction — host opens with the problem statement
03:45 Background — prior work and why existing solutions fall short
12:20 Core method — walkthrough of the proposed approach
24:10 Results — benchmark comparisons and key takeaways
31:55 Q&A — audience questions on scalability and next steps
```

### Example — Structured Summary Output

Use easy-to-read bullet points with clear sections:

**📖 The Story (Key Events)**
- What was the situation/context?
- What happened (chronological key events)?
- What were the outcomes?

**🎯 Main Conclusions**
- Core problem or insight
- Key lessons (use table or bullet format)
- Bottom line or takeaway
- Any questions raised or recommendations

**Format tips:**
- Use emoji headers for visual scanning (📖, 🎯, ⚠️, 💡, 📊)
- Group related points under clear subheaders
- Use tables for comparisons or lessons
- End with a "Bottom Line" or "Final Takeaway"
- Keep bullets concise (1-2 sentences max)

## Workflow

1. **Extract video ID**: Parse the 11-char ID from any URL format. If extraction fails, return the error to the user.
2. **Fetch metadata**: Use yt-dlp `--dump-json VIDEO_ID` to get title, channel, duration, upload date. This works even without transcripts.
3. **Fetch transcript**: Try `youtube-transcript-api` first. If unavailable or fails:
   - Try third-party API (`youtube-transcript.io` or `serapi.com`)
   - Fall back to yt-dlp `--print transcript` (not `--transcript` flag)
4. **Validate output**: Confirm non-empty and matches expected language. If empty:
   - Check if video has subtitles at all (metadata shows `subtitles` key)
   - Inform the user that transcripts are unavailable
5. **Process transcript**: If >50K characters, split into overlapping chunks (~40K with 2K overlap), summarize each, then merge.
6. **Transform** into the requested output format. If unspecified, default to a summary.
7. **Verify**: Re-read the transformed output for coherence, correct timestamps, and completeness before presenting.

## Error Handling

- **Transcript disabled**: Metadata shows `subtitles: {}` (empty) or no subtitles key. Tell the user; suggest checking video page for manual captions.
- **No transcript available**: Metadata shows subtitles key but no content. Return title/metadata still; inform user transcripts don't exist (auto-captions may not have been generated).
- **Private/unavailable video**: yt-dlp returns `UNPLAYABLE` or `LOGIN_REQUIRED`. Relay error and ask user to verify URL.
- **Dependency missing**: Primary (`youtube-transcript-api`) unavailable → fall back to third-party APIs. If all fail, return metadata only (title, channel, duration) and note transcript unavailability.
- **Shorts/embedded URLs**: Extract video ID first, retry with raw ID. Some formats fail when fetched as full URLs.
- **Non-English content**: Auto-generated captions may not exist. Specify language fallback chain (e.g., `--language en,hi`) to try English first, then Hindi, then whatever exists. Document that Hindi content often has no auto-captions.
- **yt-dlp without EJS**: The `--transcript` flag requires JavaScript runtime. Use `--print transcript` instead or document this as a known limitation requiring manual EJS setup.
- **No subtitles in metadata**: Video has no caption track at all. Return what metadata is available; this is not an error, just a limitation.
