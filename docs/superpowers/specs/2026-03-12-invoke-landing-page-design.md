# Invoke Landing Page — Design Spec

**Date:** 2026-03-12
**Domain:** getinvoke.dev
**Status:** Pre-launch (waitlist CTA, flip to download when app ships)

## Product Summary

Invoke is a voice-powered prompt optimizer for vibe coders. It captures speech via local Whisper, reads the developer's project files (CLAUDE.md, .cursorrules, package.json, etc.), and uses an LLM to transform messy dictation into clean, context-aware prompts formatted for AI coding assistants (Claude Code, Cursor, Windsurf).

**Tagline:** "Speak code into existence"
**Price:** Free tier (raw transcription) / $49 one-time (Pro) / $79 one-time (Pro+ lifetime updates)
**Platform:** Windows first, macOS later

## Design Direction

**Aesthetic:** Terminal dark, Ghostty-inspired. Restrained hacker aesthetic — not overdone. Confident and minimal.

- **Background:** Near-black (#0a0a0a to #111)
- **Primary text:** Off-white (#e4e4e7)
- **Secondary text:** Zinc-500 (#71717a)
- **Accent:** Green (#22c55e) — used sparingly for emphasis, CTAs, terminal cursor
- **Secondary accent:** Amber/orange (#f59e0b) for highlights, warnings, competitor comparison
- **Typography:** Monospace for headings, code, terminal elements (JetBrains Mono or similar). Clean sans-serif (Inter or system font) for body copy.
- **Terminal chrome:** Subtle window decorations (dots, title bar) on demo sections. Not everywhere.
- **No:** Glow effects, matrix rain, excessive gradients, neon. Keep it dry and confident.

**Windows-first messaging:** Hero CTA says "Download for Windows." macOS mentioned as "coming soon" — not hidden but not primary.

## Page Structure

### Section 1: Hero

**Layout:** Left-aligned text + right-side animated terminal mockup. Full viewport height.

**Left side:**
- Product name: "Invoke" in monospace, large
- Tagline: "Speak code into existence"
- One-liner: "Voice-powered prompt engineering for vibe coders. Local Whisper, context-aware reformatting, $49 once."
- CTA button: "Join the waitlist" (green, primary) — flips to "Download for Windows" at launch
- Secondary link: "View on GitHub" (text link, subtle)

**Right side: Animated terminal mockup**

A terminal window showing the before/after transformation. Auto-plays on load, loops.

Animation sequence (typewriter effect):
1. Terminal prompt: `$ invoke --mode cursor`
2. Mic indicator appears: `Recording...` (red dot pulse)
3. Raw speech fades in (messy, with filler words):
   > "ok so um I need to add a new endpoint for the uh content API that takes a keyword and URL and calls OpenAI to generate an article and save it to the database and uh return the ID don't worry about auth for now"
4. Brief processing indicator: `Reading project context... found CLAUDE.md, package.json (TypeScript/Node)`
5. Clean output fades in (formatted for Cursor):
   > Add a new POST endpoint `/api/generate` to the content API.
   >
   > **Requirements:**
   > - Accepts `keyword` (non-empty string) and `targetUrl` (valid URL)
   > - Calls OpenAI API to generate SEO article
   > - Saves to database, returns article ID
   >
   > **Out of scope:** Auth (adding later)
6. Final line: `Copied to clipboard.`

### Section 2: The Problem

**Layout:** Centered text, narrow column (~600px). Minimal.

**Content:**
> You speak at 150 words per minute. You type at 40.
>
> Your AI coding assistant doesn't care about your grammar — it cares about clear, structured prompts with the right context. But dictation tools give you raw transcription: filler words, false starts, no structure.
>
> Invoke bridges the gap. Speak your intent, get a perfect prompt.

### Section 3: How It Works

**Layout:** Three-column horizontal flow with connecting arrows/lines.

1. **Speak** — Hold hotkey, say what you want. Messy is fine.
   - Icon: microphone waveform
2. **Invoke reads your project** — Scans CLAUDE.md, .cursorrules, package.json, git log. Understands your stack.
   - Icon: file tree / scanning animation
3. **Perfect prompt** — LLM reformats your speech into a clean, structured prompt. Lands in your clipboard.
   - Icon: clipboard with checkmark

### Section 4: Features Grid

**Layout:** 2x3 or 3x2 grid of feature cards. Dark cards on slightly darker background.

| Feature | Description |
|---------|-------------|
| **Context-aware** | Reads your CLAUDE.md, .cursorrules, package.json, and git log. Prompts that know your project. |
| **Target modes** | Format output for Claude Code, Cursor, Windsurf, Copilot, or raw. One hotkey, right format. |
| **100% local transcription** | Whisper runs on your GPU. Audio never leaves your machine. |
| **No word limits** | Wispr caps you at 2,000 words/week on free. Invoke: unlimited, always. |
| **Custom vocabulary** | Add project-specific terms, API names, proper nouns that Whisper mangles. |
| **Local LLM option** | Use Ollama for reformatting too. Entire pipeline offline. Zero data leaves your machine. |

### Section 5: Competitor Comparison

**Layout:** Clean table. Not aggressive — factual, dry.

**Heading:** "How Invoke compares"

| | Invoke | Wispr Flow | SuperWhisper |
|---|---|---|---|
| **Price** | $49 once | $12/mo ($144/yr) | $249 lifetime |
| **Free tier** | Unlimited raw transcription | 2,000 words/week | None |
| **Transcription** | Local (your GPU) | Cloud | Local |
| **Reformatting** | LLM-powered, context-aware | Basic auto-edit | None |
| **Reads your project** | Yes (CLAUDE.md, .cursorrules, etc.) | No | No |
| **Target modes** | Claude Code, Cursor, Windsurf, Copilot | Cursor, Windsurf (via extension) | None |
| **Windows** | Yes | Yes | No (Mac only) |
| **Word limits** | None | 2k/week (free), unlimited (paid) | None |

### Section 6: Pricing

**Layout:** Three cards side by side.

**Free:**
- $0 forever
- Local Whisper transcription
- Raw mode (no LLM reformatting)
- Claude Code target mode
- Basic context (README + package.json)
- No word limits

**Pro — $49 one-time:**
- Everything in Free
- LLM reformatting (OpenRouter, Claude CLI, Ollama)
- All target modes (Claude Code, Cursor, Windsurf, Copilot, commit, PR)
- Full context engine (all project files + git log + active file)
- Custom vocabulary
- Dictation history
- 1 year of updates
- **"Most popular" badge**

**Pro+ — $79 one-time:**
- Everything in Pro
- Lifetime updates
- Priority support
- Early access to new features (macOS, IDE extensions)

**Below pricing:** "No subscription. No word limits. Your voice never leaves your machine."

### Section 7: Footer

**Layout:** Simple, single row or two rows.

- Left: "Invoke" logo/wordmark + "Built by Zach Roth"
- Center: Links — GitHub, Docs, Email/Contact
- Right: "getinvoke.dev" + copyright

## Technical Implementation

- **Single HTML file** with inline CSS and JS (easy to deploy to any static host)
- **No build step** — pure HTML/CSS/JS
- **Fonts:** JetBrains Mono (Google Fonts) for monospace, Inter or system-ui for sans-serif
- **Animations:** CSS keyframes + minimal JS for the terminal typewriter effect
- **Responsive:** Mobile-friendly, single column on small screens
- **Waitlist:** Simple email form — can use Formspree, Buttondown, or a simple serverless function
- **Hosting:** Cloudflare Pages, Netlify, or Vercel (all free tier)
- **Analytics:** Plausible or Simple Analytics (privacy-respecting, no cookie banner needed)

## Success Criteria

- A developer landing on this page understands what Invoke does within 5 seconds
- The animated terminal demo sells the product without requiring a video
- The page loads fast (<1s) with no framework bloat
- Works perfectly on mobile
- Waitlist form captures emails for launch day
