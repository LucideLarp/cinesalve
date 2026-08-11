# The power went and now the video will not play

The battery died, the drone came down, the mains went, or the card came out
while the light was still on. Whether the footage comes back is decided by what
the device had finished writing at that instant — and you can find out which
case you are in without paying anyone.

## Before anything else

**Stop writing to that card.** Take it out of the camera and copy everything off
it. A new recording can land on top of the data you are trying to get back, and
nothing recovers what has been overwritten.

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the power cut
did. Keep the untouched original somewhere else and point every tool at a
duplicate. That way you get unlimited attempts.

## Why a power cut breaks a file that is otherwise complete

A camera writes your recording to the card continuously while it runs. What it
does *not* write until you press stop is the index: the table listing the exact
byte position and size of every frame. In an MP4 or MOV that table is the `moov`
atom, and it comes last because the camera cannot know what goes in it until the
recording is over.

Lose power before that and you have the whole recording and no map of it. The
file is the size you would expect, every frame is sitting inside it, and every
player refuses it because none of them will guess where the frames are.

More detail: [what "moov atom not found" means](moov-atom-not-found.md).

## Three outcomes, and yours is already decided

It was settled at the instant the power went. They need different tools, so it
is worth a minute to find out rather than working through fixes for the other
two.

### 1. Normal size, nothing opens it

The recoverable case, and much the most common after a cut during recording. The
picture data is on the card and only the index is missing. An index can be
rebuilt by reading the recording and working out where each frame begins.

### 2. Zero bytes, or a few kilobytes

The camera never flushed the recording to the card. There is no footage inside
the file to find, and no amount of index rebuilding invents it.

This is the honest answer to *my video file is 0 bytes*: rebuilding tools cannot
help, and anything claiming otherwise is selling you a scan of an empty file.
Your remaining route is card-level recovery, which ignores the filesystem and
searches the raw card for data written before the directory entry was updated.
[PhotoRec](https://www.cgsecurity.org/wiki/PhotoRec) is free, open source and is
the standard tool for it. Run it against the card, not against the empty file.

### 3. The card will not mount, or the files look scrambled

The power loss damaged the filesystem rather than one recording. Repair or image
the card first. Rebuilding an index on a file read through a broken filesystem
gives you nothing useful, because the bytes you are handed are not reliably the
bytes the camera wrote.

## Telling them apart

```sh
ls -l GH011234.MP4
strings -t d GH011234.MP4 | grep -m 5 -E 'ftyp|mdat|moov'
```

A size in the hundreds of megabytes or gigabytes, with `ftyp` and `mdat` present
but no `moov`, is case 1: the footage is there and only the index is missing.

You can also check it in a browser, without installing anything, at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check).
It reads only the first and last megabyte, nothing is uploaded, and it reports
how many frames are recoverable. It runs on any computer, Windows included.

## The free things to try, in order

**Let the camera try.** Many cameras carry their own repair pass for a recording
they never closed; GoPro calls it SOS, and it triggers by putting the original
card back in the camera that shot it and powering on. See
[your GoPro file will not play](gopro-video-wont-play.md).

**Look for a proxy.** GoPro writes an `.LRV` low-resolution copy alongside every
recording, written separately, so it is often intact when the main file is not.
Rename a copy to `.MP4`. Not what you shot, but it confirms the recording
happened.

**Then rebuild the index.** [untrunc](https://github.com/anthwlock/untrunc) is
free, open source and does this well if you are comfortable compiling a command
line tool and giving it a healthy reference clip from the same camera. Try it
before paying anyone; if it will not build,
[these are the errors](untrunc-on-mac.md).

## Cinesalve

A Mac app that rebuilds the index. An interrupted recording is the case it is
built for: the test suite takes footage straight off real cameras, cuts each
file short the way a power cut does, then checks every recovered frame against
the original by byte position and size.

| Real camera footage, cut short | Frames recovered | Exact |
| --- | --- | --- |
| GoPro HERO6, separate take as the reference | 425 / 425 | 100% |
| GoPro HERO5 | 456 / 456 | 100% |
| GoPro HEVC, as the newer cameras record | 217 / 217 | 100% |
| DJI Mavic 3 Pro, 4K | 722 / 722 | 100% |
| DJI drone, another clip from the same flight as the reference | 116 / 116 | 100% |
| iPhone 11, HEVC | 484 / 484 | 100% |
| Canon EOS 5D Mark II, a DSLR | 208 / 208 | 100% |
| QuickTime, Apple's own muxer | 420 / 420 | 100% |

GoPro recordings carry their own codec configuration, in H.264 and HEVC alike,
so for those you hand it the file that will not open and nothing else. The other
makes keep that configuration in the part of the file the power cut took with
it, so they need a healthy clip from the same camera, which the app asks for
rather than guessing.

On the Apple-muxed recordings the sound comes back too, roughly nine frames in
ten landing correctly. Action cameras and drones interleave timecode and
telemetry into the same block as the picture, and those bytes cannot be told
from audio confidently enough to place, so on those the picture is returned and
the sound is left out rather than writing bytes that cannot be verified into
your recording. Picture is exact either way.

Nothing is uploaded and the recovery runs offline. $69 once.

## If none of it works

Send the clip, or just describe it, to
[cinesalve@proton.me](mailto:cinesalve@proton.me) with the camera model and
roughly what happened. You will get a straight answer about whether it can be
recovered, including when the answer is no.
