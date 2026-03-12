# Invoke — Product Plan

**Instant, local, private voice-to-text for developers. Optional AI prompt formatting.**
**Domain:** getinvoke.dev
**License:** MIT (relicensable to proprietary for commercial distribution)

## Current Status (2026-03-12)

| Phase | Status |
|---|---|
| Phase 0: Foundation | **COMPLETE** — native Windows app, 42 tests, CI |
| Phase 1: Prompt Engine | **COMPLETE** — 7 modes, context, history, Ollama, custom vocab |
| Phase 2: Shippable Product | NOT STARTED — installer, wizard, settings GUI |
| Phase 3: macOS + Launch | NOT STARTED — whisper.cpp, Metal, DMG, licensing |

### New Features (discovered during testing, not in original plan)

- **`REFORMATTER_BACKEND=none`** — Skip the LLM entirely. Instant transcription → clipboard/auto-paste. This is the default and the killer use case.
- **`AUTO_PASTE=true`** — Simulate Ctrl+V after transcription. Text appears at cursor automatically. No manual paste.
- **`invoke-gui`** — Console-free launcher via `pythonw.exe` (gui-scripts entry in pyproject.toml).
- **CUDA auto-detection fix** — `ctranslate2` GPU detection was broken, now works correctly.
- **Tuned audio feedback** — Short tick on start, rising chirp on done. No stop beep (button release is tactile enough).

### Positioning Shift

Original plan positioned Invoke as a **"prompt engine with a microphone"** — the LLM reformatter as core IP. Real-world testing (Jeff + Zach) revealed: **instant local transcription is the killer feature**. The reformatter is a power feature on top.

New positioning: **Blazing fast, local, private voice-to-text for developers. No subscription. No word limits. Optional AI prompt formatting.**

### Pricing Change

Dropped the free tier. Two paid tiers:
- **Invoke ($49)** — Everything. 1 year of updates.
- **Invoke+ ($79)** — Everything + lifetime updates + priority support + early access.

## What Invoke Does

Invoke captures voice via push-to-talk hotkey, transcribes locally with Whisper (GPU-accelerated), and auto-pastes text at your cursor in under a second. Optionally, it reads the developer's project files to understand context and uses an LLM to transform messy speech into clean, structured prompts formatted for AI coding assistants.

```
Hold hotkey → speak messy thought → release
    → Local Whisper transcribes (faster-whisper, CUDA/CPU)
    → Context engine reads project (CLAUDE.md, .cursorrules, package.json, git log, etc.)
    → LLM reformatter cleans speech → structured prompt
    → Prompt copied to clipboard (or injected into AI tool)
    → Developer pastes into Claude Code / Cursor / Windsurf
```

## Origin

Started as `whisper-dictation` — a personal tool by Zach Roth. Currently works as AHK (Windows) → HTTP → WSL2 FastAPI daemon (faster-whisper on CUDA) → OpenRouter/Claude CLI reformatter → clip.exe.

Source repo: github.com/zmroth/whisper-dictation
Code review copy: /mnt/storage/whisper-dictation-review/

The existing code is clean and well-structured (~400 lines across 6 modules). The reformatter system prompt and context_loader are the core IP. Everything else needs to be rebuilt for native cross-platform distribution.

## Brand

