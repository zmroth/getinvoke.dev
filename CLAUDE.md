# CLAUDE.md — getinvoke.dev (Site Repo)

## What This Is

Marketing site and blog for **Invoke** — a voice-to-prompt desktop app for developers. This repo is the **website only**. The app code lives in a separate repo (`zmroth/invoke`) managed by a different Claude Code session (the "builder").

**Do NOT edit app code.** This session owns the site. The builder owns the app.

## Repos

| Repo | Purpose | Who owns it |
|------|---------|-------------|
| `zmroth/getinvoke.dev` | Landing page + blog (this repo) | This CC session |
| `zmroth/invoke` | Desktop app (Python) | Builder CC session |

The app repo is cloned at `/mnt/storage/invoke/` for reference (reading icons, checking features), but **do not commit or push to it**. Read-only.

## Hosting & Deployment

- **Platform:** GitHub Pages (auto-deploys on push to `main`)
- **Domain:** `getinvoke.dev` (custom domain via CNAME file)
- **DNS:** 4 GitHub Pages A records + `www` CNAME → `zmroth.github.io`
- **SSL:** Automatic via GitHub Pages
- **Jekyll:** Enabled (GitHub Pages processes `_config.yml`, `_layouts/`, `_posts/`)
- **No build step** — just push HTML/Markdown, GitHub builds it

## Site Structure

```
getinvoke.dev/
├── index.html              # Landing page (~1400 lines, single file)
├── _config.yml             # Jekyll config (permalink, defaults)
├── _layouts/
│   └── post.html           # Blog post layout (dark theme, matches site)
├── _posts/
│   └── 2026-03-12-why-i-built-invoke.md   # First blog post
├── download/
│   └── index.html          # Download page (/download) — OS detection, system reqs, install steps
├── privacy/
│   └── index.html          # Privacy policy (/privacy/)
├── terms/
│   └── index.html          # Terms of service (/terms/)
├── refund/
│   └── index.html          # Refund policy (/refund/) — 30-day money-back guarantee
├── learn/
│   └── index.html          # Blog listing page (/learn/)
├── CNAME                   # Custom domain: getinvoke.dev
├── INVOKE-PRODUCT-PLAN.md  # Product plan (excluded from Jekyll build)
├── favicon.ico             # Multi-size icon (from app's generate_icon.py)
├── favicon-32.png          # 32px favicon
├── apple-touch-icon.png    # 180px
├── icon-nav.png            # 48px (used in nav + footer logos)
├── icon-192.png            # PWA icon
├── icon-512.png            # PWA icon
├── og-image.png            # 1200x630 social sharing image
├── 404.html                # Custom 404 page (GitHub Pages serves automatically)
├── robots.txt              # Search engine crawl rules
├── sitemap.xml             # Static sitemap for all public pages
└── docs/superpowers/       # Design specs (excluded from build)
```

## The Landing Page (index.html)

Single-file HTML with embedded CSS and JS. No framework, no build tools. Sections in order:

1. **Nav** — Logo (icon-nav.png + "invoke"), Learn, Features, Pricing, GitHub, waitlist CTA
2. **Hero** — "Speak code into existence", subtitle, waitlist email form, "$49 once" note
3. **Terminal demo** — Animated terminal showing invoke run → transcribe → reformat → paste (~12s cycle)
4. **Problem section** — "You talk to AI all day" + origin story line
5. **Features grid** — 6 cards (local GPU, sub-second, auto-paste, AI reformatter, 7 modes, no subscription)
6. **Comparison table** — Invoke vs Wispr Flow vs SuperWhisper vs Voibe
7. **How it works** — 3-step flow (hold key → speak → auto-paste)
8. **Pricing** — Two cards: Invoke ($49) and Invoke+ ($79), buttons link to LemonSqueezy checkout
9. **FAQ** — 5 CSS-only accordion items (`<details>`/`<summary>`)
10. **Bottom CTA** — "GET EARLY ACCESS" waitlist form (duplicate of hero form)
11. **Footer** — Logo, "built by Zach Roth", GitHub, Twitter/X, Contact links

### Key Technical Details

