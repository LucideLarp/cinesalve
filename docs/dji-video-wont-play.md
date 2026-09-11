# Your DJI file will not play

The battery ran out mid-flight, you hit the power button, or the drone came down
harder than planned. The clip is on the card and nothing will open it. Here is
what to try, in the order that costs you least, starting with what DJI can do
itself.

## Before anything else

**Do not record anything else on that card.** The one exception is step one
below, which is done deliberately and in the drone. Otherwise take the card out
and copy everything off it first. Nothing recovers footage that has been written
over.

## 1. Power cycle with the card still in the drone

This is the one that most often works, it is free, and it takes a minute. Put
the original card back in the aircraft or camera that shot it, power on, wait
**about thirty seconds**, then start a recording and stop it again. The camera
notices the recording it never finished closing and finalises it.

Do this before copying files around, and before trying any software. It is far
more likely to succeed while the card is still exactly as the drone left it.

## 2. Look for the LRF file next to the clip

DJI writes a `.LRF` alongside each recording: a low resolution proxy of the same
footage, used for playback in the app. It is written separately from the main
clip, so it is often perfectly intact when the big file is not.

Copy the `.LRF` with the matching file number and rename the copy to `.MP4`. If
it plays, you have your flight. It is not the resolution you shot, but it proves
the footage exists, and for a moment you thought was lost it is frequently
enough.

## 3. Work out whether the footage is still in the big file

If the clip is a sensible size rather than zero bytes, the video is almost
certainly still inside. What a camera writes *last*, when recording stops
cleanly, is the index saying where every frame lives. Lose power first and you
have a complete recording with no map, which every player refuses to touch.

On a Mac, this tells you which case you are in:

```
ls -l DJI_0042.MP4
strings -t d DJI_0042.MP4 | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is
there and only the index is missing. [The detail of what that
means](moov-atom-not-found.md). A file of a few kilobytes means the footage was
never written, and card level recovery software is the only remaining route.

## 4. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file for its verdict, then up to
100.7 MB of the footage to tell you how many frames are recoverable, and a
reference clip in full if you add one. Nothing is uploaded, and it runs on any
computer including Windows
even though the app itself is Mac only. It counts frames and never assembles a
playable file.

## 5. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed by
reading the raw footage and working out where each frame begins, using a healthy
clip from the same drone to learn how that camera writes.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
exactly this, if you are willing to compile a command line tool and hand it a
reference file. Try it before paying anybody. If it will not build on your Mac,
[these are the errors and what they mean](untrunc-on-mac.md).

**No other clip from that camera?** Record one now. A few seconds at the same
settings is enough. The damaged file no longer states its own codec settings, so
they have to be read from a working recording, which is why every tool that can
do this asks for one.

## Cinesalve

A Mac app that does the same job. Drop in the clip that will not open plus any
clip from the same drone that does, and it rebuilds the index so the file plays
again. Nothing is uploaded; it runs entirely on your Mac. **$69 once.**

It is measured against a real DJI Mavic 3 Pro recording, 4K at 59.94fps,
straight off the drone. Every recovered frame is compared against the original
by byte position and size:

| Real DJI footage | Frames recovered | Exact |
|---|---|---|
| Mavic 3 Pro, 4K 59.94fps, cut mid-flight | 722 / 722 | 100% |

That recording has no audio track, so nothing is claimed about DJI sound yet. It
also carries two DJI data tracks interleaved between the pictures, which is
exactly what defeated the first attempt: those blocks produce byte patterns that
read as frames. Other DJI models have not been through the suite, and a clip
from one is genuinely welcome.

## Send it over

A damaged DJI clip is genuinely the most useful thing anyone could send right
now: it goes into the test suite and the fix ships for everyone.
[cinesalve@proton.me](mailto:cinesalve@proton.me), with the drone model and
recording mode. You will get an honest answer about whether it can be recovered,
including when the answer is no, and there is no pitch attached.
