---
title: "Vibe Coding with Voice: Why Speaking Your Prompts Beats Typing Them"
date: 2026-03-15
author: Zach Roth
layout: post
excerpt: "Everyone talks about 150 WPM vs 40 WPM. Speed isn't the real win. The real win is that spoken prompts are actually better than typed ones."
og_image: og-image.png
---

## The speed argument is boring

You've heard it. "You speak at 150 words per minute and type at 40." Cool. Math checks out. Voice is faster. Article over.

Except speed isn't why I dictate every prompt. I dictate because the prompts come out *better*.

## How I actually use voice for coding

I sit down in the morning, open Cursor, and start working. When I need Claude or Cursor to do something, I hold my hotkey and talk. I don't plan what I'm going to say. I just explain what I want the way I'd explain it to another developer sitting next to me.

"Hey, the auth middleware is checking the JWT expiry but it's not handling the refresh token rotation. Can you add a refresh endpoint at /api/auth/refresh that takes the old refresh token, validates it, issues a new pair, and invalidates the old one? Use the same token signing config we have in auth.config.ts."

That took about eight seconds to say. If I had typed it, I would have spent 30-45 seconds and probably written something shorter and worse. Something like "add jwt refresh token rotation to auth middleware." The AI would have made assumptions about the endpoint path, the config file, and whether to invalidate old tokens.

The spoken version is better because I didn't have to think about efficiency. I just talked.

## When you type, you edit as you go

This is the thing nobody talks about. When you type a prompt, you're simultaneously composing and editing. You write three words, delete one, rephrase, keep going. You trim things because typing is slow and you don't want to spend a minute on a prompt.

When you speak, you don't do that. You can't delete a word you already said (well, you can say "uh" and change direction, but you don't backspace). So you just keep going. You add context you wouldn't have typed. You mention the config file. You specify the endpoint path. You call out edge cases.

The messiness of speech is actually an advantage. My dictation tool takes that stream-of-consciousness ramble, strips the filler words, and structures it into something clean. The output has more detail than anything I would have typed.

{% include cta.html %}

## Specific examples

Here are three prompts from last week. The left side is what I said (raw transcription). The right side is what I would have typed if I'd been using my keyboard.

**Spoken (raw, before reformatting):**
"uh refactor the rate limiter to use a sliding window instead of fixed windows and make sure it handles the edge case where someone hits the limit right at the window boundary, also add a header that tells the client how many requests they have left"

**What I would have typed:**
"refactor rate limiter to sliding window"

The spoken version mentions the boundary edge case and the rate limit header. I wouldn't have thought to include those while typing because I was trying to keep it short. When I'm talking, I'm just thinking out loud and those details come naturally.

**Spoken:**
"this test is flaky because it depends on timing, the setTimeout in the retry logic races with the jest fake timer, can you rewrite it to use an event-based approach instead of polling, and check if any of the other tests in this file have the same pattern"

**What I would have typed:**
"fix flaky test in retry.test.ts, uses setTimeout that races with fake timers"

Again — the spoken version includes the fix approach (event-based instead of polling) and asks to check other tests for the same issue. The typed version just describes the symptom.

## The reformatter is what makes this work

Raw speech-to-text gives you a wall of text with filler words. That's not a good prompt either. "Uh" and "like" and "you know" need to go. The sentence structure needs cleanup.

Invoke's reformatter takes the raw transcription and restructures it. It reads your project context (what framework you're using, what files you've been editing) and outputs a prompt that references the right files and uses the right terminology. The spoken prompt gets the *content* right because you think out loud. The reformatter gets the *format* right because it knows your codebase.

You could use raw Whisper output and paste it directly. I've done it. It works okay. But the reformatted version consistently gets better results from the AI because it's structured and specific.

## It works in every AI tool

I use this in Cursor about 60% of the time. The rest is Claude Code in the terminal, Copilot in VS Code, and ChatGPT for non-coding stuff. Invoke pastes into whatever text field has focus. It's not a Cursor plugin, it's a system-level tool.

I've also started using it for commit messages and PR descriptions. Invoke has prompt modes for those — I hold the hotkey, describe what I changed, and it formats a conventional commit message or a PR with summary and test plan. Faster than writing them by hand and the output is more detailed.

## Getting started

If you already use AI coding tools and you're still typing every prompt, try speaking a few. You don't need Invoke to test the concept — hold down your phone and dictate into ChatGPT. Notice how much more context you include when you don't have to type it.

If you want the full workflow — push-to-talk, local Whisper, auto-paste, reformatting — [Invoke is $49 with a 7-day free trial](/download/).
