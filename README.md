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
  codec configuration, in the H.264 and the HEVC modes the range records, and
  Cinesalve reads it from the damaged file itself.
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

**More on the failure itself**

- [The power went and now the video will not play](docs/video-wont-play-after-power-loss.md)
- [What "moov atom not found" means and how to get the footage back](docs/moov-atom-not-found.md)
- [Repairing an MP4 without a reference file](docs/repair-mp4-without-reference-file.md)
- [Your GoPro file will not play](docs/gopro-video-wont-play.md)
- [Your Canon file will not play](docs/canon-video-wont-play.md)
- [Your iPhone video will not play](docs/iphone-video-wont-play.md)
- [OBS crashed and the recording will not play](docs/obs-recording-wont-play.md)
- [Getting untrunc working on a Mac](docs/untrunc-on-mac.md)

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
| GoPro HEVC, 1080p 59.94fps | 217 / 217 | 100% | 0 |
| GoPro HEVC, second take | 89 / 89 | 100% | 0 |

An iPhone gets there by a different route and keeps its sound. About ten seconds
into a take, iOS writes a complete index into the middle of the recording so that
a phone dropped mid-shot still has one; a take longer than that is recovered from
its own account of itself, with nothing searched for and nothing inferred.

| iPhone 11, no reference clip | frames | audio | decode errors |
|---|---|---|---|
| take cut at 50% | 297 / 297 | 431 / 431 | 0 |
| take cut at 60% | 297 / 297 | 431 / 431 | 0 |
| take cut at 85% | 596 / 596 | 862 / 862 | 0 |
| second take, cut at 85% | 300 / 300 | 431 / 431 | 0 |

An index stops at the last checkpoint the phone managed to write, so a reference
clip still recovers more where one is available: the same take cut at 60% gives
297 frames from the index alone and 484 with a clip. Cinesalve reports which
route it took and how much footage sits past the checkpoint, so the choice is
yours rather than silently made for you.

## Recovered with a clip from the same camera

Recordings that state neither their own configuration nor an index are detected
automatically and read it from any working clip you have, which can be a few
seconds recorded on the spot. The last column says what each measurement here
was made against: a separate take from the same camera, which is the situation
you would actually be in, or the recording's own untruncated self where only
one camera-original clip of that make is published anywhere to pair with.

| real camera | frames recovered | exact | reference |
|---|---|---|---|
| Sony FDR-AX100E, 4K | 1488 / 1488 | 100% | its own full recording |
| DJI Mavic 3 Pro, 4K | 722 / 722 | 100% | a different take |
| iPhone 11, HEVC | 484 / 484 | 100% | a different take |
| Canon EOS 5D Mark II | 208 / 208 | 100% | a different take |
| QuickTime, Apple's own muxer | 420 / 420 | 100% | a different take |
| 1080p consumer camera | 1356 / 1356 | 100% | a different take |
| GoPro HERO6, cut at 85% | 603 / 603 | 100% | a different take |

## Synthetic suite

Cut at known points so the original is available to compare against, frame by
frame.

| test case | video frames exact | audio frames exact |
|---|---|---|
| H.264 720p 30fps, cut at 68% | 203 / 203 (100%) | 99.3% |
| H.264 720p 30fps, cut at 90% | 269 / 269 (100%) | 99.5% |
| H.264 1080p 24fps, cut at 55% | 107 / 107 (100%) | 99.0% |
| H.264 480p 60fps, cut at 45% | 217 / 217 (100%) | 100% |
| H.264 without audio, cut at 60% | 144 / 144 (100%) | — |
| HEVC 720p 30fps, cut at 60% | 146 / 146 (100%) | 98.6% |

## What it recovers

Picture, exactly, on H.264 and HEVC in MP4 and MOV — what consumer and prosumer
cameras record.

Sound comes back on recordings that keep audio in its own block. Action cameras
write timecode and telemetry into the same block as the picture, and Cinesalve
recovers the picture from those rather than writing bytes it cannot verify into
your recording.

## If it does not recover your footage

You get your money back. Email [cinesalve@proton.me](mailto:cinesalve@proton.me)
with the licence key and a sentence about what happened, and the refund goes
through the same day.

That is an easy promise because you know the answer before you buy: the
[free checker](https://cinesalve.honorboxx.workers.dev/check) reads your own file
and reports how many frames are recoverable. Nobody should pay to find out
whether their footage survived.

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