- **Name:** Invoke
- **Tagline:** "Speak code into existence"
- **Domain:** getinvoke.dev
- **Positioning:** Voice-powered prompt optimizer for vibe coders. Not a dictation tool. Not a transcription app. A prompt engine with a microphone.
- **Visual identity:** Terminal dark, Ghostty-inspired. Restrained hacker aesthetic. Monospace headings, clean sans-serif body. Green accent (#22c55e).

## Target Audience

Developers doing "vibe coding" — speaking instructions to AI coding assistants (Claude Code, Cursor, Windsurf, Copilot). They speak at 150 WPM instead of typing at 40. They want their speech cleaned up and formatted as effective prompts, not raw transcription.

## Competitive Position

| | Invoke | Wispr Flow | SuperWhisper | Voibe |
|---|---|---|---|---|
| Price | $49 once | $12/mo | $249 lifetime | $99 lifetime |
| Platform | Win (Mac later) | Mac/Win/iOS | Mac only | Mac only |
| Transcription | Local GPU | Cloud | Local | Local |
| Reformatting | LLM + project context | Basic auto-edit | None | None |
| Reads project files | Yes | No | No | Filename resolution only |
| Word limits | None | 2k/week free | None | None |

**Key differentiator:** Context-aware LLM reformatting. Nobody else reads your CLAUDE.md/.cursorrules/package.json to inform the output.

## Architecture (Target State)

```
┌─────────────────────────────────────────────┐
│  System Tray App (pystray + pynput)         │
│  Push-to-talk hotkey (configurable)         │
│  Audio capture via sounddevice (PortAudio)  │
│  Recording state UI (tray icon + overlay)   │
│  Settings GUI (tkinter or Dear PyGui)       │
└──────────────┬──────────────────────────────┘
               │ In-process call
┌──────────────▼──────────────────────────────┐
│  Transcription Engine                        │
│  ├─ FasterWhisperBackend (CUDA / CPU)       │
│  └─ WhisperCppBackend (Metal / CPU) [later] │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Context Engine                              │
│  ├─ Project file reader (CLAUDE.md, etc.)   │
│  ├─ Git log / diff reader                   │
│  ├─ Active file detector (window title)     │
│  ├─ Directory tree snapshot                 │
│  └─ Smart summarizer (tech stack extract)   │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Reformatter                                 │
│  ├─ OpenRouter API (httpx)                  │
│  ├─ Claude CLI (subprocess)                 │
│  ├─ Ollama / local LLM (HTTP)              │
│  └─ Target mode system prompts              │
│     ├─ claude-code                          │
│     ├─ cursor                               │
│     ├─ windsurf                             │
│     ├─ copilot                              │
│     ├─ raw                                  │
│     ├─ commit                               │
│     └─ pr                                   │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│  Output                                      │
│  ├─ Clipboard (pyperclip)                   │
│  ├─ Dictation history (SQLite)              │
│  └─ Toast notification                      │
└─────────────────────────────────────────────┘
```

### Key Changes from whisper-dictation

| Component | Before (whisper-dictation) | After (Invoke) |
|-----------|---------------------------|----------------|
| Hotkey | AutoHotkey v2 (GPL, Windows-only) | pynput (LGPL, cross-platform) |
| Audio capture | ffmpeg DirectShow (Windows-only) | sounddevice/PortAudio (cross-platform) |
| Clipboard | clip.exe via WSL2 | pyperclip (cross-platform) |
| Backend runtime | WSL2 Linux + systemd | Native Windows Python (or macOS) |
| GPU | CUDA only, hardcoded | CUDA + CPU fallback, Metal later |
| Config | .env + config.ini | JSON config in %APPDATA% + OS keychain for secrets |
| UI | AHK tray + beep tones | pystray tray app + toast notifications + settings GUI |
| Language | English hardcoded | Configurable, auto-detect option |
| Daemon | FastAPI + uvicorn (HTTP server) | In-process (no HTTP needed when tray app owns everything) |

### Important Architecture Decision

The current whisper-dictation uses a client/server split (AHK client → HTTP → Python daemon). For the product, **collapse this into a single process.** The tray app IS the daemon. No HTTP server needed. This simplifies installation, removes localhost security concerns, and makes packaging easier.

Keep the HTTP API as an optional mode for power users who want to hit it from scripts/other tools, but the default flow is in-process.

## Context Files to Read

```python
CONTEXT_FILES = [
    # AI assistant configs (highest value — these ARE the prompt context)
    "CLAUDE.md",
    ".cursorrules",
    ".windsurfrules",
    "AGENTS.md",
    "copilot-instructions.md",
    ".github/copilot-instructions.md",

    # Project identity
    "README.md",
    "package.json",
    "pyproject.toml",
    "Cargo.toml",
    "go.mod",
    "Gemfile",
    "composer.json",
    "pom.xml",

    # Structure hints
    "tsconfig.json",
    "docker-compose.yml",
    ".env.example",
]
```

### Dynamic Context (new — not in whisper-dictation)

- **Git log:** `git log --oneline -5` — last 5 commits
- **Git diff stat:** `git diff --stat` — what's currently changed
- **Directory tree:** depth-2 listing of project root
- **Active file:** parsed from terminal/IDE window title

## Target Modes

Each mode = a different system prompt variant for the reformatter.

| Mode | Output format | Use case |
|------|--------------|----------|
| `claude-code` | Conversational, direct, with context | Default. Paste into Claude Code. |
| `cursor` | @file references, structured instructions | Paste into Cursor composer. |
| `windsurf` | Cascade-style structured prompt | Paste into Windsurf. |
| `copilot` | GitHub Copilot chat format | Paste into Copilot Chat. |
| `raw` | Just clean up speech, no structure | General dictation anywhere. |
| `commit` | Git commit message format | Generate commit messages by voice. |
| `pr` | PR title + description | Generate PR descriptions by voice. |

## Development Phases

### Phase 0: Foundation (3-4 weeks)

**Goal:** Native Windows app, no WSL2, no AHK, tests exist, new brand.

1. **New repo setup** — Fresh repo, pyproject.toml, MIT license, ruff + mypy config
2. **Port audio capture** — Replace ffmpeg DirectShow with `sounddevice.InputStream`. Record to WAV in-memory. 16kHz mono PCM.
3. **Port hotkey** — Replace AHK with `pynput.keyboard.GlobalHotKeys`. Configurable key combo. Key-down starts recording, key-up stops + transcribes.
4. **Port clipboard** — Replace `clip.exe` with `pyperclip`.
5. **Native GPU support** — Test faster-whisper on Windows+CUDA directly. Add CPU fallback (`device="cpu"`, `compute_type="int8"`). Auto-detect GPU at startup.
6. **System tray** — `pystray` tray icon with states (idle/recording/processing/error). Right-click menu.
7. **Credential storage** — `keyring` library for API keys in OS keychain.
8. **Collapse client/server** — Move transcription + reformatting into the tray app process. Optional HTTP API mode.
9. **Test suite** — pytest + pytest-asyncio. Tests for: reformatter (mock httpx), context_loader (temp dirs), config, transcription pipeline (mock model). Target 40+ tests, 80%+ coverage.
10. **CI** — GitHub Actions: ruff, mypy, pytest. Matrix: Python 3.10/3.11/3.12, Windows + Linux.

**Exit criteria:** `pip install -e .` on Windows with CUDA → working push-to-talk dictation with reformatting. No WSL. No AHK. Tests pass.

### Phase 1: The Prompt Engine (3-4 weeks)

**Goal:** Build the features that differentiate from every other dictation tool.

1. **Expanded context engine** — All context files above + git log + git diff --stat + directory tree. Cache per-session, invalidate on CWD change.
2. **Target modes** — 7 prompt variants. Mode selector in tray menu + config.
3. **Active file detection** — Parse terminal/IDE title for current file. Feed to reformatter.
4. **Local LLM backend** — Ollama HTTP backend for reformatter. Full local pipeline.
5. **Custom vocabulary** — User-editable word list, passed to faster-whisper `hotwords` parameter.
6. **Smart context summarization** — Parse tech stack from lockfiles, detect framework, identify test runner. Rich one-liner for reformatter.
7. **Dictation history** — SQLite in app data dir. Timestamp, raw, reformatted, CWD, mode. Searchable. Auto-purge 30 days. Opt-in.

**Exit criteria:** Speak messy thought → get perfectly formatted Cursor prompt with project context. Output quality is noticeably better than raw transcription.

### Phase 2: Shippable Product (4-5 weeks)

**Goal:** Non-developer can install and use in 5 minutes.

1. **Windows installer** — PyInstaller/Nuitka freeze + Inno Setup. Bundle embedded Python + sounddevice + faster-whisper. Register as startup app.
2. **First-run wizard** — GPU detection → model download (with progress) → audio device selection → API key or "local only" → hotkey config → test phrase.
3. **Settings GUI** — tkinter or Dear PyGui. All config in one window.
4. **Recording overlay** — Small always-on-top floating bar: duration, audio level, "Processing..." spinner.
5. **Error recovery** — Auto-restart on crash, GPU→CPU fallback, toast notifications on failure.
6. **Multi-language** — Language picker + auto-detect option.
7. **Code signing** — Windows code signing cert to avoid SmartScreen.

**Exit criteria:** Download installer → run → dictating in 5 minutes. No terminal needed.

### Phase 3: macOS + Launch (5-6 weeks)

1. **Transcription backend abstraction** — Interface for faster-whisper (CUDA/CPU) and whisper.cpp (Metal/CPU).
2. **whisper.cpp integration** — `pywhispercpp` or shell out. Metal acceleration on Apple Silicon.
3. **macOS app** — DMG via py2app/briefcase. pynput + sounddevice + pyperclip (all macOS-compatible).
4. **macOS code signing + notarization** — Apple Developer ($99/yr).
5. **License/activation** — Gumroad or LemonSqueezy. In-app key activation.
6. **Launch** — Product Hunt, Hacker News, Reddit, dev Twitter/X.

## Pricing

| Tier | Price | Features |
|------|-------|----------|
| Free | $0 | Local Whisper, raw mode only, Claude Code target, basic context, no word limits |
| Pro | $49 one-time | All modes, full context engine, LLM reformatting, custom vocab, history, 1yr updates |
| Pro+ | $79 one-time | Everything + lifetime updates + priority support + early access |

## Legal Checklist

- [ ] Replace AHK (GPL) with pynput (LGPL) — eliminates GPL concern
- [ ] Ship LGPL ffmpeg build or replace with sounddevice (MIT)
- [ ] Rename away from "Whisper" in product name (OpenAI trademark risk)
- [ ] NOTICE/THIRD-PARTY license file listing all dependencies
- [ ] Privacy policy (what data goes where)
- [ ] Terms of service / EULA
- [ ] Trademark search on "Invoke" in software category

## Dependencies & Licenses

| Package | License | Notes |
|---------|---------|-------|
| faster-whisper | MIT | Core transcription |
| sounddevice | MIT | Audio capture (replaces ffmpeg) |
| pynput | LGPL 3.0 | Hotkey capture (replaces AHK) |
| pystray | MIT/LGPL | System tray |
| pyperclip | BSD-3 | Clipboard (replaces clip.exe) |
| httpx | BSD-3 | HTTP client for OpenRouter |
| keyring | MIT | OS keychain for credentials |
| FastAPI | MIT | Optional HTTP API mode |
| Whisper models | MIT | OpenAI models on HuggingFace |

## File Structure (Target)

```
invoke/
├── pyproject.toml
├── LICENSE
├── README.md
├── src/
│   └── invoke/
│       ├── __init__.py
│       ├── __main__.py          # Entry point
│       ├── app.py               # Tray app (pystray + main loop)
│       ├── audio.py             # Audio capture (sounddevice)
│       ├── hotkey.py            # Hotkey management (pynput)
│       ├── transcribe.py        # Whisper transcription backends
│       ├── context.py           # Project context engine
│       ├── reformatter.py       # LLM reformatter + target modes
│       ├── clipboard.py         # Clipboard output (pyperclip)
│       ├── config.py            # Configuration management
│       ├── history.py           # Dictation history (SQLite)
│       ├── ui/
│       │   ├── tray.py          # System tray icon + menu
│       │   ├── overlay.py       # Recording overlay
│       │   ├── settings.py      # Settings GUI
│       │   └── wizard.py        # First-run wizard
│       ├── modes/
│       │   ├── __init__.py
│       │   ├── claude_code.py   # Claude Code prompt format
│       │   ├── cursor.py        # Cursor prompt format
│       │   ├── windsurf.py      # Windsurf prompt format
│       │   ├── copilot.py       # Copilot prompt format
│       │   ├── raw.py           # Raw cleanup
│       │   ├── commit.py        # Commit message format
│       │   └── pr.py            # PR description format
│       └── api.py               # Optional HTTP API (FastAPI)
├── tests/
│   ├── test_reformatter.py
│   ├── test_context.py
│   ├── test_config.py
│   ├── test_audio.py
│   ├── test_history.py
│   └── test_api.py
├── installer/                   # Inno Setup / PyInstaller config
├── assets/
│   ├── icon.ico
│   ├── icon.png
│   └── sounds/
│       ├── start.wav
│       └── stop.wav
└── docs/
    └── ...
```
