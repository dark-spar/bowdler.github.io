---
title: "Configuration"
description: "Optional TOML config and the wordlist."
weight: 30
---

## Config file

Optional TOML at `~/.config/bowdler/config.toml`. CLI flags override config
values; config values override built-in defaults.

```toml
[paths]
cache_dir     = "/mnt/big/bowdler-cache"
vision_model  = "/opt/models/nudenet-320n.onnx"
whisper_model = "/opt/models/whisper-medium.en.bin"
wordlist      = "/etc/bowdler/words.txt"
trt_cache     = "/opt/cache/trt"

[audio]
min_word_confidence = 0.05

[render]
boxblur_radius = 25
video_crf      = 20

[run]
auto_blur_confidence = 0.90
auto_mute_confidence = 0.40
```

Use `--config <path>` to override the default location. Unknown keys fail
loudly so typos don't silently no-op.

## Wordlist

Plain text, one word per line. `#` introduces an end-of-line comment. The
default list ships at `crates/bowdler-audio/src/default_words.txt`. Override
via the config file or `--wordlist` flag, or place your own at
`~/.config/bowdler/words.txt` — bowdler auto-loads that path when no other
source is given.

```text
# example
darn
heck
crud      # mild
```

## Known limitations

- **Single-pass renderer with one bounding box per segment.** Each blur segment
  becomes one rectangle covering the union of all per-frame boxes, active for
  the whole segment range. Fast-moving objects get over-blurred. Per-frame
  mask compositing is on the deferred list.
- **HDR tone mapping is approximate.** The 10-bit (P010) preprocess kernel
  treats samples as high-precision SDR — correct for "Main 10" SDR HEVC,
  produces a color-shifted but recognizable image for true HDR (BT.2020/PQ).
  Proper PQ → linear → tone-map → sRGB is a follow-up.
- **Whisper uses Vulkan, not CUDA.** Arch's GCC 16 trips nvcc on the C++20
  features whisper.cpp pulls in transitively. Vulkan compute on the
  RTX 4080 Super performs comparably; install `gcc13` from AUR and configure
  `CUDAHOSTCXX` if you want CUDA whisper specifically.
- **No tiled inference for high-res content.** NudeNet's 320×320 input means
  small detections at 4K are missed. Letterboxing preserves aspect ratio but
  not effective resolution.
- **Detection cache assumes single-process access.** RocksDB's per-process
  lock is exclusive; running two `bowdler` invocations against the same
  `--cache-dir` simultaneously will fail on the second.
