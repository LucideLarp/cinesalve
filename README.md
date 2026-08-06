# Cinesalve

**Recover a video recording your camera never finished writing.** macOS app.

### [cinesalve.honorboxx.workers.dev](https://cinesalve.honorboxx.workers.dev)

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
[What "moov atom not found" means and how to get the footage back](https://cinesalve.honorboxx.workers.dev/moov-atom-not-found)
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

### Real camera footage

Genuine GoPro recordings, every frame exact, no decoder errors. Two kinds of
test, and they are not equally strong, so they are labelled.

*Separate take* means a different clip from the same camera was the reference.
That is the honest test, because it is the situation a user is actually in.
*Same clip* means the file's own undamaged version was the reference: it proves
Cinesalve handles that camera's container and telemetry, but it is the easier
case, and nobody who needs this app has the undamaged file to hand. Read those
rows as evidence about the camera, not as a promise about your clip.

| real camera | reference used | frames recovered | exact |
|---|---|---|---|
| HERO6, cut at 60% | separate take | 425 / 425 | 100% |
| HERO6, cut at 85% | separate take | 603 / 603 | 100% |
| QuickTime, written by Apple's own muxer | separate take | 420 / 420 | 100% |
| HERO5 | same clip | 456 / 456 | 100% |
| HERO7 | same clip | 210 / 210 | 100% |
| HERO8 | same clip | 210 / 210 | 100% |
| Karma | same clip | 200 / 200 | 100% |
| Fusion | same clip | 248 / 248 | 100% |
| MAX | same clip | 175 / 175 | 100% |

**Sound is the weaker half, and you hear exactly what you get before paying.**
On GoPro footage it is left out entirely: these cameras write timecode and
telemetry into the same block as the picture, and those bytes cannot be told
apart from audio confidently enough to place it. On simpler recordings sound
does come back, though roughly one frame in ten can land imperfectly and leave
an occasional click. Picture is exact either way.

**GoPro and DJI hardware are tested. Sony, Canon and iPhone are not** and have
not been through the suite yet. Send a damaged clip to
[cinesalve@proton.me](mailto:cinesalve@proton.me) or open an issue, and you will
get a straight answer about whether it can be recovered before any money changes
hands.

## Installing

Download the zip, unzip, drag `Cinesalve.app` to Applications.

It is not yet signed with an Apple developer certificate, so macOS refuses it the
first time. One step, once:

1. Double-click Cinesalve. macOS says it cannot be opened.
2. Open **System Settings → Privacy & Security**, scroll to the bottom, click
   **Open Anyway** next to Cinesalve.
3. Confirm.

Control-clicking and choosing Open does *not* work: Apple removed that shortcut
in macOS 15. The terminal equivalent is one line:

```
xattr -dr com.apple.quarantine /Applications/Cinesalve.app
```

macOS 13 or later. Universal binary, Apple silicon and Intel.

## Getting help

Open an issue here, or email [cinesalve@proton.me](mailto:cinesalve@proton.me).
Attach the output of the app's failure message and say which camera and recording
mode produced the file.
