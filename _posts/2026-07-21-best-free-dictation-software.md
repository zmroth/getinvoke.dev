---
title: "Best Free Dictation Software (2026)"
date: 2026-07-21
author: Zach Roth
layout: post
excerpt: "There are genuinely good free dictation tools, and for most people one of them is enough. Here's an honest ranking of what's free, what each one costs you in other ways, and when paying starts to make sense."
og_image: og-image.png
---

## Free dictation is better than it used to be

I sell a paid dictation app, so treat the recommendations here with that in mind. But I'd rather tell you the truth, which is that several free options are good and a lot of people never need to spend anything.

The catch is that "free" means different things. Some of these are free because they're open source. Some are free because you're paying with your audio. Some are free up to a cap. Those are very different deals and the difference matters more than the price tag.

Here's what actually works, roughly in order of how many people it suits.

## Windows voice typing

Press `Win + H` on Windows 11 and start talking. It's built into the operating system, it costs nothing, and it works in any text field.

It's genuinely decent for ordinary prose. Email, documents, chat messages. Punctuation commands work, and setup is zero.

The limits show up fast if you're technical. It runs on Microsoft's cloud speech service, so your audio leaves your machine. There's no way to add custom vocabulary, which means technical terms get mangled and stay mangled. And there's no push to talk, so you toggle it on, speak, toggle it off, which breaks the rhythm if you're dictating in short bursts.

For most people writing normal sentences, this is the answer and you can stop reading.

## Google Docs voice typing

Tools, then Voice typing, in Chrome. Free, accurate, and good at punctuation.

The restriction is the obvious one: it only works inside Google Docs. If your work happens in an editor, a terminal, or a chat window, you're dictating into a Doc and then copying it out, which is a worse workflow than typing for anything short.

Fine for drafting long prose. Useless as a general dictation setup.

## Whisper, self-hosted

This is the most accurate free option by a wide margin, and it's the one most people don't realize is available.

Whisper is OpenAI's speech recognition model, released open source under the MIT license. You download the weights once and run them on your own hardware. No account, no subscription, no audio leaving your machine, no usage caps. The accuracy is the same as what paid cloud services get, because most of them are running these same weights.

The cost is your time. You're installing Python packages, picking a model size, and on Windows probably fighting CUDA libraries for an afternoon. What you end up with is file transcription: point it at audio, get text back.

If you want to go this route, I wrote up [how to run Whisper locally on Windows](/learn/run-whisper-locally-windows/) with the setup and the errors you'll hit. For the runtime choice, [whisper.cpp vs faster-whisper](/learn/whisper-cpp-vs-faster-whisper/) covers which one fits your hardware. whisper.cpp in particular is a single binary with no Python at all, which makes it the easiest free option to actually live with.

## Talon Voice

Free, with a Patreon for early access to some builds. Talon is a full voice control system rather than a dictation tool. You learn a grammar of spoken commands to move a cursor, select text, navigate an editor, and write code by voice.

The learning curve is real. Expect weeks, not an afternoon. But for developers with RSI or anyone who needs to work without a keyboard, it's the most capable thing in existence and nothing paid comes close. I compared it to what I build in [Talon Voice vs Invoke](/learn/talon-voice-vs-invoke/).

## Vosk

Open source, offline, and light enough to run on a Raspberry Pi. Accuracy is below Whisper, noticeably so on accents and technical vocabulary.

Worth knowing about if you're embedding speech recognition into something small, or you need a language Whisper handles poorly. Not what I'd pick for desktop dictation in 2026.

{% include cta.html %}

## What free actually costs you

Every option above is free in the sense that no money changes hands. They're not free of tradeoffs.

**Cloud tools cost you privacy.** Windows voice typing and Google Docs both send your audio to a server. That's a non-issue for a grocery list and a real issue if you're describing an unreleased product or reading out anything covered by an NDA. It's the reason I run everything locally, explained further in [local voice to text for coding](/learn/local-voice-to-text-coding/).

**Self-hosted tools cost you time.** Whisper is free the way a pile of lumber is a free bookshelf. The model is the easy part. The hours go into setup, and then into the realization that file transcription isn't dictation.

**Free tiers cost you a cap.** Anything with a minutes limit is a countdown, and the limit always arrives during something you actually cared about.

## When free is genuinely enough

If you dictate occasionally, write mostly normal English, and don't care that your audio goes to Microsoft or Google, `Win + H` is fine. Use it. You do not need my product.

If you're comfortable in a terminal and you're transcribing recordings rather than dictating live, self-hosted Whisper is excellent and costs nothing.

## When it stops being enough

Free stops working when you're doing this constantly and the friction compounds.

The specific failure is technical vocabulary. Every free general purpose tool turns "add a POST endpoint for user auth" into "add a post endpoint for user off," and there's no dictionary to fix it because these tools don't know anything about what you're working on. Fix that by hand fifty times a day and the tool has cost you more than it saved.

The second failure is the interaction. Toggling dictation on and off is fine for a paragraph and miserable for one sentence at a time, which is what prompting an AI tool actually looks like.

That's the gap I built [Invoke](/download/) for. It runs Whisper locally on your GPU, so it's private and there's no cap. You hold a hotkey, speak, release, and the text lands at your cursor in under a second. And it has an optional reformatter that reads your `CLAUDE.md`, `package.json` and git history, so it knows "user off" meant authentication because it can see your codebase.

It's $49 once rather than free, and there's a 7 day trial with no credit card so you can find out whether the difference is worth it to you. If `Win + H` covers your needs, genuinely, keep your money.

## The short version

| Tool | Cost | Local | Best for |
|---|---|---|---|
| Windows voice typing | Free | No | Most people, ordinary prose |
| Google Docs voice typing | Free | No | Long form drafting in Docs |
| Whisper self-hosted | Free | Yes | Technical users, best free accuracy |
| whisper.cpp | Free | Yes | Free option with no Python |
| Talon Voice | Free | Yes | Full voice control, RSI |
| Vosk | Free | Yes | Embedded and low power |
| Invoke | $49 once | Yes | Developers dictating all day |

## Try it

If you've already tried the free options and hit the technical vocabulary wall, that's exactly what Invoke fixes. Free 7-day trial, no credit card. [Download here](/download/).
