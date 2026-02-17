# claude-code-process-video

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that extracts screenshots and audio transcription from video files, allowing Claude to analyze video content without watching it.

## What it does

When you provide a video file, this skill will:

1. **Extract screenshots** at smart intervals based on video duration
2. **Extract audio** and transcribe it (if a transcription tool is available)
3. **Analyze each frame** visually using Claude's multimodal capabilities
4. **Compile a timeline summary** with key findings

## Prerequisites

- **ffmpeg** - required for video processing (`brew install ffmpeg` on macOS)
- **whisper** (optional) - for audio transcription (`pip install openai-whisper` or `pip install mlx-whisper` for Apple Silicon)

## Installation

### As a personal skill (available in all projects)

```bash
# Clone to a temporary location
git clone https://github.com/YOUR_USERNAME/claude-code-process-video /tmp/claude-code-process-video

# Copy the skill to your personal skills directory
cp -r /tmp/claude-code-process-video/skills/process-video ~/.claude/skills/
```

### As a project skill (available in one project)

```bash
# From your project root
cp -r /path/to/claude-code-process-video/skills/process-video .claude/skills/
```

## Usage

In Claude Code, run:

```
/process-video /path/to/your/video.mp4
```

Or simply mention a video file and Claude will use this skill automatically.

## Screenshot interval logic

| Video duration | Screenshot interval |
|---|---|
| Under 1 minute | Every 5 seconds |
| 1-5 minutes | Every 10 seconds |
| 5-15 minutes | Every 15 seconds |
| Over 15 minutes | Every 30 seconds |

## Example output

The skill produces a structured summary including:

- **Video Overview** - file metadata (duration, resolution, codecs)
- **Timeline** - chronological description of what appears on screen
- **Key Findings** - important information, errors, actions, and data visible in the video
- **Transcription** - audio transcription (when available)

## License

MIT
