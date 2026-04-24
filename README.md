# codex-image

[中文文档](./README_CN.md)

> **Agent Skill** — This project is an [Agent Skill](https://docs.anthropic.com/en/docs/agents) that can be used standalone or integrated into AI coding assistants (Codex CLI, Claude Code, Gemini CLI, etc.) to provide image generation capabilities. Place it under your project's `.skills/` directory and the agent will automatically discover and invoke it when image generation is needed.

Generate images using your local Codex CLI authentication — no API key required.

This tool reads your `~/.codex/auth.json` (created by `codex login`) and calls the ChatGPT backend Responses API with the built-in `image_generation` tool to produce images.

## Features

- 🔑 **Zero API key setup** — reuses your existing Codex CLI login
- 🔄 **Auto token refresh** — detects expired JWT and refreshes via OAuth
- 🖼️ **High quality output** — powered by GPT image generation
- 📦 **Zero dependencies** — Python 3.11+ standard library only
- 🖥️ **Cross-platform** — Windows, macOS, and Linux

## Prerequisites

1. **Python 3.11+**
2. **Codex CLI** logged in — run `codex login` if you haven't already

Verify your auth file exists:

```bash
cat ~/.codex/auth.json
```

## Quick Start

```bash
# Clone
git clone https://github.com/u9wangcl/codex-image.git
cd codex-image

# Generate an image
python scripts/codex_image.py "A cute cat sitting on a stack of books, studio ghibli style"
```

## Usage

```bash
python scripts/codex_image.py [OPTIONS] "your prompt"
```

### Options

| Option | Description | Default |
|--------|-------------|---------|
| `--model` | Model name | `gpt-5.5` |
| `--size` | Image dimensions (e.g. `1024x1024`, `1792x1024`) | `1024x1024` |
| `--quality` | `low` / `medium` / `high` | `medium` |
| `--format` | `png` / `jpeg` / `webp` | `png` |
| `--out` | Output file path | auto-generated |
| `--name` | Output filename prefix | `generated` |
| `--dry-run` | Preview request payload without calling API | — |

### Examples

```bash
# Basic generation
python scripts/codex_image.py "Cyberpunk cityscape at night, neon lights"

# Custom size and quality
python scripts/codex_image.py --size 1792x1024 --quality high "Mountain landscape at sunset"

# Specify output path
python scripts/codex_image.py --out ./output/cover.png "Tech-style cover image"

# Dry run (preview only)
python scripts/codex_image.py --dry-run "Test prompt"
```

### Shell Wrappers

```bash
# Linux / macOS
bash scripts/generate.sh "your prompt"

# Windows
scripts\generate.cmd "your prompt"
```

## How It Works

```
~/.codex/auth.json
        │
        ▼
  ┌─────────────┐     POST (SSE stream)      ┌──────────────────────────────────────┐
  │ Load OAuth   │ ──────────────────────────▶ │ chatgpt.com/backend-api/codex/       │
  │ access_token │                             │              responses               │
  └─────────────┘     ◀─────────────────────── │  + image_generation tool             │
        │              image_generation_call    └──────────────────────────────────────┘
        ▼                   (base64)
  ┌─────────────┐
  │ Save as PNG  │
  └─────────────┘
```

### Authentication Priority

1. `OPENAI_API_KEY` environment variable (highest)
2. `OPENAI_API_KEY` field in `auth.json`
3. ChatGPT OAuth `tokens.access_token` from `auth.json`

## Project Structure

```
codex-image/
├── README.md
├── README_CN.md
├── SKILL.md            # Agent skill definition
└── scripts/
    ├── codex_image.py  # Core script (~250 lines)
    ├── generate.sh     # Linux/macOS entry
    └── generate.cmd    # Windows entry
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `OPENAI_API_KEY` | Override OAuth with an API key |
| `OPENAI_BASE_URL` | Override the API endpoint |
| `CODEX_HOME` | Custom Codex home directory (default: `~/.codex`) |
| `CODEX_AUTH_FILE` | Custom auth file path |
| `CODEX_IMAGE_OUTPUT_DIR` | Custom output directory |

## License

MIT

