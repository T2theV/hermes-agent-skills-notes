# YouTube Accurate Summaries - Quick Reference

## What It Does

Fetches actual YouTube transcripts and creates accurate summaries based on real video content.

## When to Use

- Need factual information from YouTube videos
- Creating research summaries
- Want to avoid AI hallucinations
- Need reliable source verification

## Quick Commands

```bash
# Basic summary
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL>

# With English language
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL> --language en

# Plain text output
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL> --text-only

# With timestamps
uv run python /opt/data/skills/media/youtube-content/scripts/fetch_transcript.py <URL> --timestamps
```

## Output Structure

1. **Video Information** - Title, duration, uploader
2. **Key Points** - Main ideas from the video
3. **Sample Transcript** - Direct quotes proving accuracy
4. **Bottom Line** - One-sentence takeaway

## Dependencies

```bash
uv pip install youtube-transcript-api
```

## Example

```
📺 VIDEO SUMMARY
====================================================

📌 **VIDEO INFORMATION**
----------------------------------------------------
"Building LLMs from Scratch" by Andrej Karpathy
Duration: 45:32

🔑 **KEY POINTS**
----------------------------------------------------
1. **Understanding Attention Mechanisms**
   - Self-attention computes relationships between tokens
   - Allows parallel processing of sequences
   - Key component of modern LLMs
2. **Transformer Architecture**
   - Encoder-decoder structure
   - Positional encoding for sequence order
   - Layer normalization for stability

💡 **SAMPLE TRANSCRIPT**
----------------------------------------------------
"The attention mechanism is what makes transformers so powerful. 
It allows the model to focus on different parts of the input 
sequence simultaneously, which is crucial for understanding 
long-range dependencies in language."

✅ **BOTTOM LINE**
----------------------------------------------------
Transformers use self-attention mechanisms to process sequences 
in parallel, enabling efficient and accurate language understanding.
```

## Limitations

- Video must have captions enabled
- Some videos don't provide transcripts
- Language must be supported by API

## Related

- Full documentation: `docs/skills/youtube-accurate-summaries.md`
- Skill location: `skills/media/youtube-accurate-summaries/SKILL.md`
