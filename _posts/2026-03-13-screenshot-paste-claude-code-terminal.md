---
title: "How to Paste Screenshots into Claude Code from Any Terminal"
date: 2026-03-13
author: Zach Roth
layout: post
excerpt: "Terminal emulators like Hyper, Wezterm, and Windows Terminal can't paste images into Claude Code. Invoke's screenshot-to-path feature fixes that with one hotkey."
og_image: og-image.png
---

## The problem: Ctrl+V doesn't paste images in terminals

If you've tried pasting a screenshot into Claude Code running in a terminal emulator, you know the pain. You hit Win+Shift+S, snip something, switch to your terminal, press Ctrl+V, and... nothing. Or you get "no image found in clipboard."

This isn't a Claude Code bug. It's a terminal limitation.

Terminals like Hyper, Wezterm, and even Windows Terminal handle Ctrl+V as a **text paste** operation. When the clipboard contains an image (not text), the terminal either ignores it or passes nothing through. Claude Code never sees the image data.

On WSL2, it's even worse. The Wayland clipboard bridge (WSLg) is unreliable, and tools like `xclip` and `wl-paste` often can't connect to the Wayland socket. Even if your terminal wanted to forward the image, the plumbing between Windows and WSL2 isn't there.

## Why this matters for developers

Claude Code accepts images as visual input. You can paste screenshots of error messages, UI mockups, terminal output, or architecture diagrams and Claude will analyze them. It's one of the most useful features for debugging and code review.

But if you can't paste images, you're stuck with workarounds:

- Save the screenshot manually to a file
- Find where it saved
- Type or paste the full file path into Claude Code
- Hope you got the path right

That's four steps for something that should be one.

## The fix: screenshot-to-path

Invoke has a dedicated hotkey for this. Here's the workflow:

1. **Win+Shift+S** — snip whatever you want (error message, UI, diagram)
2. **Press your image hotkey** (default: Mouse4, configurable)
3. **Done** — Invoke saves the image as a PNG and pastes the file path into your terminal

Claude Code receives something like `/home/you/.screenshots/clip_20260313_143022.png` and renders the image inline. One hotkey, zero friction.

## How it works under the hood

When you press the image hotkey, Invoke:

1. Grabs the image from the Windows clipboard via PowerShell (bypasses the broken WSLg/Wayland path entirely)
2. Saves it as a timestamped PNG to `~/.screenshots/`
3. Puts the file path string on your clipboard
4. Auto-pastes it into your terminal (if auto-paste is enabled)

The PowerShell approach is deliberate. WSLg clipboard bridging is unreliable — the Wayland socket is often missing, and `wl-paste` fails silently. PowerShell has direct access to the Windows clipboard and works every time. The subprocess call adds about 500ms, which is fine for a non-realtime operation.

## It works in every terminal

Since Invoke pastes a **text string** (the file path), it works in any terminal emulator:

- **Hyper** — works
- **Wezterm** — works
- **Windows Terminal** — works
- **VS Code integrated terminal** — works (though VS Code can also paste images natively)
- **Alacritty, Kitty, any other terminal** — works

No terminal-specific configuration needed. No image protocol support required. Just text.

## Setup

Install or update Invoke:

```
pip install --upgrade getinvoke
```

The image hotkey defaults to Mouse4 (the back thumb button). You can change it:

```
dictate config image_hotkey mouse5
```

Or use a keyboard combo:

```
dictate config image_hotkey ctrl+shift+s
```

The save directory defaults to `~/.screenshots/`. Images older than 30 days are automatically cleaned up.

## Beyond screenshots

This pairs well with Invoke's voice dictation. A typical workflow:

1. See a bug in the UI
2. **Win+Shift+S** to snip it
3. **Mouse4** to paste the image path into Claude Code
4. **Mouse5** (hold and speak): "This button should be aligned with the header. Fix the CSS in the navbar component."

Voice + visual context in two hotkey presses. That's the kind of input toolkit developers actually want.

{% include cta.html %}

## The bigger picture

Invoke started as a voice-to-text tool. But the real value isn't just dictation — it's **removing friction between your intent and your AI tools**. Screenshot-to-path is the second input method. Voice was the first. More are coming.

$49 once. [Free 7-day trial](/download). No subscription, no word limits, no image limits.
