# "moov atom not found": what it means and how to get the footage back

Your camera stopped mid-recording. The file is on the card, it is the right size,
and nothing will open it. Players report `moov atom not found`, or `Invalid data
found when processing input`, or just fail silently.

**The footage is almost certainly still there.**

## What actually happened

A recording is written to the card as it happens. The index describing where
every frame lives — the `moov` atom — is written **last**, when you press stop.

Lose power before that and you have every frame on the card and no index. No
player will touch the file, because without the index it has no idea where a
frame starts or how long it lasts.

So the file is not corrupt in the usual sense. It is complete and unlabelled.

## Check it in one command

```sh
# lists the top-level atoms; a healthy file has ftyp, moov and mdat
ffprobe -v error -show_entries format=format_name -of csv broken.mp4
```

Or read the structure directly:

```sh
xxd -l 64 broken.mp4
```

If you see `ftyp` and `mdat` but no `moov` anywhere, that is this failure exactly.

There is also a free checker that reads the file in your browser and tells you
how many frames are recoverable, with nothing uploaded and no install:
**https://cinesalve.honorboxx.workers.dev/check**

## Why copying the moov atom from another file does not work

This is the advice you will find most often, and it is why people conclude the
material is gone.

The `moov` atom is not a header you can transplant. For **every single frame** it
records:

- the exact byte offset where that frame starts
- the exact size of that frame in bytes
- how long it is displayed
- whether it is a keyframe you can seek to

A ten-minute clip at 30fps has 18,000 video frames and roughly 26,000 audio
frames, and every one of those numbers is specific to *your* recording. Another
file's index describes another file's bytes.

What can legitimately be copied is the **codec configuration** — how to decode the
frames once you have found them. That is why most tools ask for a healthy clip
from the same camera.

## Do this first, before anything else

**Stop writing to the card.** Every new recording risks landing on top of the one
you are trying to get back. Copy the whole card to a drive and work from the copy.

## Rebuilding the index

The frames can be found again by walking the media data and identifying where
each one starts, then writing a new index that describes what was found.

- [untrunc](https://github.com/anthwlock/untrunc) is free and does this. It is a
  compile-it-yourself tool, which is where most people stop.
- [Cinesalve](https://github.com/LucideLarp/cinesalve) is a Mac app that does it
  with no compiler. On GoPro recordings it needs nothing but the damaged file.

---

*From the [Cinesalve](https://cinesalve.honorboxx.workers.dev) field notes.
Full version: https://cinesalve.honorboxx.workers.dev/moov-atom-not-found*
