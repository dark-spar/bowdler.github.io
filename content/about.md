---
title: "About"
description: "Why bowdler exists, what it isn't, and the eponymous Englishman."
---

## Who was Thomas Bowdler?

Thomas Bowdler (1754–1825) was an English physician who, in 1818, published
*The Family Shakespeare* — an edition of Shakespeare's plays with the bawdy
language and risqué passages excised, so that "nothing is added to the
original text; but those words and expressions are omitted which cannot with
propriety be read aloud in a family."

The verb **bowdlerize** is named after him. It's not always a compliment — and
the editors of *The Family Shakespeare* were criticized at the time and after.
But the underlying idea is durable: people sometimes want to share media with
their families that, in the original, contains content they'd rather skip.

This project is the same idea, two centuries later, applied to video files
on your own hard drive.

## What this is

A local, GPU-accelerated tool that detects nudity and profanity in video
files and produces a family-friendly version by **blurring** sensitive
regions and **muting** offending words.

It runs entirely on your own machine. Frames stay in CUDA memory from
NVDEC decode through inference through the blur compositor. No upload, no
cloud inference, no telemetry.

## What this isn't

- **Not a service.** No web UI, no API, no SaaS. The CLI is the whole product.
- **Not a distribution platform.** It does not strip DRM, redistribute media,
  or alter anything you don't already own.
- **Not for redistribution.** The output is for personal viewing in your own
  household. You hold the copyright on neither the input nor the output.
- **Not a perfect filter.** Detection is good but not infallible. The
  `review` step is a first-class part of the workflow precisely because
  human judgment beats a 320×320 ONNX model on edge cases.

## Personal use only

bowdler is published as personal-use software. No license is granted for
redistribution, hosting as a service, or commercial use.

If you're reading this and want to build something similar for your own
household — go for it. If you want to wrap it in a paid service, please
don't.
