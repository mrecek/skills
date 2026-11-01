---
name: yt-transcript
description: Format YouTube transcripts into markdown artifacts using yt-dlp with author chapters and resilient fallback providers
---

You are using the **YouTube Transcript (yt-dlp) Formatting** skill.

Goal: produce a clean markdown transcript artifact in the established `youtube-transcripts` format.

## When To Use This Skill

Invoke this skill when the user requests:
- `/yt-transcript <URL>`
- `/yt-transcript <URL> output to <directory>`
- `/yt-transcript <URL> --no-chapters`
- Any request to generate or update a YouTube transcript artifact

## Core Contract

The script is the deterministic engine. The agent orchestrates:
- path selection
- explicit filename override requests (only when user asks)
- user confirmation
- result communication

The script handles extraction, fallback logic, formatting, and file writes.
The script also auto-generates canonical filenames when given a directory target.

## Runtime Requirements

Requires [`uv`](https://docs.astral.sh/uv/). The script declares its own Python dependencies (`yt-dlp[default]`, `youtube-transcript-api`) in its header, so there is no install step: `uv` builds a private environment on the first run.

Always run it with `--upgrade-package yt-dlp`, which moves yt-dlp to its latest release on every run (about half a second). YouTube changes routinely break older yt-dlp releases, so the latest release is the only safe one.

The Whisper ASR fallback (`--asr-fallback`) also needs `--with openai-whisper` on the `uv` command and `ffmpeg` on `PATH`.

## Workflow

### Step 1: Determine Output Path

- Parse the user request for output directory or filename hints.
- If no directory is specified, propose a `youtube-transcripts/` directory in the current workspace context.
- Build an absolute output path.

### Step 2: Let Script Generate Filename

Default behavior: pass an output directory and let the script generate:
`YYYY-MM-DD_identifier_topic-keywords.md`

Only pass an explicit `.md` file path when the user explicitly requests a custom filename.

### Step 3: Decide Extraction Flags

Default invocation is English subtitle-first.

Available flags:
- `--no-chapters`: force flat transcript body
- `--lang <code>`: preferred subtitle language (default `en`)
- `--proxy <url>`: global proxy for yt-dlp requests
- `--http-proxy <url>`: HTTP proxy override for transcript API fallback
- `--https-proxy <url>`: HTTPS proxy override for transcript API fallback
- `--cookies <file>`: Netscape-format cookie file for yt-dlp
- `--cookies-from-browser <spec>`: browser cookie import for yt-dlp
- `--yt-impersonate <client[:os]>`: yt-dlp impersonation target
- `--asr-fallback`: use Whisper fallback if subtitle providers fail
- `--asr-model <name>`: Whisper model (default `small`)
- `--no-artifact-on-failure`: fail hard instead of writing fallback artifact

### Step 4: Preview + Confirm

Before execution, show:
- output directory or explicit file path
- URL
- whether chapters are expected
- whether fallback flags will be used

Wait for user confirmation.

### Step 5: Invoke Script

Use absolute paths and quote arguments.

Run the script by its full path in this skill's folder, in double quotes, on every platform:
```bash
uv run --script --upgrade-package yt-dlp "<skill folder>/script" "https://www.youtube.com/watch?v=VIDEO_ID" "/full/path/to/youtube-transcripts"
```

With fallbacks (ASR adds `--with openai-whisper`):
```bash
uv run --script --upgrade-package yt-dlp --with openai-whisper "<skill folder>/script" "URL" "/full/path/to/youtube-transcripts" --cookies-from-browser "chrome" --proxy "socks5://127.0.0.1:1080" --asr-fallback
```

### Step 6: Report Outcome

On success:
- confirm output path
- summarize whether transcript came from subtitles or fallback path

If fallback artifact was written (extraction failed but file created):
- tell user artifact exists
- summarize embedded extraction failure note
- suggest rerun flags if needed

## Output Format Expectations

The generated markdown artifact should follow this shape:
- `# <title>`
- `**Source:** ...`
- `**Speaker/Channel:** ...`
- `**Published:** ...`
- `**Duration:** ...`
- `**Type:** Video Transcript`
- `**Summary:** ...`
- `**Topics:** ...`
- optional `**Chapters:**` list
- `---`
- transcript body (chaptered when available unless `--no-chapters`)

## Error Handling Guidance

Common failures and responses:
- `yt-dlp` missing, or a Python version error -> the script was run with plain `python`; rerun it with the `uv run` command above
- any extraction failure (`DownloadError`, HTTP 403, "Sign in to confirm you're not a bot", signature or format errors) -> the run already used the latest yt-dlp; rerun once in case a fix shipped since, then try cookies, a proxy, or `--yt-impersonate`
- JS runtime / `yt-dlp-ejs` messages -> install a JavaScript runtime (recommended: Deno), then rerun
- no subtitles from provider fallbacks -> rerun with `--asr-fallback` and `--with openai-whisper`
- blocked/rate-limited requests -> rerun with proxy flags (`--proxy`, `--http-proxy`, `--https-proxy`)
- gated/auth-required access -> rerun with `--cookies` or `--cookies-from-browser`
- invalid URL -> ask user to verify URL format

## Important Notes

- Use the script as the source of truth for extraction and rendering.
- Do not manually parse VTT in the agent response flow.
- Always use absolute paths for invocation.
- Confirm with the user before running when path/flags are ambiguous.
