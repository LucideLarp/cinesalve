# Your iPhone video will not play

The phone went flat, the Camera app quit, or it overheated and stopped while it
was still recording. There is a video in Photos with a thumbnail that will not
open, or an `IMG_1234.MOV` you copied off that nothing will touch. Here is what
to try, in the order that costs you least.

## Before anything else

**Do not record anything else on that phone yet**, and do not delete the video
from Photos to "clear space".

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the interruption
did. Keep the untouched original somewhere else and point every tool at a
duplicate.

## 1. Make sure you actually have the recording

This step has no equivalent on any other camera, and it is where an iPhone owner
most often spends their one good chance. Pull the card out of a GoPro or a Canon
and the file is simply there. An iPhone keeps the recording inside the Photos
library, and what you end up holding depends entirely on how you take it out.

- **AirDrop, Messages and Share can hand you a converted copy.** A re-encode of a
  damaged recording is a damaged recording with the evidence thrown away, and
  nothing recovers what a transcode discarded.
- **iCloud Photos may be holding the real one.** With *Optimise iPhone Storage*
  switched on, the copy on the device can be a stand-in while the full recording
  sits in iCloud. In Settings > Photos, choose *Download and Keep Originals* and
  let it finish before you copy anything off.
- **On a Mac, take it off as recorded.** Image Capture copies files straight from
  the device, and in Photos the export option for the unmodified original gives
  you the file the phone actually wrote.

An iPhone recording names the phone inside itself, so you can tell an original
from a copy without opening anything:

```
strings IMG_1234.MOV | grep -m 2 iPhone
```

On the recordings tested here that prints the phone and the lens it used, along
the lines of `iPhone 11 back camera 4.25mm f1.8`. If nothing comes back, you are
either holding a converted copy or the take was cut very early; the free check
below tells those two apart, so run it before assuming the worst.

## 2. Work out whether the footage is still in the file

If the file is a sensible size rather than a few kilobytes, the video is almost
certainly still inside it. What a phone writes *last*, when a recording stops
cleanly, is the index saying where every frame lives. Stop before that and you
have a complete recording with no map, which every player refuses to touch. That
is why the thumbnail in Photos can look right while nothing will play it.

On a Mac, this tells you which case you are in:

```
ls -l IMG_1234.MOV
strings -t d IMG_1234.MOV | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is there
and only the index is missing. [What that means in
detail](moov-atom-not-found.md). A file of a few kilobytes means the footage was
never written, and device level recovery software is the only remaining route.

## 3. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file and tells you how many frames are
recoverable. Nothing is uploaded, and it runs on any computer including Windows.
It counts frames and never assembles a playable file.

## 4. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed from
the raw footage itself.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
this job, if you are willing to compile a command line tool. Try it before paying
anybody. If it will not build on your Mac, [these are the errors and what they
mean](untrunc-on-mac.md).

## Cinesalve

A Mac app that does the same job. Drop in the file that will not open and it
rebuilds the index so the file plays again. Nothing is uploaded; it runs entirely
on your Mac. **$69 once.**

**An iPhone needs no second clip.** A phone cannot know it is about to be
dropped, so about ten seconds into a take iOS lays a complete index into the
middle of the recording and keeps extending it as the take goes on. A take that
ends normally writes its real index at the end. A take that ends in a flat
battery leaves that one behind, and it is the camera's own account of what it
wrote and where, so there is nothing to search for and nothing to guess at.

It is measured against real iPhone 11 recordings, HEVC 1080p, camera original and
cut mid-take. Every recovered frame and every recovered audio packet is compared
against the original by byte position and size:

| Real iPhone footage, no reference clip | Frames | Sound | Decode errors |
| --- | --- | --- | --- |
| 1080p 120fps, cut at 50% | 297 / 297 | 431 / 431 | 0 |
| 1080p 120fps, cut at 60% | 297 / 297 | 431 / 431 | 0 |
| 1080p 120fps, cut at 85% | 596 / 596 | 862 / 862 | 0 |
| 1080p 29.97fps, cut at 85% | 300 / 300 | 431 / 431 | 0 |

Picture and sound both, every frame and every audio packet at the exact offset
and size the phone wrote, and the repaired file decodes end to end without a
single error. The index describes the take as of its last checkpoint, and the
first one falls about ten seconds into a recording, so a take shorter than that
has none to read: the app says so and asks for a reference clip rather than
guessing. It also tells you how much footage lies past the final checkpoint.

## Send it over

A damaged iPhone clip is genuinely useful: it goes into the test suite and the
fix ships for everyone. [cinesalve@proton.me](mailto:cinesalve@proton.me) (or
[the contact form](https://cinesalve-licence.honorboxx.workers.dev/contact)), with
the phone and the recording mode. You will get an honest answer about whether it
can be recovered, including when the answer is no, and there is no pitch
attached.
