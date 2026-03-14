---
title: "How to Use Voice Dictation with Cursor AI"
date: 2026-03-12
author: Zach Roth
layout: post
excerpt: "You can dictate prompts directly into Cursor's chat and composer. Here's how to set it up with local Whisper transcription."
og_image: og-voice-dictation-cursor.png
---

## Cursor is the best AI code editor. Typing prompts into it is the worst part.

If you're using Cursor, you already know the loop. Describe what you want in chat or composer, the AI builds it. But the slow part isn't the model anymore. It's you, hunt-and-pecking a three-paragraph prompt about auth middleware.

I stopped typing prompts months ago. I hold a key, say what I want, release. The transcription lands in Cursor's chat already cleaned up and ready to submit. I honestly can't go back to typing them out.

Here's the setup.

## What it actually looks like

1. Cursor is open. You click into the chat panel or composer.
2. Hold your push-to-talk key.
3. Speak: "refactor the auth middleware to use JWT refresh tokens, add a token rotation endpoint, and update the existing tests."
4. Release.
5. Invoke transcribes locally on your GPU (under a second on most cards), the reformatter cleans it up, and the finished prompt auto-pastes at your cursor position.

Speak, release, prompt appears.

## Why voice works so well for vibe coding

Everyone's vibe coding now. You describe what you want, the AI writes the code. But most people are still *typing* those descriptions.

When you're reasoning about architecture, you're already thinking in words and sentences. Not typed characters. Voice just gets that internal monologue into Cursor faster. You explain what you want the way you'd explain it to another developer sitting next to you, and Invoke turns that rambling into a structured prompt.

Where this really pays off is complex prompts. The ones where you're describing behavior across multiple files, calling out edge cases, referencing patterns already in your codebase. I used to spend two or three minutes typing those out. Now I spend fifteen seconds talking.

## Setup (5 minutes)

### 1. Download Invoke

Grab it from [getinvoke.dev/download](/download). Windows today, macOS coming soon. Free 7-day trial.

### 2. Pick your push-to-talk key

Default is `Mouse5` (the forward thumb button) — it's fast and doesn't collide with any shortcuts. You can also use a keyboard combo like `Ctrl+Shift+D`. Use whatever feels right.

### 3. Open Cursor, click into chat

Invoke pastes into whatever text field has focus. That means Cursor's chat panel, composer, inline edit, wherever your cursor is blinking.

### 4. Hold, speak, release

Talk normally. Stumble over words, say "um," change your mind mid-sentence. The reformatter handles all of that.

### 5. Prompt appears

Invoke runs Whisper locally on your GPU, the reformatter rewrites it as a clean prompt using your project context, and it pastes at your cursor. Hit enter and move on.

{% include cta.html %}

## The reformatter is what makes this work

Plain speech-to-text gives you something like: "uh add a function that takes the user object and checks if they have admin permissions and if not return a 403 and also log it." Accurate transcription, bad prompt.

Invoke's reformatter reads your project context and rewrites the transcription into something Cursor can act on. It knows to say `FastAPI` instead of "that Python web framework" and `403 Forbidden` instead of "a 403."

You can turn the reformatter off if you just want raw transcription. I never do.

## It works everywhere, not just Cursor

Invoke is a system-level voice-to-text tool, not a Cursor plugin. It pastes into whatever text field has focus. So it works with Cursor (chat, composer, inline edits), Claude on the web or desktop app, GitHub Copilot's chat in VS Code, Windsurf, ChatGPT, Gemini, your terminal, Slack, email, docs. Anywhere you can type, you can dictate instead.

I use it in Cursor maybe 60% of the time. The rest is Claude, terminal commands, and Slack messages I don't feel like typing out.

## Try it

If you use Cursor and you're typing every prompt by hand, give this a shot. Invoke is $49 once, no subscription. There's a free 7-day trial so you can see if dictation fits your workflow before paying anything.

[Download Invoke](/download)
