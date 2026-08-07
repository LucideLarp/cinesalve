# Cinesalve

**Recover a video recording your camera never finished writing.** macOS app.

### [cinesalve.honorboxx.workers.dev](https://cinesalve.honorboxx.workers.dev)

A flat battery, a pulled card or a crash leaves every frame on the card and only
the index missing. Players report `moov atom not found` and refuse to open the
file. Cinesalve rebuilds the index and the recording plays again.

- **Every frame exact.** A recovered frame lands at the same byte offset and the
  same size the camera originally wrote, and decodes byte-identical to the
  original.
- **GoPro needs nothing but the broken file.** Those recordings carry their own
  codec configuration, and Cinesalve reads it from the damaged file itself.
- **Nothing is uploaded.** Footage never leaves your Mac. No account, no
  telemetry, works offline.
- **One-time $69.** Every Mac you own, for good. No subscription.

[Download](https://github.com/LucideLarp/cinesalve/releases/latest) ·
[cinesalve@proton.me](mailto:cinesalve@proton.me)

**Check your file free, in your browser:**
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads your damaged file and reports exactly how many frames are recoverable and
how many seconds that is. Nothing is uploaded — it reads only the first and last
megabyte — and it runs on any computer, Windows included.

**More on the failure itself:**
[What "moov atom not found" means and how to get the footage back](https://cinesalve.honorboxx.workers.dev/moov-atom-not-found).

## Recovered from the damaged file alone

No reference clip, nothing but the recording that will not play. Every sample
exact against the intact original, the codec configuration reconstructed
byte-identical to the real one, and zero decode errors from ffmpeg reading the
result end to end.

| real camera | frames recovered | exact | decode errors |
|---|---|---|---|
| GoPro HERO5 | 456 / 456 | 100% | 0 |
| GoPro HERO6 | 425 / 425 | 100% | 0 |
| GoPro HERO6, second take | 263 / 263 | 100% | 0 |
| GoPro HERO7 | 170 / 170 | 100% | 0 |
| GoPro HERO8 | 323 / 323 | 100% | 0 |
| GoPro Karma | 200 / 200 | 100% | 0 |
| GoPro Fusion | 160 / 160 | 100% | 0 |
| GoPro MAX | 175 / 175 | 100% | 0 |
| GoPro HERO6 with BLE | 181 / 181 | 100% | 0 |

## Recovered with a clip from the same camera

Recordings that do not state their own configuration are detected automatically
and read it from any working clip you have, which can be a few seconds recorded
on the spot.

| real camera | frames recovered | exact |
|---|---|---|
| DJI Mavic 3 Pro, 4K | 722 / 722 | 100% |
| QuickTime, Apple's own muxer | 420 / 420 | 100% |
| GoPro HERO6, cut at 85% | 603 / 603 | 100% |

## Synthetic suite

Cut at known points so the original is available to compare against, frame by
frame.

| test case | video frames exact | audio frames exact |
|---|---|---|
| H.264 720p 30fps, cut at 68% | 203 / 203 (100%) | 99.3% |
| H.264 720p 30fps, cut at 90% | 269 / 269 (100%) | 99.5% |
| H.264 1080p 24fps, cut at 55% | 107 / 107 (100%) | 99.0% |
| H.264 480p 60fps, cut at 45% | 216 / 216 (100%) | 100% |
| H.264 without audio, cut at 60% | 143 / 143 (100%) | — |
| HEVC 720p 30fps, cut at 60% | 146 / 146 (100%) | 98.6% |

## What it recovers

Picture, exactly, on H.264 and HEVC in MP4 and MOV — what consumer and prosumer
cameras record.

Sound comes back on recordings that keep audio in its own block. Action cameras
write timecode and telemetry into the same block as the picture, and Cinesalve
recovers the picture from those rather than writing bytes it cannot verify into
your recording.

## Installing

Download the zip, unzip, drag `Cinesalve.app` to Applications. macOS 13 or later,
universal binary, Apple silicon and Intel.

First launch needs one approval, once:

1. Double-click Cinesalve.
2. Open **System Settings → Privacy & Security**, scroll to the bottom, click
   **Open Anyway** next to Cinesalve.
3. Confirm.

Control-clicking and choosing Open does not work — Apple removed that shortcut in
macOS 15. The terminal equivalent is one line:

```
xattr -dr com.apple.quarantine /Applications/Cinesalve.app
```

Or install with Homebrew, which handles upgrades:

```
brew install --cask lucidelarp/cinesalve/cinesalve
xattr -dr com.apple.quarantine /Applications/Cinesalve.app
```

## Getting help

Open an issue here, or email [cinesalve@proton.me](mailto:cinesalve@proton.me).
Say which camera and recording mode produced the file, and include the app's
message if it showed one.