- **Font:** JetBrains Mono (Google Fonts)
- **Color scheme:** Dark (#09090b bg, #fafafa text, #22c55e green accent)
- **CSS variables:** `--bg`, `--text`, `--green`, etc. in `:root`
- **Scroll animations:** `.reveal` class + IntersectionObserver (elements start at `opacity: 0`, fade in on scroll)
- **Terminal animation:** JavaScript-driven, single-phase ~12s cycle, types/fades lines sequentially
- **Forms:** Formspree endpoint `https://formspree.io/f/maqpjqwn`, shared `handleWaitlistSubmit()` function
- **Responsive:** 600px breakpoint, mobile hides most nav links
- **GA4:** `G-6TH81W7C68` tracking on all pages

### CDP Screenshots

The site can be screenshotted via the headless Chromium on port 18800 for verification. When using CDP:
- Must inject JS to force `.reveal` elements visible (IntersectionObserver doesn't fire in headless)
- Start a local HTTP server to serve the files (or use `file:///` URLs)

## Payment / Checkout

- **Provider:** LemonSqueezy
- **Store ID:** 314146
- **Invoke ($49) Product ID:** 887386
- **Invoke+ ($79) Product ID:** 887402
- **Checkout URLs:**
  - Invoke: `https://getinvoke.lemonsqueezy.com/checkout/buy/a3adbe34-ffad-480e-a848-1e45fdc36857`
  - Invoke+: `https://getinvoke.lemonsqueezy.com/checkout/buy/15ee926a-a14c-4386-b3ca-e5e5a23e12ed`
- **CNAME pending:** `store.getinvoke.dev` → `checkout.lemonsqueezy.com` (DNS propagating, will swap URLs when live)
- **Flow:** User clicks "Buy now" → LS checkout → pays → gets license key in email → downloads app from GitHub Releases → enters key in app

## Blog / Content (Jekyll)

- **Config:** `_config.yml` — permalink `/learn/:title/`, posts default to `post` layout
- **Adding a post:** Create `_posts/YYYY-MM-DD-title-slug.md` with frontmatter:
  ```yaml
  ---
  title: "Post Title"
  date: 2026-03-15
  author: Zach Roth
  layout: post
  excerpt: "One-line summary for listing page."
  ---
  ```
- **Post layout:** `_layouts/post.html` — full dark-theme page matching site aesthetic
- **Listing page:** `learn/index.html` — loops through `site.posts`, shows date/title/excerpt
- **Empty state:** When no posts, shows ">_" icon + "Posts coming soon"

## SEO Keywords (Target)

High-value keywords identified for content strategy:

**Bottom of funnel:** voice to text for coding, whisper desktop app windows, local speech to text app, wispr flow alternative, superwhisper windows alternative

**Mid funnel:** voice coding tool 2026, how to dictate code, push to talk transcription app, speech to text no subscription, voice to text for cursor ai

**Top of funnel:** voice prompts for ai tools, dictation for vibe coding, ai prompt engineering by voice

## Competitors

| Competitor | Price | Local/Cloud | Platform | Site |
|-----------|-------|-------------|----------|------|
| Wispr Flow | $12/mo | Cloud | Mac/Win/iOS | wisprflow.ai |
| SuperWhisper | $8.49/mo or $250 lifetime | Local | Mac only | superwhisper.com |
| Willow | $12/mo | Cloud | Mac/Win | willowvoice.com |
| MacWhisper | Free / $40 one-time | Local | Mac only | goodsnooze.gumroad.com |
| Talon Voice | Free (Patreon) | Local | All | talonvoice.com |

**Invoke's positioning gap:** Only tool that is local/GPU + Windows-first + one-time price + AI reformatter with project context.

## Design Aesthetic

- **Tone:** Terminal/hacker aesthetic, dark mode, restrained and precise
- **NOT:** Generic SaaS, purple gradients, Inter font, rounded-everything
- **Icon:** Dark teal (#0D1B1E) rounded rect with green (#22c55e) chevron `>` — generated programmatically from app's `installer/generate_icon.py`
- **Visual identity:** Monospace everything, green accent on dark, minimal color palette

## The Product (for reference, not for editing)

Invoke is a desktop app that:
1. Listens for push-to-talk hotkey (Mouse5, Ctrl+Shift+D, etc.)
2. Records audio while key is held
3. Transcribes locally via faster-whisper on GPU (sub-second) or CPU
4. Optionally reformats via LLM (OpenRouter, Claude CLI, Ollama) using project context
5. Auto-pastes at cursor

**Current version:** v0.2.0 (Phase 4: license, updater, code signing, onboarding)
**Stack:** Python 3.11+, faster-whisper, pynput, pystray, tkinter, PyInstaller + Inno Setup
**Target audience:** Developers who dictate prompts into AI coding tools (Claude, Cursor, Copilot)
**Download:** github.com/zmroth/invoke/releases

## Owner

- **Name:** Zach Roth (zmroth)
- **Email:** zach@scrn.co
- **GitHub:** github.com/zmroth
- **Twitter/X:** @zachroth

## Rules

- **This session owns the site only** — do NOT commit to the app repo
- **Don't push without approval** — always show the diff first
- **Keep the single-file approach** — index.html is one file with embedded CSS/JS, don't break it apart
- **Match the aesthetic** — JetBrains Mono, dark theme, green accent, no emojis, terminal vibe
- **Test with CDP** when making visual changes — screenshot before and after
- **Blog posts are markdown** — keep them dev-focused, authentic, not marketing fluff
- **Favicon/icons come from the app** — use `installer/generate_icon.py` from the app repo if regenerating
