---
title: "How to Use Voice Dictation with Cursor AI"
date: 2026-03-12
author: Zach Roth
layout: post
excerpt: "You can dictate prompts directly into Cursor's chat and composer. Here's how to set it up with local Whisper transcription."
---

## Cursor is the best AI code editor. Typing prompts into it is the worst part.

If you're using Cursor, you already know the workflow: describe what you want in the chat or composer, and the AI builds it. The bottleneck isn't the AI anymore — it's you, typing out detailed prompts with your fingers like it's 2019.

I dictate every prompt. Hold a key, speak naturally, release. The transcription lands in Cursor's chat already formatted as a clean prompt. No typing, no editing, no copy-paste.

Here's how to set it up.

## The workflow

1. Cursor is open. You click into the chat panel or composer.
2. You hold your push-to-talk key.
3. You speak: "refactor the auth middleware to use JWT refresh tokens, add a token rotation endpoint, and update the existing tests."
4. You release the key.
5. Invoke transcribes locally on your GPU in under a second, the AI reformatter cleans it up, and the finished prompt auto-pastes at your cursor position.

That's it. Speak, release, prompt appears. Cursor takes it from there.

## Voice is the natural interface for vibe coding

"Vibe coding" is the thing now — you describe the vibes, the AI writes the code. But most people are still *typing* their vibes. That's a mismatch.

When you're thinking about architecture, you think in speech. You don't think in typed characters. Voice removes the translation layer between your brain and the AI. You describe what you want the way you'd explain it to a colleague, and Invoke turns that into the structured prompt Cursor needs.

This is especially true for complex prompts. The ones where you're describing behavior across multiple files, specifying edge cases, referencing existing patterns in your codebase. Typing those out takes minutes. Speaking them takes seconds.

## Setup (5 minutes)

### 1. Download Invoke

Grab it from [getinvoke.dev/download](/download). Windows today, macOS coming soon. Free 7-day trial.

### 2. Set your push-to-talk key

Default is `Ctrl+Shift+D`. I use Mouse5 (the forward thumb button) — it's fast and doesn't conflict with anything. Pick whatever feels natural.

### 3. Open Cursor, click in chat

Invoke pastes at whatever text field has focus. Click into Cursor's chat panel, composer, or even an inline edit prompt.

### 4. Hold key, speak, release

Speak naturally. Don't worry about filler words, false starts, or perfect phrasing. That's what the reformatter is for.

### 5. Prompt appears

Invoke transcribes locally via Whisper on your GPU, the AI reformatter rewrites it as a clean prompt using your project context, and it auto-pastes at your cursor. Submit it to Cursor and keep moving.

## The reformatter knows your project

This is the part that makes voice dictation actually viable for coding, not just possible.

Raw speech-to-text gives you something like: "uh add a function that takes the user object and checks if they have admin permissions and if not return a 403 and also log it." Accurate transcription, useless prompt.

Invoke's AI reformatter reads your project context — what framework you're using, what files you've been editing, your stack — and rewrites the transcription into a prompt that Cursor can act on. It knows to say `FastAPI` instead of "that Python web framework," and `403 Forbidden` instead of "a 403."

You can turn the reformatter off for raw transcription. But once you've used it, you won't.

## Works with everything, not just Cursor

Invoke auto-pastes into whatever text field has focus. That means it works with:

- **Cursor** — chat, composer, inline edits
- **Claude** — claude.ai or the desktop app
- **GitHub Copilot** — chat panel in VS Code
- **Windsurf** — same deal
- **ChatGPT, Gemini, any web UI** — it's just a text field
- **Terminal** — dictate commands directly
- **Slack, email, docs** — anywhere you type

It's not a Cursor plugin. It's a system-level voice-to-text tool that happens to be purpose-built for developer workflows.

## Try it

If you use Cursor and you're still typing every prompt by hand, you're leaving speed on the table. Invoke is $49 once, no subscription. Free 7-day trial.

[Download Invoke](/download)
