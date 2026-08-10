# Your Canon file will not play

The battery died, the card came out, or the body locked up while it was still
recording. There is an `MVI_1234.MOV` on the card of a believable size and
nothing will open it. Here is what to try, in the order that costs you least.

## Before anything else

**Stop writing to that card.** Take it out and copy everything off it before you
try anything, this page included. Nothing recovers footage that has been written
over.

**Then work on a copy, never the original.** Some repair tools rewrite the file
where it sits, so a failed attempt can leave you worse off than the power cut
did. Keep the untouched original somewhere else and point every tool at a
duplicate.

## 1. Check the rest of the take first

Canon bodies recording to a FAT32 card cannot write a single file past **4 GB**,
so a long take is not one file. The camera closes each part and immediately opens
the next, which means every part except the one being written when the power went
is a complete, properly finished recording.

Look at the whole run of files around the broken one, in order. If your take was
long enough to split, most of it is likely to play right now with nothing done to
it, and only the final part is damaged. That is the cheapest win available and it
is often the whole job.

## 2. Work out whether the footage is still in the file

If the broken file is a sensible size rather than a few kilobytes, the video is
almost certainly still inside it. What a camera writes *last*, when a recording
stops cleanly, is the index saying where every frame lives. Lose power before
that and you have a complete recording with no map, which every player refuses to
touch.

On a Mac, this tells you which case you are in:

```
ls -l MVI_1345.MOV
strings -t d MVI_1345.MOV | grep -m 5 -E 'ftyp|mdat|moov'
```

`ftyp` and `mdat` present but no `moov` is the good outcome: the footage is there
and only the index is missing. [What that means in
detail](moov-atom-not-found.md). A file of a few kilobytes means the footage was
never written, and card level recovery software is the only remaining route.

## 3. Check whether it is recoverable, free

The browser checker at
[cinesalve.honorboxx.workers.dev/check](https://cinesalve.honorboxx.workers.dev/check)
reads the first and last megabyte of the file and tells you how many frames are
recoverable. Nothing is uploaded, and it runs on any computer including Windows.
It counts frames and never assembles a playable file.

## 4. Rebuild the index

The index cannot be copied from another clip. It holds the exact byte position
and size of every frame in *your* recording, so it has to be reconstructed by
reading the raw footage and working out where each frame begins, using a healthy
clip from the same camera to learn how that body writes.

[untrunc](https://github.com/anthwlock/untrunc) is free and open source and does
exactly this, if you are willing to compile a command line tool and hand it a
reference file. Try it before paying anybody. If it will not build on your Mac,
[these are the errors and what they mean](untrunc-on-mac.md).

**No other clip from that camera?** Record one now, before the card goes
anywhere. A few seconds in the same mode is enough. A damaged Canon file does not
state its own codec settings, so they have to be read from a working recording,
which is why every tool that can do this asks for one.

## Cinesalve

A Mac app that does the same job. Drop in the file that will not open plus any
clip from the same camera that does, and it rebuilds the index so the file plays
again. Nothing is uploaded; it runs entirely on your Mac. **$69 once.**

Measured against a real Canon EOS 5D Mark II recording, 1080p at 29.97fps,
camera original and cut mid-take. Every recovered frame is compared against the
original by byte position and size:

| Real Canon footage | Frames recovered | Exact |
|---|---|---|
| EOS 5D Mark II, 1080p 29.97fps, cut mid-take | 208 / 208 | 100% |

Every frame lands at the exact offset and size the camera wrote, and the repaired
file decodes end to end without a single error. This camera records
*uncompressed* sound rather than the compressed kind, interleaved between the
pictures, and the picture is what is reassembled today, so the recovered file is
silent. Other Canon bodies have not been through the suite, and a clip from one
is genuinely welcome.

## If none of it works

Send the clip, or just describe it, to
[cinesalve@proton.me](mailto:cinesalve@proton.me) with the body and the recording
mode. You will get a straight answer about whether it can be recovered, including
when the answer is no.
