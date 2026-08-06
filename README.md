# Cinesalve

**Recover a video recording your camera never finished writing.** macOS app.

A flat battery, a pulled card or a crash leaves every frame on the card and only
the index missing. Players report `moov atom not found` and refuse to open the
file. Cinesalve reads a healthy clip from the same camera to learn how that
camera writes, then rebuilds the index of the damaged one.

- **Watch the recovery before paying.** The repair runs and plays in full for
  free. A licence is needed only to save the repaired file.
- **Nothing is uploaded.** Footage never leaves your Mac. No account, no
  telemetry, works offline.
- **One-time $69.** No subscription.

[Download](https://github.com/LucideLarp/cinesalve/releases/latest) ·
[cinesalve@proton.me](mailto:cinesalve@proton.me)

**Not sure your file is recoverable?**
[What "moov atom not found" means and how to get the footage back](https://lucidelarp.github.io/cinesalve/moov-atom-not-found.html)
shows how to check in one command, why copying an index from another file cannot
work, and what your options are including the free ones.

## What it handles

H.264 and HEVC video with AAC audio, in MP4 and MOV containers, which is what
consumer and prosumer cameras record. It repairs recordings that were
interrupted. It cannot invent footage that was never written to the card, and it
does not handle ProRes, MXF or AVI.

## How exact is it

A repair counts only when a recovered frame lands at the same byte offset and the
same size the camera originally wrote. On that measure video comes back exact,
and a frame decoded from a repaired file is byte-identical to the same frame in
the original.

| test case | video frames exact | audio frames exact |
|---|---|---|
| H.264 720p 30fps, cut at 30% | 90 / 90 (100%) | 98.5% |
| H.264 720p 30fps, cut at 50% | 149 / 149 (100%) | 99.1% |
| H.264 720p 30fps, cut at 68% | 203 / 203 (100%) | 99.3% |
| H.264 720p 30fps, cut at 90% | 269 / 269 (100%) | 99.5% |
| H.264 1080p 24fps, cut at 55% | 107 / 107 (100%) | 99.0% |
| H.264 480p 60fps, cut at 45% | 216 / 216 (100%) | 100% |
| H.264 without audio, cut at 60% | 143 / 143 (100%) | — |
| HEVC 720p 30fps, cut at 60% | 146 / 146 (100%) | 98.6% |

**These figures come from files encoded on a computer, not from a real camera.**
Footage from a DJI, GoPro, Sony, Canon or iPhone has not been tested yet, and
those write containers with quirks of their own. Send a damaged clip to
[cinesalve@proton.me](mailto:cinesalve@proton.me) or open an issue, and you will
get a straight answer about whether it can be recovered before any money changes
hands.

## Installing

Download the zip, unzip, drag `Cinesalve.app` to Applications. The app is not yet
signed with an Apple developer certificate, so the first launch needs a
right-click then **Open** rather than a double click. Once only.

macOS 13 or later. Universal binary, Apple silicon and Intel.

## Getting help

Open an issue here, or email [cinesalve@proton.me](mailto:cinesalve@proton.me).
Attach the output of the app's failure message and say which camera and recording
mode produced the file.
