---
name: process-video
description: Extract screenshots and audio transcription from a video file. Use when the user provides a video file (mp4, mov, webm, etc.) and wants to understand its contents without watching it.
user-invocable: true
argument-hint: <path-to-video>
---

This skill processes a video file to extract visual and audio information for analysis.

The user provides a path to a video file. Extract screenshots at regular intervals and attempt audio transcription so the contents can be understood without watching the video.

## Step 1: Validate Input

Check that the video file exists at the provided path. If no path is given, search the current working directory for common video files (*.mp4, *.mov, *.webm, *.avi, *.mkv).

## Step 2: Probe Video Metadata

Use ffprobe to get video details:

```bash
ffprobe -v quiet -print_format json -show_format -show_streams <video_path>
```

Extract and report:
- Duration
- Resolution (width x height)
- Has audio track (yes/no)
- Codec info

## Step 3: Create Output Directory

Create a temporary directory for extracted frames and audio:

```bash
mkdir -p /tmp/video-processing-<timestamp>
```

## Step 4: Extract Screenshots

Calculate an appropriate interval based on video duration:
- Under 1 minute: every 5 seconds
- 1-5 minutes: every 10 seconds
- 5-15 minutes: every 15 seconds
- Over 15 minutes: every 30 seconds

Extract frames as JPG images:

```bash
ffmpeg -i <video_path> -vf "fps=1/<interval>" -q:v 2 <output_dir>/frame_%03d.jpg
```

## Step 5: Extract and Transcribe Audio (if available)

If the video has an audio track:

1. Extract audio as WAV:
```bash
ffmpeg -i <video_path> -vn -acodec pcm_s16le -ar 16000 -ac 1 <output_dir>/audio.wav
```

2. Attempt transcription using available tools (in priority order):
   - `whisper` (OpenAI Whisper CLI)
   - `mlx_whisper` (Apple Silicon optimized)
   - Any other speech-to-text tool found via `which`

3. If no transcription tool is available, inform the user and suggest installing one:
   ```
   pip install openai-whisper
   # or for Apple Silicon:
   pip install mlx-whisper
   ```

## Step 6: Read and Analyze Screenshots

Read ALL extracted frame images using the Read tool. Analyze each frame to understand:
- What application/website/screen is shown
- Key text visible on screen
- UI elements and their state
- Any error messages or notable content
- Changes between consecutive frames

## Step 7: Compile Summary

Present a comprehensive summary with:

### Video Overview
- File name, duration, resolution

### Timeline
Walk through the video chronologically, describing what happens at each screenshot interval. Group consecutive similar frames together. Format as:

**[MM:SS - MM:SS]** Description of what's happening on screen

### Key Findings
- Important information visible in the video
- Error messages or issues shown
- Actions performed by the user
- Any notable data or configurations

### Transcription (if available)
Include the full or summarized audio transcription.

## Important Notes

- If ffmpeg is not installed, inform the user and stop
- Always clean up by telling the user where the extracted files are stored
- For very long videos (over 30 minutes), ask the user if they want full processing or just key sections
- When reading frames, batch them in parallel where possible for efficiency
- Focus on extracting actionable information, not just describing pixels
