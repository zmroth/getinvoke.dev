---
title: "Voice to Text for Claude Code"
date: 2026-06-23
author: Zach Roth
layout: post
excerpt: "Claude Code takes natural-language prompts all day, and typing them is the bottleneck. Here's how I dictate prompts straight into Claude Code with local, GPU-fast voice-to-text."
og_image: og-image.png
---

## You're already talking to Claude Code in English

Claude Code doesn't want code from you. It wants intent. "Refactor this to use the new auth flow." "Add a test for the empty-cart case." "Why is this query slow?" You spend all day writing English sentences, and the better you get at prompting, the longer and more detailed those sentences become.

Which means the bottleneck stops being thinking. It becomes typing.

## The typing tax

A good Claude Code prompt is two or three sentences of context plus the actual ask. Type that out and you're looking at 15 to 30 seconds of keyboard time per prompt, dozens of times a day. It's not the typing speed that hurts. It's that you've already *said the whole thing in your head* and now you have to transcribe yourself.

Speaking it is roughly 3x faster than typing it, and it matches how the thought arrived.

## Hold a key, speak your prompt

That's the whole interaction. With Invoke running, you hold a hotkey, say your prompt out loud, and release. It transcribes locally on your GPU and pastes the text right into the Claude Code terminal at your cursor. Sub-second. You keep your hands on the keyboard for the hotkey and never touch the mouse.

No window switching, no separate dictation box, no "send to clipboard then paste." It lands where you're already working.

## Why the reformatter matters here specifically

Raw dictation has filler: "um," "like," restarts, "actually no, do the other thing." For most apps that's fine. For a Claude Code prompt, a cleaner input gets you a cleaner result.

Invoke's reformatter cleans that up *and* uses your project context. It reads your `CLAUDE.md`, `package.json`, and git history, so it understands the codebase you're in. Say "wire the post endpoint to the user off table" and it knows you meant the **auth** table, because it can see your schema. You get a tight, correct prompt instead of a transcript of you thinking out loud.

{% include cta.html %}

## Local means your code context stays yours

This is the part that matters when you're dictating about a private codebase. Invoke transcribes on your machine. Your audio never gets uploaded to a transcription service. The prompts you speak about your proprietary code don't leave your computer. For anyone under an NDA or working on something unreleased, that's not a nice-to-have.

It pairs well with the keyboard-only Claude Code workflow. See also [pasting screenshots into the Claude Code terminal](/learn/screenshot-paste-claude-code-terminal/).

## Setup

1. [Download Invoke](/download/) and run the installer (Windows).
2. Pick a hotkey in the tray. I use `Ctrl+Shift+D`, mouse-button bindings work too.
3. Optional: connect the reformatter to Claude CLI, OpenRouter, or a local Ollama model.
4. Open Claude Code, hold the key, talk. The prompt appears in the terminal.

It works the same way for Cursor and any other tool. I wrote up the [Cursor voice dictation](/learn/voice-dictation-cursor-ai/) setup separately, and the bigger picture in [how I dictate code all day](/learn/how-i-dictate-code-all-day/).

## Try it

7-day free trial, no credit card. [Download here](/download/).
