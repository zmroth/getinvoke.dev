---
title: "How to Run Whisper Locally on Windows"
date: 2026-07-21
author: Zach Roth
layout: post
excerpt: "Whisper runs entirely on your own machine, no API key and no internet. Here's how to get it running on Windows, which runtime to pick, and how to fix the CUDA errors everyone hits."
og_image: og-image.png
---

## What running Whisper locally actually means

Whisper is a speech recognition model OpenAI released in 2022 and open sourced under the MIT license. The weights are public. You download them once, and after that transcription happens on your own hardware. No API key, no per minute billing, no audio leaving your machine.

That last part is the reason most developers want it. If you dictate anything about a private codebase, cloud transcription means your internal service names and file paths are sitting on someone else's server.

The confusing part is that "Whisper" refers to two different things. There's the model, which is just a pile of weights. And there's the runtime that loads those weights and does the math. You need both, and the runtime you pick matters more than most guides admit.

## Pick your runtime first

Three real options. They all run the same model and produce the same transcription quality, but they are not interchangeable in practice.

| Runtime | Best for | Speed | Install pain on Windows |
|---|---|---|---|
| `openai-whisper` | Reference implementation, matching the paper | Slowest | Low |
| `faster-whisper` | GPU users who want speed | Fastest on NVIDIA | Medium, CUDA deps |
| `whisper.cpp` | CPU only machines, no Python | Good on CPU | Medium, C++ build |

The original `openai-whisper` package is the reference implementation. It works, and it's the easiest to install. It's also roughly four times slower than the alternatives and uses far more memory, because it was written to be correct and readable rather than fast.

`faster-whisper` reimplements the same model on CTranslate2, which is an inference engine built for transformer models. Same weights, same output, much less VRAM. This is what I use and what Invoke ships.

`whisper.cpp` is a C++ port with no Python dependency. If you have no GPU, or you want a single binary you can drop on a machine, this is the one. It's also the best option on Apple Silicon.

## The fastest path on Windows

Assuming you have an NVIDIA card, this is the shortest route to working transcription.

Install the package:

```
pip install faster-whisper
```

Then install the GPU libraries. This is the step almost every guide skips, and it's why so many people end up with a CUDA error:

```
pip install nvidia-cublas-cu12 nvidia-cudnn-cu12
```

Now transcribe something:

```python
from faster_whisper import WhisperModel

model = WhisperModel("distil-large-v3", device="cuda", compute_type="float16")

segments, info = model.transcribe("audio.wav", beam_size=5)

print(f"Detected language: {info.language}")
for segment in segments:
    print(f"[{segment.start:.2f}s -> {segment.end:.2f}s] {segment.text}")
```

One gotcha that trips people up. `segments` is a generator, not a list. Nothing actually gets transcribed until you iterate over it. If you call `transcribe()` and it returns instantly, that's why. The work happens in the loop.

If you don't have an NVIDIA card, change two arguments:

```python
model = WhisperModel("base", device="cpu", compute_type="int8")
```

## Which model to use

This is where people waste the most time. Bigger is not automatically better for your use case.

| Model | Parameters | VRAM at float16 | Notes |
|---|---|---|---|
| tiny | 39M | ~1 GB | Fast, makes mistakes on technical words |
| base | 74M | ~1 GB | Reasonable floor for CPU use |
| small | 244M | ~2 GB | Good balance on modest GPUs |
| medium | 769M | ~5 GB | Diminishing returns for most speech |
| large-v3 | 1.55B | ~10 GB | Best accuracy, slowest |
| distil-large-v3 | 756M | ~6 GB | Close to large-v3 accuracy, several times faster |

Those VRAM numbers are approximate and depend on your compute type. Switching `compute_type` to `int8` roughly halves them, at a small accuracy cost you probably won't notice for dictation.

My recommendation for dictation on a decent GPU is `distil-large-v3`. It's a distilled version of large-v3 that keeps most of the accuracy while running much faster. For short utterances, which is what dictation is, it's hard to tell apart from the full model. That's what Invoke defaults to.

If you're transcribing long recordings where accuracy matters more than speed, use `large-v3` and go get a coffee.

{% include cta.html %}

## The CUDA error everyone hits

If you get something like this:

```
Could not locate cudnn_ops64_9.dll. Please make sure it is in your library path!
```

or a `cublas64_12.dll` version of the same message, your Python can see faster-whisper but not the CUDA libraries it depends on. CTranslate2 needs cuBLAS and cuDNN at runtime, and on Windows they are not on your path by default.

The fix is usually just the pip packages:

```
pip install nvidia-cublas-cu12 nvidia-cudnn-cu12
```

If that doesn't do it, the usual causes are a version mismatch between your CTranslate2 version and your CUDA major version, or an old system wide CUDA toolkit install shadowing the pip provided DLLs. Check what you actually have:

```python
import ctranslate2
print(ctranslate2.__version__)
print(ctranslate2.get_cuda_device_count())
```

If `get_cuda_device_count()` returns 0, the library is not seeing your GPU at all, and no amount of changing model names will help.

I spent an afternoon on this exact problem. It's the single biggest reason people give up on local Whisper and go back to a cloud service, which is a shame because the fix is two pip packages.

## Does it work offline

Yes, with one caveat. The first time you load a model, it downloads the weights from Hugging Face. After that it's cached and you never need a connection again.

On Windows the cache lives here:

```
C:\Users\<you>\.cache\huggingface\hub
```

If you're setting up a machine that will never have internet, download the model on a connected machine first and copy that folder over. Airplane mode, air gapped networks, and locked down corporate laptops all work fine once the weights are local.

## What about CPU only

It works. It's just slower.

On CPU with `int8`, expect a few seconds for a short utterance instead of well under one. That sounds tolerable until you do it a hundred times a day, at which point the pause starts to break your concentration. If CPU is your only option, use a smaller model and `whisper.cpp`, which is better optimized for it than the Python runtimes.

The accuracy is identical. The cloud services are not running a secret better model. They're running these same weights on bigger hardware.

## From a script to an actual workflow

Here's the honest limitation of everything above. What you have now is file transcription. You point it at a `.wav` and get text back.

That is not dictation. Dictation means holding a key, talking, and having the text land where your cursor already is. To get from one to the other you need audio capture, a global hotkey listener, a way to keep the model resident in VRAM so you're not paying the load cost every time, and clipboard or keystroke injection to place the result.

That's a few hundred lines of glue, and it's exactly the wall I hit. I wrote that glue, kept adding to it, and it turned into [Invoke](/download/). It runs faster-whisper on your GPU with the CUDA libraries already bundled, so the error above never happens. You hold a hotkey, speak, release, and the text appears at your cursor in under a second.

If you'd rather not build it, I compared [the Whisper desktop apps for Windows](/learn/whisper-desktop-app-windows/) separately. If you want the reasoning behind running any of this locally instead of in the cloud, that's in [local voice to text for coding](/learn/local-voice-to-text-coding/).

## Try it

Free 7-day trial, no credit card. [Download here](/download/).
