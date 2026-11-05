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
  Cinesalve reads it from the damaged file itself; the sound too, since the
  camera names every sample ahead of it and its own interleave states the rate.
- **Nothing is uploaded.** Footage never leaves your Mac. No account, no
  telemetry, works offline.
- **One-time $69.** Every Mac you own, for good. No subscription.

[Download](https://github.com/LucideLarp/cinesalve/releases/latest) ·
[cinesalve@proton.me](mailto:cinesalve@proton.me)

**Check your file free, in your browser:**
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads your damaged file and reports exactly how many frames are recoverable and
how many seconds that is. Nothing is uploaded — the verdict reads the first and
last megabyte, the frame count up to 100.7 MB of the footage and a reference clip
in full if you add one — and it runs on any computer, Windows included.

**More on the failure itself**

- [How to fix a corrupted video file on a Mac](docs/fix-corrupted-video-file-mac.md)
- [The power went and now the video will not play](docs/video-wont-play-after-power-loss.md)
- [What "moov atom not found" means and how to get the footage back](docs/moov-atom-not-found.md)
- [Repairing an MP4 without a reference file](docs/repair-mp4-without-reference-file.md)
- [Your GoPro file will not play](docs/gopro-video-wont-play.md)
- [Your DJI file will not play](docs/dji-video-wont-play.md)
- [Your Canon file will not play](docs/canon-video-wont-play.md)
- [Your iPhone video will not play](docs/iphone-video-wont-play.md)
- [Your Sony recording will not play](docs/sony-video-wont-play.md)
- [Your Pixel video will not play](docs/pixel-video-wont-play.md)
- [Your dashcam video will not play](docs/dashcam-video-wont-play.md)
- [OBS crashed and the recording will not play](docs/obs-recording-wont-play.md)
- [Getting untrunc working on a Mac](docs/untrunc-on-mac.md)
- [My test suite was lying to me](docs/my-tests-were-lying.md)

## Recovered from the damaged file alone

No reference clip, nothing but the recording that will not play. Every sample
exact against the intact original, the codec configuration reconstructed
byte-identical to the real one, and zero decode errors from ffmpeg reading the
result end to end.

| real camera | frames recovered | sound | exact | decode errors |
|---|---|---|---|---|
| GoPro HERO5 | 456 / 456 | 892 / 892 | 100% | 0 |
| GoPro HERO6 | 425 / 425 | 665 / 665 | 100% | 0 |
| GoPro HERO6, second take | 263 / 263 | 412 / 412 | 100% | 0 |
| GoPro HERO7 | 170 / 170 | 266 / 266 | 100% | 0 |
| GoPro HERO8 | 323 / 323 | 506 / 506 | 100% | 0 |
| GoPro Karma | 200 / 200 | 313 / 313 | 100% | 0 |
| GoPro Fusion | 160 / 160 | 251 / 251 | 100% | 0 |
| GoPro MAX | 175 / 175 | 274 / 274 | 100% | 0 |
| GoPro HERO6 with BLE | 181 / 181 | 354 / 354 | 100% | 0 |
| GoPro HERO12 Black, 1080p HEVC | 217 / 217 | 169 / 169 | 100% | 0 |
| GoPro HERO12 Black, second take | 186 / 186 | 145 / 145 | 100% | 0 |
| GoPro HERO11 Black, 4K 4:3 HEVC | 145 / 145 | 114 / 114 | 100% | 0 |
| GoPro HERO11 Black, second take | 205 / 205 | 161 / 161 | 100% | 0 |

An iPhone or an iPad gets there by a different route and keeps its sound. About ten seconds
into a take, iOS writes a complete index into the middle of the recording so that
a phone dropped mid-shot still has one; a take longer than that is recovered from
its own account of itself, with nothing searched for and nothing inferred.

| no reference clip | frames | audio | decode errors |
|---|---|---|---|
| iPhone 11, take cut at 50% | 397 / 397 | 560 / 560 | 0 |
| iPhone 11, take cut at 60% | 484 / 484 | 690 / 690 | 0 |
| iPhone 11, take cut at 85% | 692 / 692 | 991 / 991 | 0 |
| iPhone 11, second take, cut at 85% | 416 / 416 | 582 / 582 | 0 |
| iPad (5th generation), cut at 80% | 395 / 395 | 646 / 646 | 0 |

The index stops at the last checkpoint the phone managed to write, but it carries
what a reference clip would carry, the codec configuration, the sound description
and the way the phone lays its samples down, so the footage after that checkpoint
is read with it, exactly as a clip from the same phone would have it read. The
take cut at 60% gives 297 frames from the index and 187 more past it, 484 in all,
which is what a clip from the same phone recovers through the same bytes.
Cinesalve reports how many frames came from each.

## Recovered with a clip from the same camera

Recordings that state neither their own configuration nor an index are detected
automatically and read it from any working clip you have, which can be a few
seconds recorded on the spot. The last column says what each measurement here
was made against: a separate take from the same camera, which is the situation
you would actually be in, or the recording's own untruncated self where only
one camera-original clip of that model is published anywhere to pair with.

| real camera | frames recovered | exact | reference |
|---|---|---|---|
| Sony FDR-AX100E, 4K | 1488 / 1488 | 100% | its own full recording |
| Sony FDR-AX100E, cut at 85% | 2292 / 2292 | 100% | its own full recording |
| Sony ILCE-6300, 1080p | 354 / 354 | 100% | a different take |
| Sony ILCE-6300, cut at 85% | 508 / 508 | 100% | a different take |
| Sony ILCE-6100, 4K | 108 / 108 | 100% | a different take |
| Sony ILCE-6100, 4K, cut at 85% | 154 / 154 | 100% | a different take |
| Sony ILCE-7M3, 1080p | 322 / 322 | 100% | a different take |
| Sony ILCE-7M3, 1080p, cut at 85% | 456 / 456 | 100% | a different take |
| Sony DSC-RX10M4, 1080p | 200 / 200 | 100% | a different take |
| Sony DSC-RX10M4, 1080p, cut at 85% | 282 / 282 | 100% | a different take |
| Sony FDR-AX33, 1080p | 226 / 226 | 100% | a different take |
| Sony FDR-AX33, 1080p, cut at 85% | 317 / 317 | 100% | a different take |
| Sony FDR-AX43, 4K | 89 / 89 | 100% | a different take |
| Sony FDR-AX43, 4K, cut at 85% | 126 / 126 | 100% | a different take |
| Sony ILCE-6400, 4K | 117 / 117 | 100% | a different take |
| Sony ILCE-6400, 4K, cut at 85% | 168 / 168 | 100% | a different take |
| Fujifilm FinePix HS50EXR, 720p | 488 / 488 | 100% | a different take |
| Fujifilm FinePix HS50EXR, 720p, cut at 85% | 710 / 710 | 100% | a different take |
| Sony ILME-FX3, 4K | 136 / 136 | 100% | a different take |
| Sony ILME-FX3, 4K, cut at 50% | 197 / 197 | 100% | a different take |
| Sony FDR-AX100, 4K 30p | 190 / 190 | 100% | a different take |
| Sony FDR-AX100, 4K 30p, cut at 85% | 259 / 259 | 100% | a different take |
| Sony FDR-AX700, 4K 30p | 111 / 111 | 100% | a different take |
| Sony FDR-AX700, 4K 30p, cut at 85% | 159 / 159 | 100% | a different take |
| Google Pixel 7 Pro, 1080p | 728 / 728 | 100% | its own full recording |
| Google Pixel 7 Pro, cut at 85% | 1051 / 1051 | 100% | its own full recording |
| Google Pixel 9 Pro, 1080p | 210 / 210 | 100% | a different take |
| Google Pixel 9 Pro XL, 1080p | 90 / 90 | 100% | a different take |
| Google Pixel 8a, 1080p 60p | 143 / 143 | 100% | a different take |
| Google Pixel 6 Pro, 1080p | 64 / 64 | 100% | a different take |
| Google Pixel 5, 1080p | 79 / 79 | 100% | its own full recording |
| Motorola moto x4, 1080p | 336 / 336 | 100% | a different take |
| Motorola moto x4, cut at 85% | 480 / 480 | 100% | a different take |
| LG-M250H, 720p | 170 / 170 | 100% | a different take |
| LG-M250H, cut at 85% | 243 / 243 | 100% | a different take |
| Samsung Galaxy S23 Ultra, 1080p HEVC | 340 / 340 | 100% | a different take |
| Samsung Galaxy S23 Ultra, cut at 85% | 483 / 483 | 100% | a different take |
| Xiaomi 15T Pro, 4K HEVC | 389 / 389 | 100% | a different take |
| Xiaomi 15T Pro, cut at 85% | 551 / 551 | 100% | a different take |
| DJI Mavic 3 Pro, 4K | 722 / 722 | 100% | a different take |
| DJI Osmo Pocket, 1080p | 534 / 534 | 100% | a different take |
| DJI Osmo Pocket, cut at 85% | 755 / 755 | 100% | a different take |
| DJI Air 3S, 4K HEVC | 251 / 251 | 100% | a different take |
| DJI Air 3S, cut at 85% | 361 / 361 | 100% | a different take |
| DJI Osmo Pocket 3, 1080p HEVC | 367 / 367 | 100% | a different take |
| DJI Osmo Pocket 3, cut at 85% | 540 / 540 | 100% | a different take |
| DJI FC7503, 1080p | 252 / 252 | 100% | a different take |
| DJI FC7503, cut at 85% | 360 / 360 | 100% | a different take |
| DJI FC330, 1080p | 376 / 376 | 100% | a different take |
| DJI FC330, cut at 85% | 534 / 534 | 100% | a different take |
| iPhone 11, HEVC | 484 / 484 | 100% | a different take |
| iPhone 12 Pro Max, Dolby Vision HDR | 344 / 344 | 100% | a different take |
| iPhone 12 Pro Max, cut at 85% | 484 / 484 | 100% | a different take |
| iPhone 13 Pro Max, 4K60 HEVC | 537 / 537 | 100% | a different take |
| iPhone 13 Pro Max, cut at 85% | 768 / 768 | 100% | a different take |
| iPhone 14 Pro Max, 4K60 H.264 | 156 / 156 | 100% | a different take |
| iPhone 14 Pro Max, cut at 85% | 206 / 206 | 100% | a different take |
| iPad (5th generation), 1080p | 114 / 114 | 100% | a different take |
| iPad (5th generation), cut at 85% | 168 / 168 | 100% | a different take |
| Canon EOS 5D Mark II | 208 / 208 | 100% | a different take |
| Canon PowerShot SX30 IS | 391 / 391 | 100% | a different take |
| Canon PowerShot SX30 IS, cut at 85% | 558 / 558 | 100% | a different take |
| Canon PowerShot G7 X Mark II, 1080p25 | 252 / 252 | 100% | a different take |
| Canon PowerShot G7 X Mark II, cut at 85% | 360 / 360 | 100% | a different take |
| Canon EOS R10, 1080p | 525 / 525 | 100% | a different take |
| Canon EOS R10, cut at 85% | 749 / 749 | 100% | a different take |
| Canon EOS M200, 1080p | 81 / 81 | 100% | a different take |
| Canon EOS M200, cut at 85% | 117 / 117 | 100% | a different take |
| Canon EOS 90D, 1080p50 | 78 / 78 | 100% | a different take |
| Canon EOS 90D, cut at 85% | 114 / 114 | 100% | a different take |
| Canon EOS 200D, 1080p25 | 302 / 302 | 100% | a different take |
| Canon EOS 200D, cut at 85% | 432 / 432 | 100% | a different take |
| Canon EOS Rebel T7i, 1080p | 204 / 204 | 100% | a different take |
| Canon EOS Rebel T7i, cut at 85% | 288 / 288 | 100% | a different take |
| Canon EOS 6D Mark II, 1080p | 419 / 419 | 100% | a different take |
| Canon EOS 6D Mark II, cut at 85% | 593 / 593 | 100% | a different take |
| Canon EOS 80D, 1080p | 215 / 215 | 100% | a different take |
| Canon EOS 80D, cut at 85% | 306 / 306 | 100% | a different take |
| Canon, 720p, mono sound | 266 / 266 | 100% | a different take |
| Canon, 720p, mono sound, cut at 85% | 372 / 372 | 100% | a different take |
| SANYO Xacti WH1 | 837 / 837 | 100% | a different take |
| SANYO Xacti WH1, cut at 85% | 1195 / 1195 | 100% | a different take |
| SANYO Xacti SH1, interlaced | 731 / 731 | 100% | a different take |
| SANYO Xacti SH1, cut at 85% | 1056 / 1056 | 100% | a different take |
| SANYO Xacti FH1, 1080p60 | 1566 / 1566 | 100% | a different take |
| SANYO Xacti FH1, cut at 85% | 2226 / 2226 | 100% | a different take |
| Casio EX-S10 | 842 / 842 | 100% | a different take |
| Casio EX-S10, cut at 85% | 1166 / 1166 | 100% | a different take |
| Fujifilm FinePix XP70 | 3494 / 3494 | 100% | a different take |
| Fujifilm FinePix XP70, cut at 85% | 4951 / 4951 | 100% | a different take |
| Fujifilm X-T20, 1080p59.94 | 381 / 381 | 100% | a different take |
| Fujifilm X-T20, cut at 85% | 554 / 554 | 100% | a different take |
| Fujifilm FinePix S8350, 1080p59.94 | 558 / 558 | 100% | a different take |
| Fujifilm FinePix S8350, cut at 85% | 808 / 808 | 100% | a different take |
| Panasonic DC-GH5, 4K | 111 / 111 | 100% | a different take |
| Panasonic Lumix DMC-TZ60 | 3274 / 3274 | 100% | a different take |
| Panasonic Lumix DMC-TZ60, cut at 85% | 4572 / 4572 | 100% | a different take |
| Panasonic Lumix DMC-SZ1 | 570 / 570 | 100% | a different take |
| Panasonic Lumix DMC-SZ1, cut at 85% | 829 / 829 | 100% | a different take |
| Nikon COOLPIX P300, 1080p | 840 / 840 | 100% | a different take |
| Nikon COOLPIX P300, cut at 85% | 1187 / 1187 | 100% | a different take |
| Nikon D3100, 1080p | 282 / 282 | 100% | a different take |
| Nikon D3100, cut at 85% | 408 / 408 | 100% | a different take |
| Nikon 1 J3, interlaced | 444 / 444 | 100% | a different take |
| Nikon 1 J3, cut at 85% | 630 / 630 | 100% | a different take |
| Nikon Z6III, 4K HEVC | 46 / 46 | 100% | a different take |
| Nikon Z6III, 4K HEVC, cut at 85% | 62 / 62 | 100% | a different take |
| Nikon Z 50, 4K | 125 / 125 | 100% | a different take |
| Nikon Z 50, 4K, cut at 85% | 184 / 184 | 100% | a different take |
| Pentax X-5, 1080p | 120 / 120 | 100% | a different take |
| Pentax X-5, cut at 85% | 179 / 179 | 100% | a different take |
| Olympus, 1080p | 399 / 399 | 100% | a different take |
| Olympus, cut at 85% | 569 / 569 | 100% | a different take |
| Ricoh WG-80, 1080p | 817 / 817 | 100% | a different take |
| Ricoh WG-80, cut at 85% | 1168 / 1168 | 100% | a different take |
| Samsung SMX-F70 camcorder, 720x576 | 14380 / 14380 | 100% | a different take |
| Samsung SMX-F70, cut at 85% | 20313 / 20313 | 100% | a different take |
| YI action camera, 4K | 408 / 408 | 100% | a different take |
| YI action camera, cut at 85% | 578 / 578 | 100% | a different take |
| iCatch action camera, 1080p60 | 417 / 417 | 100% | a different take |
| iCatch action camera, cut at 85% | 592 / 592 | 100% | a different take |
| Novatek dashcam, 1080p60 | 3125 / 3125 | 100% | a different take |
| Novatek dashcam, cut at 85% | 4429 / 4429 | 100% | a different take |
| BlackVue DR770X dashcam, 1080p60 | 454 / 454 | 100% | a different take |
| BlackVue DR770X dashcam, cut at 85% | 648 / 648 | 100% | a different take |
| QuickTime, Apple's own muxer | 420 / 420 | 100% | a different take |
| 1080p consumer camera | 1356 / 1356 | 100% | a different take |
| 1080p camera that names no maker, mono sound | 227 / 227 | 100% | a different take |
| 1080p camera that names no maker, cut at 85% | 325 / 325 | 100% | a different take |
| GoPro HERO6, cut at 85% | 603 / 603 | 100% | a different take |

## Synthetic suite

Cut at known points so the original is available to compare against, frame by
frame.

| test case | video frames exact | audio frames exact |
|---|---|---|
| H.264 720p 30fps, cut at 68% | 203 / 203 (100%) | 100% |
| H.264 720p 30fps, cut at 90% | 269 / 269 (100%) | 100% |
| H.264 1080p 24fps, cut at 55% | 107 / 107 (100%) | 100% |
| H.264 480p 60fps, cut at 45% | 217 / 217 (100%) | 100% |
| H.264 without audio, cut at 60% | 144 / 144 (100%) | — |
| HEVC 720p 30fps, cut at 60% | 146 / 146 (100%) | 100% |

## What it recovers

Picture, exactly, on H.264 and HEVC in MP4 and MOV — what consumer and prosumer
cameras record.

Sound comes back on every camera in the suite that recorded any. On a GoPro, a
Canon EOS, a Canon PowerShot, a Casio EX-S10, a Fujifilm FinePix, a Fujifilm X-T20, a Nikon COOLPIX, a Nikon D3100, a Nikon 1 J3, a Nikon Z6III, a Panasonic DC-GH5, a Panasonic Lumix TZ60, a Panasonic Lumix SZ1, a Pentax X-5, a Ricoh WG-80, a Samsung SMX-F70, an Olympus, a YI action camera, an iCatch action camera, a SANYO Xacti, a Sony camcorder, a Sony a6300, a Sony a6100, a Sony a7 III, a Sony FX3, a Sony FDR-AX700, a Sony RX10 IV, a Sony FDR-AX33, a Sony FDR-AX43, a Sony a6400, a consumer camcorder, a recording written by
Apple's own muxer, an iPhone, a Google Pixel, a Motorola moto x4, an LG phone, a Samsung Galaxy S23 Ultra, a Xiaomi 15T Pro, a DJI Osmo Pocket, a DJI Osmo Pocket 3, a dashcam on a Novatek chip and a BlackVue dashcam it comes back whole, every frame
at the position the camera wrote it: a GoPro and a BlackVue name the size of every sample in
the bytes ahead of it, the Canon MOV cameras, the Fujifilms, the iCatch camera, the Olympus, the GH5, the Pentax, the Ricoh, the Nikon D3100, the Nikon Z6III and the Sony write uncompressed sound that is
placed from the pictures by arithmetic rather than searched for, and the
Canon MP4 cameras', the camcorders', the Apple-muxed recording's, both Lumix compacts', the COOLPIX's, the Nikon 1's, the YI's, the Xacti's, the Casio's, the iPhone's, the moto x4's, the LG's, the Galaxy's, the Xiaomi's, both Osmo Pockets', the Novatek dashcam's and the Pixel's
compressed sound is read frame by frame, with the system's own decoder saying
where each frame ends. The metadata
an iPhone writes behind each block of sound, and the recovery index it keeps
inside its own recording, each name their own length, so they are stepped over
to the next picture rather than read as sound. On a camera it has not measured,
where bytes between pictures cannot be attributed to any track, Cinesalve
recovers the picture and says it left the sound out rather than writing bytes
it cannot verify into your recording.

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
