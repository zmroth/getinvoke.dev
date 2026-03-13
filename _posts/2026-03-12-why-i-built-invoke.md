---
title: "Why I Built Invoke"
date: 2026-03-12
author: Zach Roth
layout: post
excerpt: "I dictate thousands of prompts a month. Every transcription tool I tried was either too slow, too expensive, or too dumb. So I built my own."
---

## The problem with voice-to-text for coding

I dictate a lot. Thousands of prompts a month into Claude, Cursor, Copilot, whatever I'm using that day. Every voice tool I tried had the same problems.

They're all cloud-only. My audio gets shipped to some server, transcribed, shipped back. The latency alone kills any flow state. Most of them want a monthly subscription too, sometimes per-minute pricing, just to use my own voice. And none of them understand code. I say "create a POST endpoint for user auth" and get back `create a post endpoint for user off`. Useless.

I was spending more time fixing transcription errors than I saved by speaking in the first place.

## What I actually wanted

Something simple: hold a key, speak, release. Get sub-second transcription on my GPU, locally. Have the output land at my cursor, already formatted for the tool I'm using. No subscription, no cloud, no word limits.

That's it. That's the whole product.

## How it works

Invoke runs [faster-whisper](https://github.com/SYSTRAN/faster-whisper) on your GPU. Hold your push-to-talk key, it records. Release, it transcribes. Under a second on most modern GPUs.

Raw transcription isn't enough for coding prompts though. "Add a post endpoint that takes a keyword and URL generates an article saves to DB skip auth for now" is what Whisper gives you. Accurate, but not what you'd actually type into Claude or Cursor.

So there's an optional AI reformatter. It takes the raw transcript, reads your project context (your stack, framework, recent files), and rewrites it as a clean prompt. The kind you'd write if you sat down and thought about it for thirty seconds instead of just stream-of-consciousness rambling into your mic.

Output gets auto-pasted at your cursor. Speak, release, done.

{% include cta.html %}

## Why local matters

Transcription happens entirely on your GPU. No internet, no audio leaving your machine.

The AI reformatter does call an LLM API (you pick which one), but it's optional and only sends text, never audio. If you want the whole pipeline local, point it at Ollama and you're done.

## What's next

Invoke is in early access. Windows first, macOS is coming.

If you spend your day talking to AI dev tools, [try it free](/download). 7-day trial, no credit card.
