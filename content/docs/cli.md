---
title: "CLI reference"
description: "The four subcommands: detect, review, render, and the all-in-one run."
weight: 20
---

bowdler is shaped around four subcommands. Detection is expensive; rendering is
cheap. The split lets you re-tune thresholds, re-render, and review by hand
without re-inferring.

## `bowdler detect <input>`

Runs vision + audio detection and emits an `EditDecisionList` JSON to stdout
(or `--output <path>`). Caches the result keyed by file content hash.

```bash
bowdler detect movie.mkv -o movie.edl.json
```

Cache hits skip detection entirely after the BLAKE3 hash. Re-detection is
forced with `--rebuild`; the cache is bypassed entirely with `--no-cache`.

**Knobs:** `--vision-model`, `--whisper-model`, `--wordlist`,
`--min-word-confidence`, `--no-video`, `--no-audio`.

## `bowdler review <input>`

TUI for stepping through cached detections.

```bash
bowdler review movie.mkv
```

| Key | Action |
|---|---|
| `j` / `↓`, `k` / `↑` | Navigate item list |
| `space` | Toggle approval on current |
| `a` / `r` | Approve / reject explicitly |
| `t` | Cycle the inline thumbnail through start / middle / end of segment |
| `q` | Save approvals to cache and quit |
| `Q` | Discard changes and quit |

Thumbnails render inline in the detail pane via `ratatui-image` — kitty
graphics protocol when supported (kitty / ghostty / wezterm), iTerm2 protocol
on macOS, sixel where available, halfblock fallback otherwise. PNGs also land
in `~/.cache/bowdler/thumbnails/<file_id>/` for offline previewing.

## `bowdler render <input> -o <output>`

Reads the cached EDL and approvals, runs a single-pass FFmpeg `filter_complex`
graph that boxblurs each approved region for its time range and silences each
approved word.

```bash
bowdler render movie.mkv -o movie.bowdler.mkv
```

If you skipped `review`, every detection is approved by default. Tune with
`--boxblur-radius` (default `15`) and `--video-crf` (default `23`).

## `bowdler run <input> -o <output>`

One-shot: detect → auto-approve items above the confidence floors → render.
Items below the floor are auto-rejected with a warning; revisit them with
`bowdler review` if you disagree.

```bash
bowdler run movie.mkv -o movie.bowdler.mkv
```

**Knobs:** `--auto-blur-confidence` (default `0.85`),
`--auto-mute-confidence` (default `0.5`).

## Logging

Default level is `warn,bowdler=info`, which keeps the spinner UI clean while
surfacing per-stage breadcrumbs from bowdler's own crates. Override with
`BOWDLER_LOG`:

```bash
BOWDLER_LOG=debug bowdler detect movie.mkv
BOWDLER_LOG=info,ort=debug bowdler detect movie.mkv
```
