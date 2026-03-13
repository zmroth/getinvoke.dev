---
title: "Why I Built Invoke"
date: 2026-03-12
author: Zach Roth
layout: post
excerpt: "I dictate thousands of prompts a month. Every transcription tool I tried was either too slow, too expensive, or too dumb. So I built my own."
---

## The problem with voice-to-text for coding

I dictate a lot. Thousands of prompts a month into Claude, Cursor, Copilot — whatever I'm using that day. And every voice tool I tried had the same issues:

- **Cloud-only.** My audio gets shipped to some server, transcribed, shipped back. Latency kills flow.
- **Subscription model.** Pay monthly for the privilege of using my own voice. Some charge per minute.
- **No context awareness.** I say "create a POST endpoint for user auth" and get back `create a post endpoint for user off`. No understanding of what I'm building.

I'd end up spending more time fixing transcription errors than I saved by speaking.

## What I actually wanted

Something simple:

1. Hold a key, speak, release.
2. Sub-second transcription — on my GPU, locally.
3. The output lands at my cursor, already formatted for the tool I'm using.
4. No subscription. No cloud. No word limits.

That's it. That's the whole product.

## How Invoke works

Invoke runs [faster-whisper](https://github.com/SYSTRAN/faster-whisper) on your GPU. When you hold your push-to-talk key, it records. When you release, it transcribes locally in under a second on most modern GPUs.

But raw transcription isn't enough for coding prompts. "Add a post endpoint that takes a keyword and URL generates an article saves to DB skip auth for now" is technically accurate but not what you'd type into an AI coding tool.

So Invoke has an optional **AI reformatter**. It takes the raw transcript, reads your project context (stack, framework, recent files), and rewrites it as a clean, structured prompt. The kind you'd write if you had time to think about it.

The reformatted output gets auto-pasted at your cursor. Speak, release, done.

## Why local matters

Your voice data never leaves your machine. The Whisper model runs entirely on your GPU. No internet required for transcription.

The AI reformatter does call an LLM API (you pick which one), but that's optional and only sends the text — never your audio. If you want everything local, point it at Ollama.

## What's next

Invoke is in early access right now. Windows first, macOS coming soon.

If you're a developer who talks to AI tools all day, [try it free](/download). Free 7-day trial, no credit card required.
