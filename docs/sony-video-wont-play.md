# Your Sony recording will not play

The battery went mid-take, the card came out, or the camcorder now says the image
database file is damaged and shows you nothing at all. Those are two different
problems with two different answers. Here is what to try, in the order that costs
you least.

## Before anything else

**Stop writing to that card.** Take it out and copy everything off it before you
try anything, this page included. Nothing recovers footage that has been written
over.

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the power cut
did. Keep the untouched original somewhere else and point every tool at a
duplicate.

## 1. If the camera reports a damaged image database, your footage is probably intact

A Sony camcorder keeps a catalogue of what is on the card, stored separately from
the recordings themselves. When that catalogue is damaged the camera stops listing
your footage and offers to build a fresh one, which looks exactly like losing
everything. It usually is not. Sony's own guide to that warning says of the
recordings themselves: *Image files are not damaged.*

**Copy the card before you answer the prompt.** The message reads *The Image
Database File is damaged. Do you want to create a new file?*, and creating new
information is the step that leaves the earlier recordings unreachable from the
camera. Sony's guide is explicit that once new information exists, the old
recorded images on the media cannot be played back. Pull the card, copy it whole,
and only then let the camera tidy itself up.

The repair is a menu item rather than a service job. On a Handycam it is
**[Setup] > [Media Settings] > [Repair Img. DB F.]**, then choose the recording
medium. Sony documents it for the [FDR-AX100, FDR-AX100E, HDR-CX900 and
HDR-CX900E](https://helpguide.sony.net/gbmig/45346551/v1/en/contents/TP0000429308.html)
and for the [Handycam range
generally](https://helpguide.sony.net/gbmig/44475101/v1/gb/contents/09/03/02/02.html).
It is also the fix when clips you copied back onto a card refuse to appear in
Event View.

## 2. The clips are not at the top of the card

Sony does not leave recordings where you would look for them, so a card that
appears empty in the Finder usually is not. Which folder depends on the format
you shot:

- **XAVC S** recordings sit in `PRIVATE > M4ROOT > CLIP` and are ordinary `.MP4`
  files, named in sequence from `C0001.MP4`.
- **AVCHD** recordings sit in `PRIVATE > AVCHD > BDMV > STREAM` as `.MTS` files.

Copy the whole `PRIVATE` folder rather than picking files out of it. The material
around the clips is what a camera and an editor use to make sense of them, and it
costs nothing to keep.

## 3. Work out whether the footage is still in the file

If the file that will not open is a sensible size rather than a few kilobytes, the
video is almost certainly still inside it. What a camera writes *last*, when a
recording stops cleanly, is the index saying where every frame lives. Lose power
before that and you have a complete recording with no map, which every player
refuses to touch.

On a Mac, this tells you which case you are in:

```
ls -l C0001.MP4
strings -t d C0001.MP4 | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is there
and only the index is missing. [What that means in
detail](moov-atom-not-found.md). A file of a few kilobytes means the footage was
never written, and card level recovery software is the only remaining route.

## 4. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file and tells you how many frames are
recoverable. Nothing is uploaded, and it runs on any computer including Windows.
It counts frames and never assembles a playable file.

## 5. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed by
reading the raw footage and working out where each frame begins, using a healthy
clip from the same camera to learn how that body writes.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
exactly this, if you are willing to compile a command line tool and hand it a
reference file. Try it before paying anybody. If it will not build on your Mac,
[these are the errors and what they mean](untrunc-on-mac.md).

**No other clip from that camera?** Record one now, before the card goes
anywhere. A few seconds in the same mode and the same resolution is enough. A
damaged Sony file does not state its own codec settings: XAVC S keeps them in the
header the camera never got to write, so they have to be read from a working
recording, which is why every tool that can do this asks for one.

## Cinesalve

A Mac app that does the same job. Drop in the file that will not open plus any
clip from the same camera that does, and it rebuilds the index so the file plays
again. Nothing is uploaded; it runs entirely on your Mac. **$69 once.**

Measured against a real Sony FDR-AX100E recording, 4K at 25fps, camera original
and cut mid-take. Every recovered frame is compared against the original by byte
position and size:

| Real Sony footage | Frames recovered | Exact | Reference |
|---|---|---|---|
| Sony FDR-AX100E, 2160p 25fps, cut mid-take | 1488 / 1488 | 100% | its own full recording |

Every frame lands at the exact offset and size the camera wrote. That last column
is there because one camera original AX100 clip is published anywhere to test
against, so the measurement pairs the damaged file with its own untruncated self
rather than with a separate take. This camera records *uncompressed* sound rather
than the compressed kind, interleaved between the pictures alongside a timecode
track, and the picture is what is reassembled today, so the recovered file is
silent. Other Sony bodies have not been through the suite, and a clip from one is
genuinely welcome.

## If none of it works

Send the clip, or just describe it, to
[cinesalve@proton.me](mailto:cinesalve@proton.me) with the body and the recording
mode. You will get a straight answer about whether it can be recovered, including
when the answer is no.
