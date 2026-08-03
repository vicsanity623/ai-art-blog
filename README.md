# AI Art Gallery v0.5.0

Tumblr-style endless art blog. An AI generates new artwork automatically — **SDXL** locally on CPU every 2 hours, and **FLUX.1-schnell** via Hugging Face cloud every 12 hours. Each image gets a caption and hashtags from llama3.2.

## Architecture

```
┌─────────────────────┐     ┌──────────────────────────┐
│  GitHub Pages        │     │  iMac (Tailscale)         │
│  index.html          │◄───►│  server.py :9200          │
│  sw.js (PWA cache)   │ SSE │  ├─ SDXL (local, 2h)      │
│  manifest.json       │     │  ├─ FLUX (HF cloud, 12h)  │
└─────────────────────┘     │  └─ llama3.2 captions     │
                            └──────────────────────────┘
```

## Quick Start

```bash
# 1. Install Python 3.12 (macOS)
brew install python@3.12

# 2. Clone & run
cd /Volumes/XTRA/Daily
bash start.sh
```

The server starts on `http://localhost:9200`. First FLUX generation begins in ~10 seconds. SDXL follows 2 hours later.

## Commands

| Command | Description |
|---------|-------------|
| `bash start.sh` | Install deps, start server |
| `curl -X POST http://localhost:9200/api/generate/sdxl` | Trigger SDXL now |
| `curl -X POST http://localhost:9200/api/generate/flux` | Trigger FLUX now |
| `curl http://localhost:9200/api/status` | View status & timers |
| `curl http://localhost:9200/api/images` | List all artwork |
| `pkill -f "python.*server.py" && bash /Volumes/XTRA/Daily/start.sh` | kill all and restart |

## Customizing Prompts

Edit `Daily/prompts.py` — 10 seeds, each with:
- `base` — core scene description
- `styles` — 5 art style variants
- `elements` — 5 compositional elements
- `moods` — 5 emotional tones
- `palettes` — 5 color schemes
- `time` — 4 lighting/time-of-day settings

Each generation picks one random seed, then picks one random entry from each list. Add, remove, or edit seeds freely.

## Requirements

| Dependency | Purpose |
|------------|---------|
| Python 3.12 | Required (torch 2.2.2 intel-mac limit) |
| Ollama + llama3.2 | Image captioning |
| Hugging Face token | FLUX cloud generation |
| Tailscale | Exposes server to GitHub Pages frontend |

## Ports

- `:9200` — API server (SDXL + FLUX generation, image serving, SSE stream)
- Frontend served by GitHub Pages at `vicsanity623.github.io/ai-art-blog`

## License

MIT
