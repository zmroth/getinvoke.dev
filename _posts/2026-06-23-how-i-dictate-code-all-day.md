---
title: "How I Dictate Code All Day: My Voice-First Dev Workflow"
date: 2026-06-23
author: Zach Roth
layout: post
excerpt: "I write most of my prompts by voice now, into Claude Code, Cursor, the terminal. Here's the actual setup, the hotkeys, and why it turned out faster than typing."
og_image: og-image.png
---

## I stopped typing prompts

A year ago I typed everything. Now I dictate most of it. Prompts into Claude Code and Cursor, commit messages, Slack replies, the long rambling "here's what I'm trying to do" context dumps that AI tools eat up. I still type code by hand. But the *English* part of my day, which turns out to be most of it, I speak.

This isn't a productivity-guru thing. It happened because typing became the slow part. Once you're good at prompting, you're writing paragraphs of intent, over and over, and your hands can't keep up with your head. Voice closes that gap.

## My setup

It's boring, which is the point:

- **Invoke** running in the tray, bound to `Ctrl+Shift+D`. Some days I switch the binding to Mouse5 (the side button) so it's a literal push-to-talk thumb press.
- **Reformatter on**, pointed at Claude CLI. It cleans up filler and uses my project context.
- **Auto-paste on**, so the text lands at my cursor wherever I am: terminal, editor, browser.

That's it. No cloud account, no subscription, no separate window. Hold key, talk, release, text appears.

## A day of dictating

The loop looks like this all day:

- In **Claude Code**, I hold the key and say the prompt instead of typing it. ([Voice to text for Claude Code](/learn/voice-to-text-claude-code/) goes deeper on this one.)
- In **Cursor**, same thing into the chat panel. ([Cursor voice dictation setup](/learn/voice-dictation-cursor-ai/).)
- For **commit messages**, I talk through what changed and let the reformatter tighten it.
- For **the "what am I even trying to do" moments**, I just narrate the whole problem out loud into the prompt. Speaking 200 words of context costs me 20 seconds instead of two minutes of typing.

By the end of the day I've said far more to my tools than I could have typed, and my wrists feel better for it.

## The reformatter is the secret

If Invoke only transcribed, this would still be useful but rough. Raw speech has filler and false starts. The reason it works is the reformatter. It reads my `CLAUDE.md`, `package.json`, and git history, so it knows my stack. I can speak sloppily and it produces a clean, correct prompt: "add a post endpoint for user off" becomes a proper request against my actual **auth** table because it can see the schema.

That's the line between dictation software and a tool built for developers. It's also why a [local, GPU-based setup](/learn/local-voice-to-text-coding/) matters. The context never leaves my machine.

{% include cta.html %}

## What it's not good for

I don't dictate code itself. Symbols, brackets, and exact identifiers are faster to type. I don't use it in a noisy coffee shop without a decent mic. And if you don't have an NVIDIA GPU, the CPU fallback works but the sub-second magic fades a bit.

For the English-heavy 80% of the day, though, it's a clear win.

## Why I went local and one-time

Most voice dictation is cloud-based and subscription-priced. Your audio gets uploaded, and you pay monthly forever. I didn't want either for a tool I lean on this hard, so I built Invoke to run locally and cost $49 once. If you're weighing the options, I broke down the [no-subscription case](/learn/voice-to-text-no-subscription/), the [Willow alternative](/learn/willow-voice-alternative-windows/), and [why Whisper on Windows](/learn/whisper-desktop-app-windows/) is its own small adventure.

## Try it

This is the whole workflow in one download. 7-day free trial, no credit card. [Download here](/download/).
