---
title: "faster-whisper: Why It's Faster and When to Use It"
date: 2026-07-21
author: Zach Roth
layout: post
excerpt: "faster-whisper runs the same Whisper weights about four times faster on less memory. Here's the actual reason why, which settings matter, and what I learned shipping it in a production app."
og_image: og-image.png
---

## What faster-whisper actually is

faster-whisper is a reimplementation of OpenAI's Whisper that runs on [CTranslate2](https://github.com/OpenNMT/CTranslate2) instead of PyTorch. Same model weights, same transcription output, roughly four times the speed on less memory.

That sounds like a minor optimization. It isn't. The gap between the reference implementation and this one decides whether local transcription feels instant or feels like waiting, and that difference is the whole reason local dictation is viable at all.

I ship it inside Invoke, a paid Windows dictation app, so I've had to care about the parts that never come up in a quickstart. This is what I've learned.

## Why it's faster

The model is identical. Nobody retrained anything. All of the speed comes from how the math gets executed.

The reference `openai-whisper` package is PyTorch, written to be readable and to match the paper. Every forward pass goes through Python, builds a graph, and allocates tensors as it goes. That overhead is irrelevant when you're running research experiments. It's waste when you're running the same fixed model shape ten thousand times.

CTranslate2 is a C++ inference engine built specifically for transformer models. A few things it does that PyTorch doesn't do by default:

Weights get quantized, so an int8 model moves a quarter of the bytes a float32 one does. Inference is usually limited by memory bandwidth rather than raw compute, so this matters more than it sounds like it should.

Operations get fused. Instead of a matrix multiply, then a bias add, then an activation as three separate kernel launches, it does them in one pass. Fewer round trips to memory.

Memory gets reused. Buffers are allocated once and written over, rather than allocated and freed per call.

And the inner loop is compiled C++ with no Python in it at all.

None of that changes what the model computes. It changes how efficiently the hardware gets to do it.

## How much faster

The project claims up to four times faster than `openai-whisper` at the same accuracy while using less memory. That matches what I see closely enough that I've never bothered benchmarking it more rigorously.

In Invoke, a short dictation utterance on an NVIDIA card comes back in well under a second. The model itself takes about two seconds to load into VRAM at startup. That startup number is the important one, and I'll come back to it.

## Getting it running

```
pip install faster-whisper
pip install nvidia-cublas-cu12 nvidia-cudnn-cu12
```

```python
from faster_whisper import WhisperModel

model = WhisperModel("distil-large-v3", device="cuda", compute_type="float16")
segments, info = model.transcribe("audio.wav", vad_filter=True)

for segment in segments:
    print(segment.text)
```

If that throws a `cudnn_ops64_9.dll` or `cublas64_12.dll` error, the problem is your CUDA libraries, not your code. Full fix in [how to run Whisper locally on Windows](/learn/run-whisper-locally-windows/).

## compute_type is the knob most people ignore

Everyone tunes the model size. Almost nobody touches `compute_type`, and it moves speed and memory just as much.

On an NVIDIA card you'll typically have these available:

```
float32, float16, bfloat16, int8, int8_float16, int8_float32, int8_bfloat16
```

`float16` is the sensible default on any reasonably modern GPU. Half the memory of float32, no accuracy difference you'll notice on speech.

`int8` roughly halves it again. On short dictation clips I genuinely cannot tell the output apart from float16. On long recordings with accents or background noise, I can occasionally catch it dropping a word.

`int8_float16` sits in between. Weights stored as int8, math done in float16.

On CPU, use `int8`. Nothing else is worth running.

The short version: start at `float16` on GPU and `int8` on CPU, and only move if you're short on VRAM.

{% include cta.html %}

## The VAD filter matters more than you would think

Whisper hallucinates on silence. Feed it a clip that's mostly nothing and it will confidently produce text nobody said. Usually a stray "Thank you." or a subtitle credit line it absorbed from training data.

For file transcription that's an annoyance. For push to talk dictation, where clips routinely start and end with a bit of dead air, it's a real problem. Nothing erodes trust in a dictation tool faster than words appearing that you never spoke.

Passing `vad_filter=True` runs voice activity detection first and strips silence before the audio ever reaches the model. You can watch it work in Invoke's logs:

```
Processing audio with duration 00:02.100
VAD filter removed 00:00.848 of audio
```

That's 40% of the clip discarded before transcription, which is both faster and more accurate. Turn it on. I can't think of a dictation case where you'd want it off.

## Which model to pair it with

`distil-large-v3` for dictation. It's a distilled large-v3 that keeps most of the accuracy at a fraction of the compute. On short utterances the difference is hard to detect, and it's what Invoke ships by default.

Use full `large-v3` when you're transcribing long recordings and accuracy beats latency. Use `base` or `small` on CPU or a small GPU.

There's a fuller breakdown of sizes and VRAM in [the local Whisper guide](/learn/run-whisper-locally-windows/).

## Four things I hit shipping it

**`transcribe()` returns a generator.** It hands back `segments` immediately and does no work until you iterate. The first time you time this function you'll think it's impossibly fast, then realize the cost just moved into your for loop.

**Model load is expensive, transcription is cheap.** Loading weights into VRAM takes seconds. Transcribing a short clip takes a fraction of one. If you're building anything interactive, load the model once at startup and keep it resident. Reloading per request means every single user interaction pays the worst cost in the pipeline.

**The CUDA libraries are your deployment problem, not your user's.** Telling a developer to pip install two nvidia packages is fine. Telling a paying customer that is not. Invoke bundles `cublas64_12.dll`, `cublasLt64_12.dll` and `cudart64_12.dll` in the install directory so the GPU path just works on a clean machine. It added a few hundred megabytes to the installer and removed the single most common support question I had.

**`word_timestamps=True` is not free.** Useful if you need per word alignment for subtitles or highlighting. If you only want the text, leave it off.

## When not to use it

If you have no GPU, [whisper.cpp](https://github.com/ggerganov/whisper.cpp) is better optimized for CPU and ships as a single binary with no Python at all.

On Apple Silicon, whisper.cpp with Metal or an MLX based runtime will beat it.

And if you transcribe a file once a week, the reference `openai-whisper` package installs more easily and the speed difference will never matter to you.

faster-whisper wins when you're on NVIDIA, you care about latency, and you're transcribing constantly rather than occasionally. Which is a precise description of dictation.

## Try it

If you want faster-whisper running as a real push to talk app instead of a script, with the CUDA libraries already handled and the model kept warm in VRAM, that's what I built [Invoke](/download/) for. Hold a hotkey, speak, release, and the text lands at your cursor.

Free 7-day trial, no credit card. [Download here](/download/).
